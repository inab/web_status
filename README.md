# Web Status Page

This repository contains a static HTML page to be used as a generic **"Service Unavailable"** placeholder for INB/ELIXIR-ES services that are temporarily down or discontinued.

It is a self-contained static page, requiring no backend, and is designed to:

- Display a clear service unavailable message.
- Show the current domain name dynamically.
- Attempt to show a logo specific to the domain.

---

## 💡 How it works

- The hostname is detected via `window.location.hostname`.
- The page tries to load a logo from: logos/hosts/<subdomain>.png

For example:
- `inb-elixir.es` → tries `logos/hosts/elixir.es.png`, etc.
- It uses a `HEAD` request to check whether the logo exists.
- If the logo exists, it is displayed to the right of the status message.
- If not, it is silently hidden.

---

## 🗂 Directory structure
<pre>
.
├── index.html
├── image_service.jpg
└── logos/
    ├── bsc.png
    ├── elixir.png
    └── hosts/
        ├── inb-elixir.es.png
        ├── openebench.bsc.es.png
        └── ...
</pre>

## Hooking this repository to nginx site declaration

These instructions are assuming you are going to use nginx to serve the
domain names which are on hold.

1. First, you have to assure nginx is installed and running.

   ```bash
   sudo apt install nginx
   ```

2. Be sure the nginx logging directory can be written by `www-data` user:

   ```bash
   sudo chown www-data /var/log/nginx
   ```

3. Clone this repo in an appropriate place, giving its ownership to user `www-data`:

   ```bash
   sudo bash -c "cd /var/www ; git clone https://github.com/inab/web_status ; chown -R www-data: web_status"
   ```

4. Create a file at `/etc/nginx/sites-available` with the name of the domain
  (or wildcard domain) which is on hold. File [nginx-site-template](nginx-site-template) can be used as template
  for single domains, meanwhile file [nginx-site-template-wildcard](nginx-site-template-wildcard) is preferred for
  multiple subdomains.

   * For a single domain, you have to change all the occurrences of `THEHOSTNAME` to the real
     domain name to be served:
  
     ```bash
     sudo cp /var/www/web_status/nginx-site-template /etc/nginx/sites-available/platform.eucanimage.eu
     sudo sed -i 's/THEHOSTNAME/platform.eucanimage.eu/g' /etc/nginx/sites-available/platform.eucanimage.eu
     ```

   * For a wildcard domain, you have to change all the occurrences of `BASEDOMAINNAME` to the real
     base domain name to be served:
  
     ```bash
     sudo cp /var/www/web_status/nginx-site-template-wildcard /etc/nginx/sites-available/wildcard_openebench.bsc.es
     sudo sed -i 's/BASEDOMAINNAME/openebench.bsc.es/g' /etc/nginx/sites-available/wildcard_openebench.bsc.es
     ```

5. Create a file with either the name `/etc/nginx/snippets/THEHOSTNAME.conf` (substituting THEHOSTNAME for the DNS entry)
   or `/etc/nginx/snippets/wildcard_BASEDOMAINNAME.conf` (substituting BASEDOMAINNAME for the DNS subdomain),
   holding the common declarations, like SSL certificates and so. This is a sample content of the file:
   
   ```apache
	listen 443 ssl;
	listen [::]:443 ssl;

   ssl_certificate     /etc/nginx/certs/openebench/openebench.bsc.es.pem;
   ssl_certificate_key /etc/nginx/certs/openebench/openebench.bsc.es.decrypt.key;
   ```

6. Create a symlink to the declared site file, check nginx config is right, and restart nginx:

   ```bash
   sudo ln -s ../sites-available/platform.eucanimage.eu /etc/nginx/sites-enabled
   sudo nginx -t && systemctl restart nginx
   ```

   or

   ```bash
   sudo ln -s ../sites-available/wildcard_openebench.bsc.es /etc/nginx/sites-enabled
   sudo nginx -t && systemctl restart nginx
   ```

7. If needed, add the logo(s) for the domain name(s) to `/var/www/web_status/service_down/logos/hosts`.
  In this example, files with either the name `platform.eucanimage.eu.png` or `eucanimage.eu.png`
  would work, taking the first precedence.

## Hooking this repository to Apache site declaration

These instructions are assuming you are going to use Apache 2.4 to serve the
domain names which are on hold.

1. First, you have to assure Apache is installed and running.

   ```bash
   sudo apt install apache2
   ```

2. Clone this repo in an appropriate place, giving its ownership to user `www-data`:

   ```bash
   sudo bash -c "cd /var/www ; git clone https://github.com/inab/web_status ; chown -R www-data: web_status"
   ```

3. Create a file at `/etc/apache2/sites-available` with the name of the domain
  (or wildcard domain) which is on hold , and extension `.conf`. File [apache-site-template.conf](apache-site-template.conf) can be used as template
  for single domains.

   * You have to change all the occurrences of `THEHOSTNAME` to the real
     domain name to be served:
  
     ```bash
     sudo cp /var/www/web_status/apache-site-template.conf /etc/apache2/sites-available/test.inb.bsc.es.conf
     sudo sed -i 's/THEHOSTNAME/test.inb.bsc.es/g' /etc/apache2/sites-available/test.inb.bsc.es.conf
     ```

4. Create a symlink to the declared site file, check Apache config is right, and restart Apache:

   ```bash
   sudo ln -s ../sites-available/test.inb.bsc.es.conf /etc/apache2/sites-enabled
   sudo apache2ctl configtest && systemctl reload apache2
   ```

5. If needed, add the logo(s) for the domain name(s) to `/var/www/web_status/service_down/logos/hosts`.
  In this example, files with either the name `test.inb.bsc.es.png` or `inb.bsc.es.png`
  would work, taking the first precedence.

## 🙌 Acknowledgments

- Barcelona Supercomputing Center (BSC)

- INB / ELIXIR-ES

- And to all the servers that occasionally need a nap 😴
