# To use ansible against a vagrant box

## Fix the "Sudo password is required issue"
[1]: http://stackoverflow.com/questions/37434598/ansible-sudo-without-password

1. login to the box you want to provision and add yourself to the sudoers

Open the sudoers file
``` bash
sudo vim /etc/sudoers
```

find the section that deals with privileges. It should look something like this
```bash
# User privilege specification
root      ALL=(ALL:ALL) ALL
```

copy the line, paste it below and add the following
```bash
yourusernamehere ALL=(ALL:ALL) NOPASSWD:ALL
```
This will remove the annoying password prompt when executing sudo commands

2. properly setup the host in the inventory file (called using -i)

```ini
[case]
192.168.x.x
```

## Fix the /usr/bin/python not found issue

3. properly setup the playbook.yml file

```yaml
- hosts: case
  remote_user: yourusername
  become: yes
  gather_facts: no
  pre_tasks:
    - name: "Install Python"
    - raw: sudo apt-get update && sudo apt-get install python-simplejson
  tasks:
    // List your tasks here
```

According to this [0](stack overflow issue), ansible needs to run using python2. The
solution to this is to actually install it along with the simplejson package before any of
the tasks run.

At this point I'm not sure if it's just related to vagrant or if it's a wider issue but
we'll see.

In any case, you should fix the permissions on your box if you haven't already. (There
might be a way to do it in a pre_tasks module but I haven't tried that yet)

[0]:
http://stackoverflow.com/questions/32429259/ansible-fails-with-bin-sh-1-usr-bin-python-not-found
