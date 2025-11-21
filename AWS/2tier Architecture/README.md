
# Host a Login Page on EC2 and Save to RDS (2-Tier Architecture)
---
## 🛠️ Tech Stack

- **Frontend**: HTML  
- **Backend**: PHP  
- **Database**: Amazon RDS (MySQL)  
- **Platform**: Amazon EC2 (Ubuntu server)  
- **Security Groups**: Allow ports `80`, `22`, and `3306` in security groups

---

##  Step 1: Create RDS MySQL Database

- **Engine**: MySQL  
- **DB Name**: `LoginDB`  
- **Username**: `admin`  
- **Password**: `Test.1234..`  
- **Public Access**: ✅ Yes  
- **Endpoint Example**:  
  `login-db.cj4x7kld6rdb.ap-south-1.rds.amazonaws.com`

---
##  Step 2: Configure RDS from EC2 (Optional)

###  Install MySQL Client on EC2:

```bash
sudo apt update -y
sudo apt install mysql-server -y
```

###  Connect to RDS from EC2:

```sh
mysql -h login-db.cj4x7kld6rdb.ap-south-1.rds.amazonaws.com -u admin -p
```

---
###  Create Database & Table:

```sql
CREATE DATABASE LoginDB;

USE LoginDB;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    password VARCHAR(255) NOT NULL
);
```

---

##  Step 3: Setup EC2 Instance (Ubuntu)

```sh
sudo apt update -y
sudo apt install apache2 php libapache2-mod-php php-mysql -y
sudo systemctl start apache2
sudo systemctl enable apache2
sudo rm /var/www/html/index.html
```

---

## 📄 Step 4: Add HTML + PHP Code

### ✅ Save this as `/var/www/html/index.php`

```php
<?php
session_start();

$host = "rds.c7a0ge8ccl27.ap-south-1.rds.amazonaws.com";  // Replace RDS endpoint
$db_user = "admin";  // Replace DB master username
$db_pass = "5OvXbfTpoIlClznb6CKk";  // Replace DB master passwd
$db_name = "LoginDB";  //Replace Database Name

$conn = new mysqli($host, $db_user, $db_pass, $db_name);
if ($conn->connect_error) {
    die("Database connection failed: " . $conn->connect_error);
}

// Register
if (isset($_POST["register"])) {
    $username = $conn->real_escape_string($_POST['username']);
    $password = $_POST['password'];

    $check_query = "SELECT * FROM users WHERE username=?";
    $stmt = $conn->prepare($check_query);
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $stmt->store_result();

    if ($stmt->num_rows > 0) {
        $error = "Username already exists!";
    } else {
        $query = "INSERT INTO users (username, password) VALUES (?, ?)";
        $stmt = $conn->prepare($query);
        $stmt->bind_param("ss", $username, $password);
        if ($stmt->execute()) {
            $success = "Registration successful! You can now log in.";
        } else {
            $error = "Error: " . $stmt->error;
        }
    }
    $stmt->close();
}

// Login
if (isset($_POST["login"])) {
    $username = $conn->real_escape_string($_POST['username']);
    $password = $_POST['password'];

    $query = "SELECT password FROM users WHERE username=?";
    $stmt = $conn->prepare($query);
    $stmt->bind_param("s", $username);
    $stmt->execute();
    $result = $stmt->get_result();

    if ($result->num_rows > 0) {
        $row = $result->fetch_assoc();
        if ($password == $row['password']) {
            $_SESSION['username'] = $username;
        } else {
            $error = "Invalid username or password!";
        }
    } else {
        $error = "Invalid username or password!";
    }
}

if (isset($_GET["logout"])) {
    session_destroy();
    header("Location: index.php");
    exit();
}
?>

<!-- HTML Section -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login & Register</title>
    <style>
        body { font-family: Arial; background: #f4f4f4; padding: 50px; text-align: center; }
        .container { background: #fff; padding: 20px; border-radius: 10px; width: 300px; margin: auto; box-shadow: 0 0 10px gray; }
        input { width: 90%; padding: 8px; margin: 6px 0; border-radius: 4px; border: 1px solid gray; }
        .btn { background: blue; color: white; padding: 10px; border: none; width: 100%; cursor: pointer; }
        .btn:hover { background: darkblue; }
        .logout-btn { background: red; padding: 8px; color: white; border: none; cursor: pointer; }
        .error { color: red; }
        .success { color: green; }
    </style>
</head>
<body>

<?php if (isset($_SESSION['username'])): ?>
    <div class="container">
        <h1>Welcome, <?php echo $_SESSION['username']; ?>!</h1>
        <p>You have successfully logged in.</p>
        <a href="index.php?logout=true" class="logout-btn">Logout</a>
    </div>
<?php else: ?>
    <div class="container">
        <h2>Login</h2>
        <?php if (isset($error)) echo "<p class='error'>$error</p>"; ?>
        <form method="POST">
            <input type="text" name="username" placeholder="Enter Username" required><br>
            <input type="password" name="password" placeholder="Enter Password" required><br>
            <input type="submit" class="btn" name="login" value="Login">
        </form>

        <h2>Register</h2>
        <?php if (isset($success)) echo "<p class='success'>$success</p>"; ?>
        <form method="POST">
            <input type="text" name="username" placeholder="Enter Username" required><br>
            <input type="password" name="password" placeholder="Enter Password" required><br>
            <input type="submit" class="btn" name="register" value="Register">
        </form>
    </div>
<?php endif; ?>

</body>
</html>
```
## ✅ To Verify

> Login into Mysql and check the table

```sql
use LoginDB;
select * from users;
```

---
## 📁 Folder Structure

```bash
/var/www/html/
├── index.php      # Contains both frontend and backend code`
```

---

##  Troubleshooting

- Check Apache logs for errors:

```
sudo tail -f /var/log/apache2/error.log
```
