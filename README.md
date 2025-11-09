# Debian DNS (BIND9) & LAMP Server Lab

This repository contains all the configuration files and a step-by-step guide to build a complete server on Debian, featuring:

* [cite_start]**A DNS Server (BIND9):** Configured as a primary server for a custom domain, with a forwarder (to 8.8.8.8) [cite: 434][cite_start], a primary zone [cite: 420][cite_start], and a reverse lookup zone.
* **A LAMP Stack:**
    * **Linux** (Debian)
    * [cite_start]**Apache2** Web Server [cite: 451]
    * [cite_start]**MariaDB** (MySQL) Database [cite: 509]
    * [cite_start]**PHP** [cite: 474]
* **Multiple Websites:** Configuration for 3 different websites using Apache Virtual Hosts:
    1.  [cite_start]A simple HTML site ("Linux forever!")[cite: 448].
    2.  [cite_start]A dynamic PHP site (showing `phpinfo()`)[cite: 472, 654].
    3.  [cite_start]A full WordPress blog installation[cite: 508].

---

### Configuration Files

This repository contains the final configuration files. You can find them in the following directories, mimicking the structure of a Linux server:

* `/etc/bind/` - Contains the configuration for the BIND9 DNS server.
* `/etc/apache2/sites-available/` - Contains the Virtual Host files for Apache2.

---

### Lab Guide & Commands

This is a summary of the commands used to build this server (based on my lab notes).

#### [cite_start]1. DNS Server (BIND9) Setup [cite: 417]

1.  **Install BIND9:**
    ```bash
    apt install bind9
    ```
2.  **Configure `named.conf.options`:**
    * [cite_start]Set up a forwarder (e.g., to Google's DNS) to resolve external domains.
    * [cite_start]*See file: `/etc/bind/named.conf.options`* 

3.  **Configure `named.conf.local`:**
    * [cite_start]Define the primary forward zone (e.g., `mydomain.osos`).
    * [cite_start]Define the primary reverse zone (e.g., `50.168.192.in-addr.arpa`).
    * [cite_start]*See file: `/etc/bind/named.conf.local`* 

4.  **Create Zone Files:**
    * [cite_start]Create the forward zone file `db.mydomain.osos` with A, CNAME, and NS records.
    * [cite_start]Create the reverse zone file `db.192.168.50` with PTR records[cite: 432].
    * *See files in: `/etc/bind/`*

5.  **Test and Restart:**
    ```bash
    systemctl restart bind9
    nslookup www.mydomain.osos
    ```

#### 2. Apache2, PHP & MariaDB (LAMP) Setup

1.  **Install LAMP Stack:**
    ```bash
    apt install apache2 [cite: 451]
    apt install php libapache2-mod-php [cite: 474]
    apt install mariadb-server [cite: 509]
    ```

2.  **Configure Site 1 (HTML - "Linux forever!"):**
    * [cite_start]Create directory: `mkdir -p /var/www/linuxforever` [cite: 453]
    * Create `index.html`: `echo "Linux forever!" > [cite_start]/var/www/linuxforever/index.html` [cite: 460]
    * [cite_start]Create Apache config: `/etc/apache2/sites-available/linuxforever.conf` 
    * Enable the site:
        ```bash
        [cite_start]a2ensite linuxforever.conf [cite: 465]
        a2dissite 000-default.conf [cite: 466]
        systemctl restart apache2 [cite: 467]
        ```

3.  **Configure Site 2 (PHP - `phpinfo`)**
    * [cite_start]Create directory: `mkdir -p /var/www/mydomain.osos` [cite: 476]
    * [cite_start]Create `index.php`: `echo "<?php phpinfo(); ?>" > /var/www/mydomain.osos/index.php` [cite: 480]
    * [cite_start]Create Apache config: `/etc/apache2/sites-available/wordpress.conf` (renamed from `ismael.osos.conf`)[cite: 482].
    * Enable the site:
        ```bash
        [cite_start]a2ensite wordpress.conf [cite: 484]
        systemctl restart apache2 [cite: 485]
        ```

#### 3. WordPress Installation

1.  **Create Database:**
    ```sql
    sudo mysql -u root
    CREATE DATABASE wordpress; [cite: 512]
    CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'password'; [cite: 516]
    GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost'; [cite: 520]
    FLUSH PRIVILEGES; [cite: 521]
    EXIT; [cite: 522]
    ```

2.  **Download & Configure WordPress:**
    * [cite_start]`cd /tmp` [cite: 523]
    * [cite_start]`curl -O https://wordpress.org/latest.tar.gz` [cite: 525]
    * [cite_start]`tar xzvf latest.tar.gz` [cite: 527]
    * [cite_start]`mv /tmp/wordpress/* /var/www/blog.mydomain.osos` [cite: 531]
    * *Follow the web-based installation wizard to connect to the database.*
