---
title: "Ansible for Beginners: My First Real Automation"
date: 2026-01-15
tags: ["ansible", "devops", "automation", "linux", "configuration-management"]
---

## What is Ansible? (In Simple Words)

Think of **Ansible as a remote control for your servers**.

Instead of:
- SSH into server
- Run commands manually
- Forget what you changed
- Repeat the same steps again and again 😵

You tell Ansible:
> “Hey, I want this server to look *like this*.”

And Ansible makes it happen — **again and again**, without breaking things.

The best part?
- No agent needed
- Just SSH
- Simple YAML files
- Very human-readable

---

## My Setup (Real World)

This is exactly what I used:

| Role            | OS                 |
| --------------- | ------------------ |
| Control Node    | Arch Linux (i3 WM) |
| Managed Node    | Ubuntu 22.04       |
| Connection      | SSH                |
| Automation Tool | Ansible            |

Yes — **control node and managed node can be different OSes**.
That’s totally normal in DevOps.

---

## Step 1: Inventory File (Who Do I Control?)

Ansible needs to know **which server to manage**.

I created `inventory.ini`:

```ini
[ubuntu_servers]
192.168.1.80 ansible_user=ubuntu ansible_python_interpreter=/usr/bin/python3
```

In plain English:

- **Group name**: `ubuntu_servers`
- **Server IP**: `192.168.1.80`
- **SSH user**: `ubuntu`
- **Python path**: `/usr/bin/python3` (fixed to avoid warnings)

---

## Step 2: First Test — Ping (Hello Server 👋)

Before doing anything serious, always test connectivity:

```bash
ansible ubuntu_servers -i inventory.ini -m ping
```

Output (simplified):

```text
ping: pong
```

That’s Ansible saying:

> “Yep, I can talk to your server.”

---

## Step 3: Installing Packages (Without Logging In)

I installed `curl` on Ubuntu from my Arch machine:

```bash
ansible ubuntu_servers -i inventory.ini \
  -m apt -a "name=curl state=present update_cache=yes" \
  --become --ask-become-pass
```

**Why `--become`?**

- Because installing packages needs `sudo`.

**Why `--ask-become-pass`?**

- Because my Ubuntu user uses a sudo password.

---

## Important Concept: `changed` vs `ok`

This confused me at first, but it’s very important.

**First run:**

```text
changed: true
```

Means:

> “I had to do something.”

**Second run:**

```text
ok: true
changed: false
```

Means:

> “Everything is already perfect. Nothing to do.”

This is called **idempotency**
(fancy word, simple idea).

---

## Step 4: My First Real Playbook

Ad-hoc commands are fine, but playbooks are the real power.

Here is my beginner playbook `setup.yml`:

```yaml
- name: Ubuntu beginner setup
  hosts: ubuntu_servers
  become: true

  tasks:
    - name: Install base packages
      apt:
        name:
          - curl
          - nginx
          - htop
          - git
          - vim
        state: present
        update_cache: yes

    - name: Remove nano editor
      apt:
        name: nano
        state: absent

    - name: Ensure nginx is stopped
      service:
        name: nginx
        state: stopped
```

This playbook says:

- **Install useful tools**
- **Remove nano** (bye bye 👋)
- **Stop nginx**

### Running the Playbook

```bash
ansible-playbook -i inventory.ini setup.yml --ask-become-pass
```

First run output (important part):

```text
changed=1
```

Second run output:

```text
changed=0
```

That means:

- ✅ Playbook works
- ✅ State is correct
- ✅ Automation is reliable

---

## Verifying Things Manually (Just to Be Sure)

Check `nano`:

```bash
nano --version
# command not found
```

Check `nginx`:

```bash
systemctl status nginx
# inactive (dead)
```

Browser test:

```text
http://192.168.1.80
# not loading (as expected)
```

Everything matched my playbook.
That felt really satisfying.

---

## What I Learned (Big Takeaways)

- **Control node OS doesn’t matter**
  - Arch controlling Ubuntu? Totally fine.

- **Managed node OS decides modules**
  - Ubuntu → `apt`
  - Arch → `pacman`

- **Idempotency is king**
  - Same command
  - Same result
  - No surprises

- **Playbooks > manual work**
  - Clear
  - Repeatable
  - Shareable

---

## Common Beginner Confusions (That I Faced)

- **“Nothing changed — is it broken?”**
  → No. That’s success.

- **“Why do I need sudo password?”**
  → Because automation respects security.

- **“Why fix Python interpreter path?”**
  → To avoid surprises later.

---

## What’s Next?

Now that the basics are solid, next steps are:

- **Variables** (no hardcoding)
- **Handlers** (restart services only when needed)
- **Roles** (clean project structure)

But before that — documenting this was important.
Because if you can explain it simply, you actually understand it.

---

## Final Thoughts

Ansible feels like:

- Writing instructions for a very obedient robot 🤖
  - Who never forgets
  - Never gets tired
  - And never says “works on my machine”

If you manage servers manually, Ansible is a life upgrade.

Written while learning Ansible on Arch Linux, controlling an Ubuntu server — one task at a time.
