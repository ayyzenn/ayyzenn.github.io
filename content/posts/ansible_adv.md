---
title: "Ansible: Docker and Kubernetes Node Setup"
date: 2026-01-17
tags: ["ansible", "docker", "kubernetes", "devops", "automation"]
---
## Why This Post Exists

After writing my **first real Ansible playbook**, I realized something:

> Installing software manually once is easy.
> Installing it **correctly, repeatedly, and safely** is not.

So I decided to automate something **real** and **useful**:

- Install **Docker Engine (official way)**
- Prepare an Ubuntu server to become a **Kubernetes node**
- Do everything using **Ansible**
- No SSH-ing into the server again and again

This post documents that journey.

---

## Real Setup (Same as Before)

Nothing fancy. Just real machines.

| Role         | OS                    |
| ------------ | --------------------- |
| Control Node | Arch Linux (i3 WM)    |
| Managed Node | Ubuntu 22.04          |
| Tool         | Ansible               |
| Goal         | Kubernetes-ready node |

Yes, Arch → Ubuntu automation works perfectly fine.

---

## Project Structure (Clean & Grown-Up)

Once things get bigger, **single playbooks get messy**.
So I moved to **roles**.

```text
ansible-k8s-bootstrap/
├── inventory.ini
├── playbook.yml
└── roles/
    ├── docker/
    │   ├── tasks/main.yml
    │   ├── handlers/main.yml
    │   └── vars/main.yml
    └── k8s_node/
        ├── tasks/main.yml
        ├── handlers/main.yml
        └── vars/main.yml
```

Think of roles as:

> "One role = one responsibility"

---

## Inventory (Nothing New Here)

```ini
[ubuntu_servers]
192.168.1.80 ansible_user=ubuntu ansible_python_interpreter=/usr/bin/python3
```

Same server, same rules.

---

## Phase 1: Installing Docker (The Right Way)
### Why Not `apt install docker.io`?

Because Kubernetes is picky.

Ubuntu's default Docker package:
- Is often outdated
- Can break container runtimes
- Causes weird kubelet issues later

So we install Docker from Docker's official repository.

### Docker Variables (No Hardcoding)

`roles/docker/vars/main.yml`:

```yaml
docker_apt_packages:
  - ca-certificates
  - curl
  - gnupg
  - lsb-release

docker_packages:
  - docker-ce
  - docker-ce-cli
  - containerd.io
  - docker-buildx-plugin
  - docker-compose-plugin

docker_gpg_key_path: /etc/apt/keyrings/docker.gpg
```

Why variables?

Because future-you will thank you.

### Docker Tasks (What Ansible Actually Does)

`roles/docker/tasks/main.yml`:

```yaml
- name: Install prerequisite packages
  apt:
    name: "{{ docker_apt_packages }}"
    state: present
    update_cache: yes

- name: Create keyrings directory
  file:
    path: /etc/apt/keyrings
    state: directory

- name: Add Docker GPG key
  get_url:
    url: https://download.docker.com/linux/ubuntu/gpg
    dest: "{{ docker_gpg_key_path }}"

- name: Add Docker repository
  apt_repository:
    repo: >
      deb [arch=amd64 signed-by={{ docker_gpg_key_path }}]
      https://download.docker.com/linux/ubuntu
      {{ ansible_lsb.codename }} stable
    state: present

- name: Install Docker Engine
  apt:
    name: "{{ docker_packages }}"
    state: present
    update_cache: yes
  notify: restart docker

- name: Enable Docker
  service:
    name: docker
    enabled: yes
    state: started

- name: Add user to docker group
  user:
    name: "{{ ansible_user }}"
    groups: docker
    append: yes
```

### First Taste of Handlers (Important Concept)

`roles/docker/handlers/main.yml`:

```yaml
- name: restart docker
  service:
    name: docker
    state: restarted
```

Why handlers matter:
- Docker restarts only if something changes
- No unnecessary restarts
- Clean automation

---

## Phase 2: Kubernetes Node Bootstrap
At this point Docker works.
But Kubernetes still won't.

Why?

Because Kubernetes has rules.

### Things Kubernetes Demands

- Swap must be disabled
- The cgroup driver must match what kubelet expects
- Required kernel networking modules must be loaded
- containerd must use the systemd cgroup driver
- kubelet, kubeadm, and kubectl versions must stay pinned (no auto-upgrades)

We automate all of this.

### Kubernetes Variables

`roles/k8s_node/vars/main.yml`:

```yaml
k8s_packages:
  - kubelet
  - kubeadm
  - kubectl
```

### Kubernetes Tasks (The Serious Stuff)

`roles/k8s_node/tasks/main.yml`:

```yaml
- name: Disable swap immediately
  command: swapoff -a

- name: Disable swap permanently
  replace:
    path: /etc/fstab
    regexp: '^([^#].*swap.*)$'
    replace: '# \1'

- name: Load kernel modules
  copy:
    dest: /etc/modules-load.d/k8s.conf
    content: |
      overlay
      br_netfilter

- name: Apply sysctl settings
  copy:
    dest: /etc/sysctl.d/k8s.conf
    content: |
      net.bridge.bridge-nf-call-iptables = 1
      net.ipv4.ip_forward = 1

- name: Reload sysctl
  command: sysctl --system

- name: Configure containerd for Kubernetes
  shell: |
    containerd config default > /etc/containerd/config.toml
    sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
  notify: restart containerd

- name: Install Kubernetes tools
  apt:
    name: "{{ k8s_packages }}"
    state: present
    update_cache: yes

- name: Hold Kubernetes packages
  dpkg_selections:
    name: "{{ item }}"
    selection: hold
  loop: "{{ k8s_packages }}"
```

### Kubernetes Handler

`roles/k8s_node/handlers/main.yml`:

```yaml
- name: restart containerd
  service:
    name: containerd
    state: restarted
```

Again: restart only if config changes.

---

## Final Playbook (One Command to Rule Them All)

`playbook.yml`:

```yaml
- name: Kubernetes node bootstrap
  hosts: ubuntu_servers
  become: true
  roles:
    - docker
    - k8s_node
```

Run it:

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-become-pass
```

---

## Verification (Always Trust, But Verify)

```bash
docker --version
swapoff --show
kubeadm version
```

Expected feelings:
- Relief
- Confidence
- "Okay… this is real DevOps now."

---

## What I Learned From This

- Roles make automation readable
- Handlers prevent stupid restarts
- Variables prevent future pain
- Ansible is not magic — it's discipline

---


## Final Thoughts

Ansible feels like writing instructions for your future self.

And future-you?

Will be very thankful you automated this.

Written while learning Ansible, breaking things safely, and finally doing it the right way.

