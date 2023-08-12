# Ansible
## What is Ansible?
- Ansible is an open-source configuration management, automation, and orchestration tool designed to simplify IT operations by automating various tasks such as configuration management, application deployment, cloud provisioning, and more. 
- It is developed by Red Hat and was first released in 2012.
- Ansible's simplicity and ease of use make it popular among system administrators, developers, and IT teams for automating repetitive tasks, reducing manual errors, and accelerating the deployment of applications and infrastructure changes.
- Agentless
- Written in the Python programming language

### Ansible executables
```
[ansible@comtroller]$ ls -l /usr/local/bin/ | grep -i ansible
total 18312
-rwxr-xr-x 1 root root      224 Aug  3 19:23 ansible
-rwxr-xr-x 1 root root      244 Aug  3 19:24 ansible-community
-rwxr-xr-x 1 root root      225 Aug  3 19:23 ansible-config
-rwxr-xr-x 1 root root      254 Aug  3 19:23 ansible-connection
-rwxr-xr-x 1 root root      226 Aug  3 19:23 ansible-console
-rwxr-xr-x 1 root root      222 Aug  3 19:23 ansible-doc
-rwxr-xr-x 1 root root      225 Aug  3 19:23 ansible-galaxy
-rwxr-xr-x 1 root root      228 Aug  3 19:23 ansible-inventory
-rwxr-xr-x 1 root root      227 Aug  3 19:23 ansible-playbook
-rwxr-xr-x 1 root root      223 Aug  3 19:23 ansible-pull
-rwxr-xr-x 1 root root     1708 Aug  3 19:23 ansible-test

```

## Installing Ansible
Ansible release history can be found from
```
https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html
```
### On VirtualBox
A quick summary of VirtualBox networking modes:

- NAT – This is the default network adapter when you create a new VM. This gives internet access but applications running on the host can’t make network connections to the VM.

- Bridged – with this mode VirtualBox uses a special driver for the host’s physical network interface to create a virtual network interface for the VM. The VM gets an IP on the same network that the host is physically connected to. Host-to-guest communication and internet access are available.

- Host-only – with this mode, VirtualBox creates a virtual network that the host and the VMs are connected to. This allows host-to-guest communication but this virtual network has no access to the internet.

#### Download VirtualBox
```
https://www.virtualbox.org/wiki/Downloads
```
#### Download CentOS 7.9.2004 VDI
```
https://sourceforge.net/projects/osboxes/files/v/vb/10-C-nt/7/7.9-2009/CentOS-7.9-2009_VB-64bit.7z/download
```
#### Download Ubuntu 18.04.6 Bionic Beaver VDI
```
https://sourceforge.net/projects/osboxes/files/v/vb/55-U-u/18.04/18.04.6/64bit.7z/download
```
#### Default VDI Credentials
```
Username: osboxes
Password: osboxes.org
```
#### Installing Python3.9
```
sudo su
yum install gcc openssl-devel bzip2-devel libffi-devel zlib-devel -y
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
tar xzf Python-3.9.16.tgz
cd Python-3.9.16
./configure --enable-optimizations
make altinstall
```
#### Check Python & PIP versions
```
whereis python3
whereis pip3
/usr/local/bin/python3.9 --version
```
#### Installing Ansible
##### with yum (installs ansible 2.9)
```
yum install epel-release -y
yum install ansible -y
yum install sshpass -y
```
##### with pip(installs ansible 2.15)
```
/usr/local/bin/pip3.9 install ansible
```
#### Ansible installation directory contents
```
[root@osboxes Python-3.9.16]# ls -l /etc/ansible
total 24
-rw-r--r--. 1 root root 19985 Jan 15  2022 ansible.cfg
-rw-r--r--. 1 root root  1016 Jan 15  2022 hosts
drwxr-xr-x. 2 root root     6 Jan 15  2022 roles
```
#### Unistalling Ansible
```
yum autoremove ansible -y
```
#### Update PATH
```
cat << EOF >> ~/.bashrc
export PATH=$PATH:/usr/local/bin
EOF
source ~/.bashrc
```
#### Update Aliases
```
cat << EOF >> ~/.bashrc
alias python=python3.9
alias pip=pip3.9
EOF
source ~/.bashrc
```
#### Set Hostnames
```
hostnamectl set-hostname <NEW NAME>
```
#### Ping test
```
ansible all -m ping --user <USERNAME> --ask-pass
```
### On AWS EC2
Amazon Linux 2 AMI and t2.micro instance

#### Installing Python3.9
```
sudo su
yum install gcc openssl-devel bzip2-devel libffi-devel zlib-devel -y
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
tar xzf Python-3.9.16.tgz
cd Python-3.9.16
./configure --enable-optimizations
make altinstall
```
#### Check Python & PIP versions
```
whereis python3
whereis pip3
/usr/local/bin/python3.9 --version
```
#### Installing Ansible
##### with pip(installs ansible 2.15)
```
/usr/local/bin/pip3.9 install ansible
```
#### Ping test
```
ansible all -i <INVENTORY-FILE> -m ping --user <REMOTE-USER>
```

```
root@ip-172-xx-xx-xx ec2-user] # ansible all -i inv.txt -m ping --user ec2-user --private-key=ec2.pem
[WARNING]: Platform Linux on host 172.31.87.122 is using the discovered Python interpreter at /usr/bin/python3.7,
but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.15/reference_appendices/interpreter_discovery.html for more information.
172.31.87.122 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.7"
    },
    "changed": false,
    "ping": "pong"
}
```

## Setup Passwordless SSH
### Create a new user account
Create user with `useradd`
```
useradd -m ansible
```
Add this user to the sudoers file to grant sudo privileges to all commands without a password 
```
echo "ansible ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```
In case the user needs to enter a password for privilege escalation 
```
echo "ansible ALL=(ALL) ALL" >> /etc/sudoers
```
### Generate SSH keys
Generate SSH Keys using the following command in your local terminal:
```
ssh-keygen
```
This will create two files: `id_rsa` (private key) and `id_rsa.pub` (public key) in the `~/.ssh/` directory of the user.

### Copy SSH keys to the remote machine
Now, you need to copy the content of your public key (id_rsa.pub) to the remote server's authorized_keys file at `~/.ssh/authorized_keys`. You can do this manually or by using the `ssh-copy-id` command.
```
ssh-copy-id user@remote_server_ip
```
Replace the user with your username on the remote server and remote_server_ip with the IP address or hostname of the remote server. This command will prompt you for the remote server's password once, to add your public key to the authorized_keys file.
If the ssh-copy-id command is not available on your system, you can manually append the public key to the authorized_keys file.
Once the public key is added to the remote server's authorized_keys file, you should be able to SSH into the remote server without being prompted for a password
```
ssh user@remote_server_ip
```
If everything is set up correctly, you will log in without needing to enter a password.

## Ansible Commands
Get help
```
[ansible@ip-172-31-86-202 ~]$ ansible --help
usage: ansible [-h] [--version] [-v] [-b] [--become-method BECOME_METHOD] [--become-user BECOME_USER]
               [-K | --become-password-file BECOME_PASSWORD_FILE] [-i INVENTORY] [--list-hosts]
               [-l SUBSET] [-P POLL_INTERVAL] [-B SECONDS] [-o] [-t TREE]
               [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT]
               [--ssh-common-args SSH_COMMON_ARGS] [--sftp-extra-args SFTP_EXTRA_ARGS]
               [--scp-extra-args SCP_EXTRA_ARGS] [--ssh-extra-args SSH_EXTRA_ARGS]
               [-k | --connection-password-file CONNECTION_PASSWORD_FILE] [-C] [-D] [-e EXTRA_VARS]
               [--vault-id VAULT_IDS]
               [--ask-vault-password | --vault-password-file VAULT_PASSWORD_FILES] [-f FORKS]
               [-M MODULE_PATH] [--playbook-dir BASEDIR] [--task-timeout TASK_TIMEOUT] [-a MODULE_ARGS]
               [-m MODULE_NAME]
               pattern

Define and run a single task 'playbook' against a set of hosts

positional arguments:
  pattern               host pattern

optional arguments:
  --ask-vault-password, --ask-vault-pass
                        ask for vault password
  --become-password-file BECOME_PASSWORD_FILE, --become-pass-file BECOME_PASSWORD_FILE
                        Become password file
  --connection-password-file CONNECTION_PASSWORD_FILE, --conn-pass-file CONNECTION_PASSWORD_FILE
                        Connection password file
  --list-hosts          outputs a list of matching hosts; does not execute anything else
  --playbook-dir BASEDIR
                        Since this tool does not use playbooks, use this as a substitute playbook
                        directory. This sets the relative path for many features including roles/
                        group_vars/ etc.
  --task-timeout TASK_TIMEOUT
                        set task timeout limit in seconds, must be positive integer.
  --vault-id VAULT_IDS  the vault identity to use
  --vault-password-file VAULT_PASSWORD_FILES, --vault-pass-file VAULT_PASSWORD_FILES
                        vault password file
  --version             show program's version number, config file location, configured module search
                        path, module location, executable location and exit
  -B SECONDS, --background SECONDS
                        run asynchronously, failing after X seconds (default=N/A)
  -C, --check           don't make any changes; instead, try to predict some of the changes that may
                        occur
  -D, --diff            when changing (small) files and templates, show the differences in those files;
                        works great with --check
  -K, --ask-become-pass
                        ask for privilege escalation password
  -M MODULE_PATH, --module-path MODULE_PATH
                        prepend colon-separated path(s) to module library (default={{ ANSIBLE_HOME ~
                        "/plugins/modules:/usr/share/ansible/plugins/modules" }})
  -P POLL_INTERVAL, --poll POLL_INTERVAL
                        set the poll interval if using -B (default=15)
  -a MODULE_ARGS, --args MODULE_ARGS
                        The action's options in space separated k=v format: -a 'opt1=val1 opt2=val2' or
                        a json string: -a '{"opt1": "val1", "opt2": "val2"}'
  -e EXTRA_VARS, --extra-vars EXTRA_VARS
                        set additional variables as key=value or YAML/JSON, if filename prepend with @
  -f FORKS, --forks FORKS
                        specify number of parallel processes to use (default=5)
  -h, --help            show this help message and exit
  -i INVENTORY, --inventory INVENTORY, --inventory-file INVENTORY
                        specify inventory host path or comma separated host list. --inventory-file is
                        deprecated
  -k, --ask-pass        ask for connection password
  -l SUBSET, --limit SUBSET
                        further limit selected hosts to an additional pattern
  -m MODULE_NAME, --module-name MODULE_NAME
                        Name of the action to execute (default=command)
  -o, --one-line        condense output
  -t TREE, --tree TREE  log output to this directory
  -v, --verbose         Causes Ansible to print more debug messages. Adding multiple -v will increase
                        the verbosity, the builtin plugins currently evaluate up to -vvvvvv. A
                        reasonable level to start is -vvv, connection debugging might require -vvvv.

Privilege Escalation Options:
  control how and which user you become as on target hosts

  --become-method BECOME_METHOD
                        privilege escalation method to use (default=sudo), use `ansible-doc -t become
                        -l` to list valid choices.
  --become-user BECOME_USER
                        run operations as this user (default=root)
  -b, --become          run operations with become (does not imply password prompting)

Connection Options:
  control as whom and how to connect to hosts

  --private-key PRIVATE_KEY_FILE, --key-file PRIVATE_KEY_FILE
                        use this file to authenticate the connection
  --scp-extra-args SCP_EXTRA_ARGS
                        specify extra arguments to pass to scp only (e.g. -l)
  --sftp-extra-args SFTP_EXTRA_ARGS
                        specify extra arguments to pass to sftp only (e.g. -f, -l)
  --ssh-common-args SSH_COMMON_ARGS
                        specify common arguments to pass to sftp/scp/ssh (e.g. ProxyCommand)
  --ssh-extra-args SSH_EXTRA_ARGS
                        specify extra arguments to pass to ssh only (e.g. -R)
  -T TIMEOUT, --timeout TIMEOUT
                        override the connection timeout in seconds (default=10)
  -c CONNECTION, --connection CONNECTION
                        connection type to use (default=smart)
  -u REMOTE_USER, --user REMOTE_USER
                        connect as this user (default=None)

Some actions do not make sense in Ad-Hoc (include, meta, etc)
```


## Important Links
#### Ansible Configuration Settings
```
https://docs.ansible.com/ansible/latest/reference_appendices/config.html#
```
