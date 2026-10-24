# NexAxis Randview Data Centre — static website

Self-contained static site: one `index.html`, one CSS file, one JS file, five images,
plus the downloadable Reseller Agreement (`.docx`). No build step, no external libraries.
Cyan theme, scroll transitions, lightbox gallery, Google Maps embed.

## Structure

```
nexaxis-website/
├── index.html
├── assets/
│   ├── css/site.css
│   ├── js/site.js
│   ├── downloads/
│   │   └── NexAxis_Reseller_Agreement_2026-05-12.docx   (Resellers section)
│   └── images/
│       ├── facade.jpg   (hero background + gallery)
│       ├── hero.jpg
│       ├── hall.jpg
│       ├── notice.jpg
│       └── logo.jpeg    (nav/footer/favicon)
└── README.md
```

Sections: Overview · Services · Infrastructure · **Resellers &amp; Partners** (program +
agreement download; pricing kept out of the public page, per owner request) · Gallery · Location.

Quick local preview on any machine:

```bash
# Python (any OS)
python3 -m http.server 8080 -d nexaxis-website
# then open http://localhost:8080
```

---

# Hosting on Apache (Linux Mint)

## 1. Install Apache

```bash
sudo apt update
sudo apt install apache2 -y
```

Apache's default config ships already enabled. Start and enable it:

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
# verify
sudo systemctl status apache2
curl -I http://localhost   # expect: HTTP/1.1 200 OK
```

## 2. Put the site in place

```bash
sudo mkdir -p /var/www/nexaxis
sudo cp -r nexaxis-website/. /var/www/nexaxis/
sudo chown -R www-data:www-data /var/www/nexaxis
sudo chmod -R 755 /var/www/nexaxis
```

Two ways to serve it:

### Option A — as the site root (simplest, this box is single-purpose)

```bash
# back up the Mint default page
sudo cp -a /var/www/html /var/www/html.bak
sudo rm -rf /var/www/html
sudo mv /var/www/nexaxis /var/www/html
```

### Option B — dedicated vhost on a path (keeps default page too)

Create `/etc/apache2/sites-available/nexaxis.conf`:

```apache
<VirtualHost *:80>
    ServerName nexaxis.local
    DocumentRoot /var/www/nexaxis

    <Directory /var/www/nexaxis>
        Options -Indexes +FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/nexaxis_error.log
    CustomLog ${APACHE_LOG_DIR}/nexaxis_access.log combined
</VirtualHost>
```

Enable and reload:

```bash
sudo a2ensite nexaxis
sudo a2dissite 000-default      # optional
sudo systemctl reload apache2
```

## 3. Firewall (if UFW is on)

```bash
sudo ufw allow 'Apache Full'   # or: sudo ufw allow 80/tcp
```

## 4. Verify

```bash
curl -I http://localhost          # 200 OK
curl -I http://127.0.0.1/ assets/js/site.js   # assets resolve
# from another machine on the LAN:
curl -I http://<server-ip>/       # e.g. http://192.168.1.50/
```

## 5. LAN vs public

- **LAN only** (data centre / office): the IP in step 4 is all you need.
- **Public internet**: you need a public IP or domain pointing at this box,
  plus TLS — generate a cert with Let's Encrypt:

```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d randview.nexaxis.co.za
# certbot auto-renews; test renewal with: sudo certbot renew --dry-run
```

Keep the office-grade defaults: don't open port 80 to the internet without
TLS and some rate-limiting / WAF in front (e.g. a small reverse proxy or your
ISP's firewall).

## 6. Updates

Change a file, then:

```bash
sudo cp index.html /var/www/nexaxis/   # whatever changed
sudo systemctl reload apache2          # only needed for config changes
```

Static files need no restart at all.

---

## Content sources

- Facility copy, services list, infrastructure features and photos:
  [DataCenterMap — NexAxis Randview Datacentre](https://www.datacentermap.com/south-africa/johannesburg/nexaxis-randview-datacentre/)
- Photos were pulled to `assets/images/` at build time (the site has no
  hotlinks, so it works even if the source changes).
- Map embed + Street View buttons link to Google Maps for the address
  `1 Avon Rd, Randview Business Park, Ferndale Ext. 9, Randburg 2194`.

Built by Bond, 2026-10-23.
