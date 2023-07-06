
# Odoo 16 Installation Ubuntu 20.*

Odoo is a popular open-source suite of business apps that help companies to manage and run their business. It includes a wide range of applications such as CRM, e-Commerce, website builder, billing, accounting, manufacturing, warehouse, project management, inventory, and much more, all seamlessly integrated.

Odoo can be installed in different ways, depending on the use case and available technologies. The easiest and quickest way to install Odoo is by using the official Odoo APT repositories.
Installing Odoo in a virtual environment, or deploying as a Docker container, gives you more control over the application and allows you to run multiple Odoo instances on the same system.
This article goes through installing and deploying Odoo 15 inside a Python virtual environment on Ubuntu 20.04. We’ll download Odoo from the official GitHub repository and use Nginx as a reverse proxy.

## Installing Dependencies

The first step is to install Git , Pip , Node.js , and development [tools required to build]
```bash
  sudo apt update
```
```bash
  sudo apt install git python3-pip build-essential wget python3-dev python3-venv \
    python3-wheel libfreetype6-dev libxml2-dev libzip-dev libldap2-dev libsasl2-dev \
    python3-setuptools node-less libjpeg-dev zlib1g-dev libpq-dev \
    libxslt1-dev libldap2-dev libtiff5-dev libjpeg8-dev libopenjp2-7-dev \
    liblcms2-dev libwebp-dev libharfbuzz-dev libfribidi-dev libxcb1-dev
```

## Creating a System User

Running Odoo under the root user poses a great security risk. We’ll create a new system user and group with home directory /opt/odoo16 that will run the Odoo service. To do so, run the following command:
```bash
  sudo useradd -m -d /opt/odoo16 -U -r -s /bin/bash odoo16
```
You can name the user anything you want, as long you create a PostgreSQL user with the same name.

## Installing and Configuring PostgreSQL

Odoo uses PostgreSQL as the database back-end. PostgreSQL is included in the standard Ubuntu repositories. The installation is straightforward:
```bash
  sudo apt install postgresql
```
Once the service is installed, create a PostgreSQL user with the same name as the previously created system user. In this example, that is odoo16:
```bash
  sudo su - postgres -c "createuser -s odoo16"
```

## Installing wkhtmltopdf

wkhtmltopdf is a set of open-source command-line tools for rendering HTML pages into PDF and various image formats. To print PDF reports in Odoo, you’ll need to install the wkhtmltox package.

The version of wkhtmltopdf that is included in Ubuntu repositories does not support headers and footers. The recommended version for Odoo is version 0.12.5. We’ll download and install the package from Github:
```bash
  sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
```
Once the file is downloaded, install it by typing:
```bash
  sudo apt install ./wkhtmltox_0.12.5-1.bionic_amd64.deb
```

## Installing and Configuring Odoo 16

We’ll install Odoo from the source inside an isolated Python virtual environment .

First, change to user “odoo16”:
```bash
  sudo su - odoo16
```

Clone the Odoo 16 source code from GitHub:
```bash
  git clone https://www.github.com/odoo/odoo --depth 1 --branch 16.0 /opt/odoo16/odoo
```

Create a new Python virtual environment for Odoo:
```bash
cd /opt/odoo16
python3 -m venv odoo-venv
```
Activate the virtual environment:

```bash
source odoo-venv/bin/activate
```
Odoo dependencies are specified in the requirements.txt file. Install all required Python modules with pip3:

```bash
pip3 install wheel
pip3 install -r odoo/requirements.txt
```

Once done, deactivate the environment by typing:
```bash
deactivate
```
We’ll create a new directory a separate directory for the 3rd party addons:
```bash
mkdir /opt/odoo16/odoo-custom-addons
```
Later we’ll add this directory to the addons_path parameter. This parameter defines a list of directories where Odoo searches for modules.
```bash
exit
```
Create a configuration file with the following content:
```bash
sudo nano /etc/odoo16.conf
```
```bash
[options]
; This is the password that allows database operations:
admin_passwd = my_admin_passwd
db_host = False
db_port = False
db_user = odoo16
db_password = False
addons_path = /opt/odoo16/odoo/addons,/opt/odoo16/odoo-custom-addons
logfile = /var/log/odoo/odoo16.log
```
Create Logfile
```besh
sudo mkdir /var/log/odoo
sudo chown odoo16:root /var/log/odoo
```
## Creating Systemd Unit File

A unit file is a configuration ini-style file that holds information about a service.

Open your text editor and create a file named odoo16.service with the following content:


```bash
  sudo nano /etc/systemd/system/odoo16.service
```
```bash
[Unit]
Description=Odoo16
Requires=postgresql.service
After=network.target postgresql.service

[Service]
Type=simple
SyslogIdentifier=odoo16
PermissionsStartOnly=true
User=odoo16
Group=odoo16
ExecStart=/opt/odoo16/odoo-venv/bin/python3 /opt/odoo16/odoo/odoo-bin -c /etc/odoo16.conf
StandardOutput=journal+console

[Install]
WantedBy=multi-user.target

```

Notify systemd that a new unit file exists:
```bash
sudo systemctl daemon-reload
```
Start the Odoo service and enable it to start on boot by running:
```bash
sudo systemctl enable --now odoo16
```
Verify that the service is up and running:
```bash
sudo systemctl status odoo16
```
The output should look something like below, showing that the Odoo service is active and running:
```bash
● odoo16.service - Odoo16
     Loaded: loaded (/etc/systemd/system/odoo16.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-10-26 09:56:28 UTC; 28s ago
...
```
You can check the messages logged by the Odoo service using the command below:

```bash
sudo journalctl -u Odoo16
```
## Testing the Installation
Open your browser and type:
```bash
http://<your_domain_or_IP_address>:8069
```
## Configuring Nginx as SSL Termination Proxy
The default Odoo web server is serving traffic over HTTP. To make the Odoo deployment more secure, we will set Nginx as an SSL termination proxy that will serve the traffic over HTTPS.

SSL termination proxy is a proxy server that handles the SSL encryption/decryption. This means that the termination proxy (Nginx) will process and decrypt incoming TLS connections (HTTPS), and pass on the unencrypted requests to the internal service (Odoo). The traffic between Nginx and Odoo will not be encrypted (HTTP).

Using a reverse proxy gives you a lot of benefits such as Load Balancing, SSL Termination, Caching, Compression, Serving Static Content, and more.

Ensure that you have met the following prerequisites before continuing with this section:

-Domain name pointing to your public server IP. We’ll use example.com.

-Nginx installed .

-SSL certificate for your domain. You can install a free Let’s Encrypt SSL certificate .

Open your text editor and create/edit the domain server block:
```bash
sudo nano /etc/nginx/sites-enabled/example.com.conf
```

The following configuration sets up SSL Termination, HTTP to HTTPS redirection , WWW to non-WWW redirection, cache the static files, and enable GZip compression.
```bash
# Odoo servers
upstream odoo {
 server 127.0.0.1:8069;
}

upstream odoochat {
 server 127.0.0.1:8072;
}

# HTTP -> HTTPS
server {
    listen 80;
    server_name www.example.com example.com;

    include snippets/letsencrypt.conf;
    return 301 https://example.com$request_uri;
}

# WWW -> NON WWW
server {
    listen 443 ssl http2;
    server_name www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
    include snippets/ssl.conf;
    include snippets/letsencrypt.conf;

    return 301 https://example.com$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com;

    proxy_read_timeout 720s;
    proxy_connect_timeout 720s;
    proxy_send_timeout 720s;

    # Proxy headers
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;

    # SSL parameters
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
    include snippets/ssl.conf;
    include snippets/letsencrypt.conf;

    # log files
    access_log /var/log/nginx/odoo.access.log;
    error_log /var/log/nginx/odoo.error.log;

    # Handle longpoll requests
    location /longpolling {
        proxy_pass http://odoochat;
    }

    # Handle / requests
    location / {
       proxy_redirect off;
       proxy_pass http://odoo;
    }

    # Cache static files
    location ~* /web/static/ {
        proxy_cache_valid 200 90m;
        proxy_buffering on;
        expires 864000;
        proxy_pass http://odoo;
    }

    # Gzip
    gzip_types text/css text/less text/plain text/xml application/xml application/json application/javascript;
    gzip on;
}
```
Once you’re done, restart the Nginx service :
```bash
sudo systemctl restart nginx
```
Next, we need to tell Odoo to use the proxy. To do so, open the configuration file and add the following line:
```bash
sudo nano /etc/odoo16.conf
```
add on 
```bash /etc/odoo16.conf file
proxy_mode = True
```

Restart the Odoo service for the changes to take effect:

```bash
sudo systemctl restart odoo16
```

At this point, the reverse proxy is configured, and you can access your Odoo instance at https://example.com.

