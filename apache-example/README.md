
# Ansible Tutorial - The Apache Example

These notes relate to a 2-box setup to demonstrate Ansible.

Note: I tried using Ubuntu 14 (Trusty64), and found that the installed version
of Ansible is too old. Switched to Ubuntu 16 (Xenial) instead, and got 2.0.0.2
which is new enough.

If you have a really old version of Ubuntu (e.g. we found a box with Ubuntu 12
(Precise Pangolin) on it), a way of installing Ansible on old OSs is described
at: https://valdhaus.co/writings/ansible-ubuntu-debian/ 

## Start the Virtual Machines
This tutorial uses a pair of virtual machines; one will have Ansible installed
on it, and will be where you run the Ansible commands, the other is the target
machine, which Ansible will modify.

I'll assume you have installed [Vagrant](https://www.vagrantup.com/docs/installation/).

| Hostname | IP Addr   | SSH Forwarded port | Apache Forwarded port |
| -------- | --------- | ------------------ | ----------------------|
| alpha    | 10.0.0.23 | 2223               | N/A
| beta     | 10.0.0.24 | 2224               | 8024

A user called "andy" is created on each server, with a password of "changeme"
(see the Vagrantfile), and sudo privileges

To start the VMs, open a shell and 'cd' to the folder where the "Vagrantfile" 
is, then run
```
vagrant up
```

## Pre-flight checks

**Can you ssh into the 'alpha' box?** 

Try this:
```
ssh -p 2223 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null andy@127.0.0.1
```

**Set up some keys**:

Run these commands on the first VM:
- Create an SSH key: `ssh-keygen -f ~/.ssh/id_rsa -P ''`
- Copy the key to the second box: `ssh-copy-id -i ~/.ssh/id_rsa andy@10.0.0.24`

**Can Ansible talk to the second VM?**

- Install Ansible: `sudo apt install -y ansible`
- Ensure Python is installed on the 2nd node: `ssh 'andy@10.0.0.24' 'sudo apt install -y python'` (*)
- Grab the hosts.txt file from this repository. Save to your current folder
- Check Ansible is working: `ansible -i hosts.txt all -m ping`

(*) N.B. Python is only required for the Ansible "Ping" module. Apart from
that, Python isn't needed on the second VM.

## To run this Playbook
Copy the following files to the first VM:
- hosts.txt (you probably did this already; see pre-flight checks above)
- index.html
- playbook.yaml
- vhost.template

Then run the following command:

```
ansible-playbook -i hosts.txt playbook.yaml
```

Point your browser at http://localhost:8024

## Tear down the VMs
In the folder with the Vagrantfile, run:

```
vagrant destroy
```

## Sources
The Apache Example used in this demo is taken from Erika Heidi's Ansible 
Tutorial at 
https://www.digitalocean.com/community/tutorials/configuration-management-101-writing-ansible-playbooks


