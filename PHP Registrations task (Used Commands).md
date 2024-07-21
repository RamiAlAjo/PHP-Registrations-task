# PHP Registrations task

[//]: # "To Preview The Output Press (Ctrl + k) followed by (v)  "

## Create a registration and login page. Ensure all required fields (name, email, and password) are validated using PHP.

## 1. Setting Up the Database

```sql
CREATE DATABASE user_registration;
```

```sql
USE user_registration;
```

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);
```

---

## 2. Create register.php

### HTML & CSS

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Register</title>
    <style>
      body {
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
        background-color: #eef2f3;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
      }
      .container {
        background-color: #fff;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        max-width: 400px;
        width: 100%;
      }
      h2 {
        text-align: center;
        color: #333;
      }
      label {
        display: block;
        margin-bottom: 6px;
        color: #666;
      }
      input[type="text"],
      input[type="email"],
      input[type="password"] {
        width: calc(100% - 20px);
        padding: 10px;
        margin: 10px 0 20px 0;
        border: 1px solid #ccc;
        border-radius: 5px;
        box-sizing: border-box;
      }
      button {
        width: 100%;
        background-color: #4caf50;
        color: white;
        padding: 12px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
      }
      button:hover {
        background-color: #45a049;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h2>Registration Form</h2>
      <form action="register.php" method="post">
        <label for="name">Name:</label>
        <input type="text" name="name" required />
        <label for="email">Email:</label>
        <input type="email" name="email" required />
        <label for="password">Password:</label>
        <input type="password" name="password" required />
        <label for="confirm_password">Confirm Password:</label>
        <input type="password" name="confirm_password" required />
        <button type="submit" name="register">Register</button>
      </form>
    </div>
  </body>
</html>
```

### PHP Validation with Regex (register.php)

```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST['name'];
    $email = $_POST['email'];
    $password = $_POST['password'];
    $confirm_password = $_POST['confirm_password'];

    $name_regex = "/^[a-zA-Z-' ]+$/";
    $email_regex = "/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/";
    $password_regex = "/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/";

    if (empty($name) || empty($email) || empty($password) || empty($confirm_password)) {
        echo "All fields are required.";
    } elseif (!preg_match($name_regex, $name)) {
        echo "Invalid name format.";
    } elseif (!preg_match($email_regex, $email)) {
        echo "Invalid email format.";
    } elseif (!preg_match($password_regex, $password)) {
        echo "Password must be at least 8 characters long and contain at least one letter and one number.";
    } elseif ($password !== $confirm_password) {
        echo "Passwords do not match.";
    } else {
        $hashed_password = password_hash($password, PASSWORD_DEFAULT);

        $conn = new mysqli("localhost", "root", "", "user_registration");

        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        }

        $stmt = $conn->prepare("INSERT INTO users (name, email, password) VALUES (?, ?, ?)");
        $stmt->bind_param("sss", $name, $email, $hashed_password);

        if ($stmt->execute()) {
            echo "Registration successful.";
        } else {
            echo "Error: " . $stmt->error;
        }

        $stmt->close();
        $conn->close();
    }
}
?>
```

---

## 3. Create login.php

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Login</title>
    <style>
      body {
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
        background-color: #eef2f3;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
      }
      .container {
        background-color: #fff;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        max-width: 400px;
        width: 100%;
      }
      h2 {
        text-align: center;
        color: #333;
      }
      label {
        display: block;
        margin-bottom: 6px;
        color: #666;
      }
      input[type="email"],
      input[type="password"] {
        width: calc(100% - 20px);
        padding: 10px;
        margin: 10px 0 20px 0;
        border: 1px solid #ccc;
        border-radius: 5px;
        box-sizing: border-box;
      }
      button {
        width: 100%;
        background-color: #4caf50;
        color: white;
        padding: 12px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
      }
      button:hover {
        background-color: #45a049;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h2>Login Form</h2>
      <form action="login.php" method="post">
        <label for="email">Email:</label>
        <input type="email" name="email" required />
        <label for="password">Password:</label>
        <input type="password" name="password" required />
        <button type="submit" name="login">Login</button>
      </form>
    </div>
  </body>
</html>
```

### PHP Validation With Regex (login.php)

```php
<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $email = $_POST['email'];
    $password = $_POST['password'];

    $email_regex = "/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/";

    if (empty($email) || empty($password)) {
        echo "Both fields are required.";
    } elseif (!preg_match($email_regex, $email)) {
        echo "Invalid email format.";
    } else {
        $conn = new mysqli("localhost", "root", "", "user_registration");

        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        }

        $stmt = $conn->prepare("SELECT password FROM users WHERE email = ?");
        $stmt->bind_param("s", $email);
        $stmt->execute();
        $stmt->store_result();

        if ($stmt->num_rows > 0) {
            $stmt->bind_result($hashed_password);
            $stmt->fetch();

            if (password_verify($password, $hashed_password)) {
                echo "Login successful.";
            } else {
                echo "Incorrect password.";
            }
        } else {
            echo "No user found with that email.";
        }

        $stmt->close();
        $conn->close();
    }
}
?>
```

---

## 4. (Optional Step) Create index.php (Landing Page)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Welcome</title>
    <style>
      body {
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
        background-color: #eef2f3;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
      }
      .container {
        text-align: center;
        background-color: #fff;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        max-width: 400px;
        width: 100%;
      }
      h1 {
        color: #333;
        margin-bottom: 20px;
      }
      .btn-container {
        display: flex;
        justify-content: space-between;
      }
      a.button {
        background-color: #4caf50;
        color: white;
        padding: 12px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
        text-decoration: none;
        display: inline-block;
        width: 48%;
        text-align: center;
      }
      a.button:hover {
        background-color: #45a049;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h1>Welcome</h1>
      <div class="btn-container">
        <a class="button" href="register.php">Register</a>
        <a class="button" href="login.php">Login</a>
      </div>
    </div>
  </body>
</html>
```

---

---
