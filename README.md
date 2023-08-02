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
A quick summary about VirtualBox networking modes

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
sudo yum install gcc openssl-devel bzip2-devel libffi-devel zlib-devel -y
wget https://www.python.org/ftp/python/3.9.16/Python-3.9.16.tgz
tar xzf Python-3.9.16.tgz
cd Python-3.9.16
sudo ./configure --enable-optimizations
sudo make altinstall
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
#### Ping Test
```
ansible all -m ping --user <USERNAME> --ask-pass
```
### On AWS EC2
TODO