
---

# Topics :

- EC2 - Security Groups 
- Apache HTTP Server & File System 
- Ip Address 

---
# Security Groups 

> [!NOTE]  
>A **Security Group** is like a **virtual firewall** in AWS that controls **inbound and outbound traffic** to your EC2 instances.

<img width="512" height="261" alt="image" src="https://github.com/user-attachments/assets/97bcea49-bdd7-406c-b2f8-44477e8e0481" />

#### Why Do We Need Them?

- To control **who can access** your instance.
    
- To allow only necessary traffic (e.g., SSH, HTTP).
    
- Acts as a protective layer against unwanted access.

#### How It Works:

- You **define rules**: for example, allow port 22 (SSH), or port 80 (HTTP).
    
- **Stateful**: If inbound is allowed, the response goes out automatically.
    
- Rules are based on **IP**, **port number**, and **protocol**.

---
# What is Apache?

> [!NOTE]
>**Apache** is a **free, open-source web server** software used to serve web pages over the internet.

#### Why Use Apache?

- It's stable, widely used, and easy to configure.
    
- Runs on Linux, Windows, and macOS.
    
- Perfect for hosting websites and web apps.

#### How It Works:

- Apache listens on port **80** for HTTP requests.
    
- When someone visits your server’s IP, Apache serves the **HTML content** stored in the server’s file system.

>Common Command to Install:
```sh
sudo apt install apache2
```

---
# Apache File System Structure

Apache’s configuration and web content files are stored in **`/etc/apache2/`** and **`/var/www/html/`** on Ubuntu systems.

### 🗂️ Important Directories:

|Path|Description|
|---|---|
|`/etc/apache2/apache2.conf`|Main configuration file|
|`/etc/apache2/ports.conf`|Defines listening ports|
|`/etc/apache2/sites-enabled/`|Active virtual hosts|
|`/var/www/html/`|Default location for website files (`index.html`)|
 >To modify the web page:
```sh
sudo nano /var/www/html/index.html
````

---
# What is an IP Address?

<div style="border-left: 4px solid #007ACC; padding: 0.5em;">
  <strong>Definition:</strong> An IP address is a unique number assigned to every device on the Internet.
</div>

####  Types of IP in AWS:

|Type|Purpose|
|---|---|
|**Public IP**|Used to access EC2 from the internet|
|**Private IP**|Used within AWS (e.g., between EC2s in same VPC)|
#### Why is IP Important?

- You need the **public IP** to SSH into your instance or access your web app.
    
- **Private IPs** are essential for internal communication in the cloud (e.g., with a database).

**Understanding:**
>Think of the **public IP** as your **home address** and the **private IP** as your **room number inside the house**.
