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

5. Create a symlink to the declared site file, check nginx config is right, and restart nginx:

   ```bash
   sudo ln -s ../sites-available/platform.eucanimage.eu /etc/nginx/sites-enabled
   sudo nginx -t && systemctl restart nginx
   ```

   or

   ```bash
   sudo ln -s ../sites-available/wildcard_openebench.bsc.es /etc/nginx/sites-enabled
   sudo nginx -t && systemctl restart nginx
   ```

6. If needed, add the logo(s) for the domain name(s) to `/var/www/web_status/service_down/logos/hosts`.
  In this example, files with either the name `platform.eucanimage.eu.png` or `eucanimage.eu.png`
  would work, taking the first precedence.

## 🙌 Acknowledgments

- Barcelona Supercomputing Center (BSC)

- INB / ELIXIR-ES

- And to all the servers that occasionally need a nap 😴
