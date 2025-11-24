#  Ansible Installation & First Automation Guide (Linux Edition)

---
# 1.Installation of Ansible 

### On Ubuntu / Debian:

```sh
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

> Verify: `ansible --version`

---
#### Update Ansible to the Latest Version (All Distros)

> If you want the **latest version** via `pip`:

```sh
sudo apt install -y python3-pip  # or yum install python3-pip
pip3 install --user ansible --break-system-packages
```

create path of it :
```sh
export PATH="$HOME/.local/bin:$PATH"
source ~/.bashrc
```

Then verify with version;
```sh
ansible --version
```

#### Uninstall or Reinstall Ansible:

```sh
# To remove Ansible (apt):
sudo apt remove --purge ansible

# To remove Ansible (pip):
pip3 uninstall ansible
```
---
### Setup SSH Access (Linux Targets)
#### On Control Node:

>Generate SSH Key (on Control Node):
```sh
ssh-keygen
```

>Share Public Key Automatically:
```sh
ssh-copy-id ubuntu@<target-ip>
```

#### Or manually:

```sh
cat ~/.ssh/id_rsa.pub
```

> Paste the key into the target machine:
```sh
nano ~/.ssh/authorized_keys
```

---

# 2.First Automation  
## Create an Inventory File

Create a inventory file;
```sh
nano inventory
```

>Example content:
```ini
[webservers]
192.168.1.10 ansible_user=ubuntu ansible_ssh_private_key_file=/home/ubuntu/.ssh/id_rsa
```

> Save as `inventory`.


---
## Run an Ad Hoc Command

#### ✅ Ping Test:
```sh
ansible -i inventory all -m ping 
```

#### ✅ Create a File on Remote Host:
```sh
ansible -i inventory all -m shell -a "touch NewFile"
```

- `-i inventory`: Specifies the inventory file
    
- `-m shell`: Uses the shell module
    
- `-a`: Passes the command argument

---
## What is a Playbook?

A **playbook** is a **YAML file** where you define **what to automate**.

- **What** tasks to run
    
- **Where** to run them
    
- **How** to run them (using modules, variables, etc.)

### 📄 Sample Playbook: `playbook.yml`

```yml
- name: Install and Start Nginx
  hosts: webservers
  become: yes
  gather_facts: no


  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Ensure Nginx is running
      service:
        name: nginx
        state: started
        enabled: yes
```

> Run the Playbook

```sh
ansible-playbook -i inventory playbook.yml
```

✅ This will:

- Install NGINX
    
- Start and enable the NGINX service
    
- Apply it to all `webservers` listed in your `inventory`
---


