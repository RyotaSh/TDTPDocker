### 3-1 Document your inventory and base commands
all: top-level group for all hosts.

vars: variables applied to all hosts.

ansible_user: SSH user.

ansible_ssh_private_key_file: path to your private key.

children: sub-groups.

prod: production group with your server hostname.
## base command
ansible all -i inventories/setup.yml -m ping

ansible all -i inventories/setup.yml -m setup ####gather facts get system info

ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*" ###filter specific fact

ansible all -i inventories/setup.yml -m apt -a "name=package_name state=present" --become install package

ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become remove package

ansible-playbook -i inventories/setup.yml playbook.yml
## step for setup
# Install WSL (Windows Subsystem for Linux) and Ansible there
wsl --install -d Ubuntu

Then inside Ubuntu:
sudo apt update
sudo apt install ansible -y 

then test with : 
ansible all -i /mnt/c/Users/Cheng/Documents/GitHub/TDTPDocker/ansible/inventories/setup.yml -m ping
# Copy the key into your WSL home
cp /mnt/c/Users/Cheng/Documents/GitHub/TDTPDocker/ansible/inventories/id_rsa ~/.ssh/id_rsa
# Set proper permissions
chmod 600 ~/.ssh/id_rsa

Then update your setup.yml inventory to point to the new path:
ansible_ssh_private_key_file: ~/.ssh/id_rsa

ansible all -i setup.yml -m ping should work again.


### Docker installed on remote server
To check execute this in ubuntu after you installed it :
ansible all -i inventories/setup.yml -m command -a "docker --version"
### 3-2 Document your playbook
my playbook is light and short because i've put everything inside main.yml in docker and i execute those taks using docker role
### what a docker_container task should look like:
- name: Run HTTPD
  docker_container:
    name: httpd
    image: your image name from DockerHub
### 3-3 Document your docker_container tasks configuration.

###
###


