# Ansible
## What is Ansible?
- Ansible is an open-source configuration management, automation, and orchestration tool designed to simplify IT operations by automating various tasks such as configuration management, application deployment, cloud provisioning, and more. 
- It is developed by Red Hat and was first released in 2012.
- Ansible's simplicity and ease of use make it popular among system administrators, developers, and IT teams for automating repetitive tasks, reducing manual errors, and accelerating the deployment of applications and infrastructure changes.
- Agentless
- Written in the Python programming language

## Installing Ansible
Ansible release history can be found from
```
https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html
```
### On VirtualBox
A quick summary about VirtualBox networking modes:

- NAT – This is default network adapter when you create a new VM. This gives internet access but applications running on the host can’t make network connections to the VM.

- Bridged – with this mode VirtualBox uses a special driver for the host’s physical network interface to create a virtual network interface for the VM. The VM gets an IP on the same network that the host is physically connected to. Host-to-guest communication and internet access are available.

- Host-only – with this mode VirtualBox creates a virtual network that the host and the VMs are connected to. This allows host-to-guest communication but this virtual network has no access to the internet.

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
root@ip-172-31-81-238 ec2-user]# ansible all -i <INVENTORY-FILE> -m ping --user <REMOTE-USER> --private-key=<PRIVATE-KEY-FILE>
[WARNING]: Platform linux on host 172.31.87.122 is using the discovered Python interpreter at /usr/bin/python3.7,
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
Add this user to sudoers file to grant sudo privileges to all commands without password 
```
echo "ansible ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```
### Generate SSH keys
Generate SSH Keys using the following command in your local terminal:
```
ssh-keygen
```
This will create two files: id_rsa (private key) and id_rsa.pub (public key) in the ~/.ssh/ directory of the user.

### Copy SSH keys to remote machine
Now, you need to copy the content of your public key (id_rsa.pub) to the remote server's authorized_keys file at `~/.ssh/authorized_keys`. You can do this manually or by using the ssh-copy-id command:
```
ssh-copy-id user@remote_server_ip
```
Replace user with your username on the remote server and remote_server_ip with the IP address or hostname of the remote server. This command will prompt you for the remote server's password once, to add your public key to the authorized_keys file.
If the ssh-copy-id command is not available on your system, you can manually append the public key to the authorized_keys file.
Once the public key is added to the remote server's authorized_keys file, you should be able to SSH into the remote server without being prompted for a password:
```
ssh user@remote_server_ip
```
If everything is set up correctly, you will log in without needing to enter a password.