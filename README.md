# 🐳 Laravel Mini Dashboard - Docker Setup Guide

This project uses **Docker** to run a complete Laravel environment with:
- PHP + Apache
- MySQL
- phpMyAdmin

Follow the steps below carefully 👇

---

## 🧰 1. Prerequisites

Make sure you have these tools installed on your system:
- 🐳 [Docker](https://www.docker.com/)
- ⚙️ [Docker Compose](https://docs.docker.com/compose/)

---

## 🚀 2. Start the Docker environment

```bash
# Build and start all containers (php, mysql, phpmyadmin)
docker compose up --build
```

📘 This command will:

* Build the PHP + Apache container using the Dockerfile
* Start MySQL and phpMyAdmin containers
* Expose the app on **[http://localhost:81](http://localhost:81)**
* Expose phpMyAdmin on **[http://localhost:8087](http://localhost:8087)**

---

## 🐘 3. Access the PHP container

```bash
# Enter the running PHP container
docker exec -it php_mini_dashboard bash
```

📘 You are now inside the PHP/Apache container (where Laravel lives).

---

## 📦 4. Install Laravel dependencies

Inside the container, run:

```bash
# Install all required PHP dependencies for Laravel
composer install
```

💡 If you don’t see a `.env` file, copy it from `.env.example`:

```bash
cp .env.example .env
```

Then generate the Laravel key:

```bash
php artisan key:generate
```

---

## ⚙️ 5. Apache Configuration (inside the container)

Now configure Apache to serve **Laravel’s public folder**.

Run this command (copy-paste it inside the container):

```bash
# Replace Apache default configuration
cat <<EOF > /etc/apache2/sites-enabled/000-default.conf
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName localhost
    DocumentRoot /var/www/html/public

    <Directory /var/www/html/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog /var/log/apache2/error.log
    CustomLog /var/log/apache2/access.log combined
</VirtualHost>
EOF
```

Then enable `mod_rewrite` (required by Laravel) and restart Apache:

```bash
# Enable Apache rewrite module
a2enmod rewrite

# Restart Apache service
service apache2 restart
```

---

## 🔒 6. Set correct permissions

```bash
# Give Apache access to project files
chown -R www-data:www-data /var/www/html
chmod -R 755 /var/www/html
```

---

## 🧱 7. Database Configuration

By default, your **MySQL container** is configured with these values:

```
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=mini_dashboard
DB_USERNAME=user
DB_PASSWORD=user_password
```

📝 Open your `.env` file and make sure these lines are set correctly.

---

## 🌐 8. Access the Project

After Apache restarts successfully, open your browser:

* 🖥️ Laravel App → [http://localhost:81](http://localhost:81)
* 🧰 phpMyAdmin → [http://localhost:8087](http://localhost:8087)

---

## 🧹 9. Useful Docker Commands

```bash
# Stop all running containers
docker compose down

# Rebuild containers without using cache
docker compose build --no-cache

# Restart only the PHP container
docker restart php_mini_dashboard
```

---

## ✅ Summary

| Service       | URL                                            | Port | Container Name            |
| ------------- | ---------------------------------------------- | ---- | ------------------------- |
| Laravel (App) | [http://localhost:81](http://localhost:81)     | 81   | php_mini_dashboard        |
| phpMyAdmin    | [http://localhost:8087](http://localhost:8087) | 8087 | phpmyadmin_mini_dashboard |
| MySQL         | Internal only (`mysql:3306`)                   | 3304 | mysql_mini_dashboard      |

---

🎉 **That’s it!**
You can now develop and test your Laravel Mini Dashboard using Docker.
