
# Sample Project: Deploy a Web Server

#### Goal:

Use Ansible to automate NGINX setup and host a simple webpage.
### 📁 Project Structure:

```md
ansible-web/
├── inventory
├── playbook.yml
└── files/
    └── index.html
```

### 🗂️ Inventory

```ini
[webservers]
192.168.1.10 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa
```

### 📄 Files/index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hello from Ansible</title>
  <style>
    body { font-family: sans-serif; text-align: center; padding-top: 50px; }
  </style>
</head>
<body>
  <h1>🚀 Welcome to my Ansible-deployed web server!</h1>
  <p>This page was deployed automatically with Ansible.</p>
</body>
</html>
```

###  Playbook.yml

```yml

- name: Deploy NGINX Web Server
  hosts: webservers
  become: yes
  gather_facts: no

  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Copy custom index.html to web root
      copy:
        src: files/index.html
        dest: /var/www/html/index.html
        mode: '0644'

    - name: Ensure NGINX is running
      service:
        name: nginx
        state: started
        enabled: yes

```

### Run the Playbook:

```
ansible-playbook -i inventory playbook.yml
```

### ✅ Result:

- NGINX is installed on the target host(s)
    
- Custom HTML page is copied to `/var/www/html/index.html`
    
- You can now open in your browser:

```http
http://<your-server-ip>
```

---