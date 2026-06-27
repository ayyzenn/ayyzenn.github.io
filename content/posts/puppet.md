---
title: "Puppet with Docker: How I Tested Master and Agent"
date: 2022-07-28
tags: ["dockers", "containers", "puppet"]
---
## What I Did

I wanted to try **Puppet** without dedicating two full VMs. I ran a Puppet master and agent in separate **Ubuntu Docker containers** on my machine. Docker host commands work the same on Ubuntu and Arch.

## Installing Puppet Master

1. Download the Ubuntu image:

    ```bash
    sudo docker pull ubuntu
    ```

2. Create a container for Puppet master:

    ```bash
    sudo docker run --name puppet-master -it ubuntu
    exit
    ```

3. Start the container:

    ```bash
    sudo docker start puppet-master
    ```

4. Open the bash shell of the Puppet master container:

    ```bash
    sudo docker exec -it puppet-master bash
    ```

5. Update and upgrade the container:

    ```bash
    apt update
    apt upgrade
    ```

6. Install necessary tools:

    ```bash
    apt install nano inetutils-ping net-tools wget -y
    ```

7. Add the Puppet repository:

    ```bash
    wget https://apt.puppetlabs.com/puppet7-release-focal.deb
    dpkg -i puppet7-release-focal.deb
    apt update
    ```

8. Create an image of the configured container:

    ```bash
    exit
    sudo docker commit puppet-master puppet-7
    ```

9. Confirm the image creation:

    ```bash
    sudo docker images
    ```

10. Install Puppet server:

    ```bash
    sudo docker exec -it puppet-master bash
    apt install puppetserver
    ```

11. Modify memory allocation for Java in Puppet server configuration:

    ```bash
    nano /etc/default/puppetserver
    ```
    Replace:
    ```
    JAVA_ARGS="-Xms2g -Xmx2g"
    ```
    With:
    ```
    JAVA_ARGS="-Xms64m -Xmx512m"
    ```

12. Create a certificate authority:

    ```bash
    /opt/puppetlabs/bin/puppetserver ca setup
    ```

13. Start the Puppet server:

    ```bash
    service puppetserver start
    ```

14. Add Puppet to the system path:

    ```bash
    echo 'PATH=$PATH:/opt/puppetlabs/bin' >> ~/.bashrc
    source ~/.bashrc
    ```

15. Retrieve the fully qualified domain name (FQDN):

    ```bash
    facter -p | grep fqdn
    ```

## Installing the Puppet Agent

1. Create a container for the Puppet agent using the previously created image:

    ```bash
    sudo docker run --name puppet-agent -it puppet-7
    exit
    ```

2. Start the Puppet agent container:

    ```bash
    sudo docker start puppet-agent
    ```

3. Open the bash shell of the Puppet agent container:

    ```bash
    sudo docker exec -it puppet-agent bash
    ```

4. Install Puppet agent:

    ```bash
    apt install puppet-agent
    ```

5. Add Puppet to the system path:

    ```bash
    echo 'PATH=$PATH:/opt/puppetlabs/bin' >> ~/.bashrc
    source ~/.bashrc
    ```

## Configuring Puppet Master and Puppet Agent

### Configuring the Puppet Agent

1. Edit the hosts file:

    ```bash
    nano /etc/hosts
    ```

2. Add the IP address of the Puppet master container along with its FQDN.

3. Check the connection:

    ```bash
    ping <your fqdn>
    ```

### Configuring the Puppet Master

1. Sign certificates of all agents:

    ```bash
    nano /etc/puppetlabs/puppet/puppet.conf
    ```

2. Add the following line:

    ```bash
    autosign=true
    service puppetserver restart
    ```

3. Verify the connection:

    ```bash
    puppet agent --test --server <fqdn>
    ```

If the connection is established successfully, the output should indicate no errors.

