# Debian DNS (BIND9) & LAMP Server Lab

This repository contains all the configuration files and a step-by-step guide to build a complete server on Debian, featuring:

* **A DNS Server (BIND9):** Configured as a primary server for a custom domain, with a forwarder (to 8.8.8.8), a primary zone, and a reverse lookup zone.
* **A LAMP Stack:**
    * **Linux** (Debian)
    * **Apache2** Web Server
    * **MariaDB** (MySQL) Database
    * **PHP**
* **Multiple Websites:** Configuration for 3 different websites using Apache Virtual Hosts:
    1. A simple HTML site ("Linux forever!").
    2. A dynamic PHP site (showing `phpinfo()`).
    3. A full WordPress blog installation.

---

### Configuration Files

This repository contains the final configuration files. You can find them in the following directories, mimicking the structure of a Linux server:

* `/etc/bind/` - Contains the configuration for the BIND9 DNS server.
* `/etc/apache2/sites-available/` - Contains the Virtual Host files for Apache2.

---

### Lab Guide & Commands

This is a summary of the commands used to build this server (based on my lab notes).

#### 1. DNS Server (BIND9) Setup

1.  **Install BIND9:**
    ```bash
    apt install bind9
    ```
2.  **Configure `named.conf.options`:**
    * Set up a forwarder (e.g., to Google's DNS) to resolve external domains.
    * *See file: `/etc/bind/named.conf.options`*

3.  **Configure `named.conf.local`:**
    * Define the primary forward zone (e.g., `mydomain.osos`).
    * Define the primary reverse zone (e.g., `50.168.192.in-addr.arpa`).
    * *See file: `/etc/bind/named.conf.local`*

4.  **Create Zone Files:**
    * Create the forward zone file `db.mydomain.osos` with A, CNAME, and NS records.
    * Create the reverse zone file `db.192.168.50` with PTR records.
    * *See files in: `/etc/bind/`*

5.  **Test and Restart:**
    ```bash
    systemctl restart bind9
    nslookup www.mydomain.osos
    ```

#### 2. Apache2, PHP & MariaDB (LAMP) Setup

1.  **Install LAMP Stack:**
    ```bash
    apt install apache2
    apt install php libapache2-mod-php
    apt install mariadb-server
    ```

2.  **Configure Site 1 (HTML - "Linux forever!"):**
    * Create directory: `mkdir -p /var/www/linuxforever`
    * Create `index.html`: `echo "Linux forever!" > /var/www/linuxforever/index.html`
    * Create Apache config: `/etc/apache2/sites-available/linuxforever.conf`
    * Enable the site:
        ```bash
        a2ensite linuxforever.conf
        a2dissite 000-default.conf
        systemctl restart apache2
        ```

3.  **Configure Site 2 (PHP - `phpinfo`)**
    * Create directory: `mkdir -p /var/www/mydomain.osos`
    * Create `index.php`: `echo "<?php phpinfo(); ?>" > /var/www/mydomain.osos/index.php`
    * Create Apache config: `/etc/apache2/sites-available/wordpress.conf` (renamed from `ismael.osos.conf`).
    * Enable the site:
        ```bash
        a2ensite wordpress.conf
        systemctl restart apache2
        ```

#### 3. WordPress Installation

1.  **Create Database:**
    ```sql
    sudo mysql -u root
    CREATE DATABASE wordpress;
    CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

2.  **Download & Configure WordPress:**
    * `cd /tmp`
    * `curl -O https://wordpress.org/latest.tar.gz`
    * `tar xzvf latest.tar.gz`
    * `mv /tmp/wordpress/* /var/www/blog.mydomain.osos`
    * *Follow the web-based installation wizard to connect to the database.*
