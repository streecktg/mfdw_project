# mfdw_project
# use included PDF to setup app
# use https://medium.com/@admin_83645/best-way-to-set-up-django-3-with-nginx-unit-and-mariadb-on-centos-7-f23282df1bcd to setup with nginx
- copy the mfdw_root to /usr/share/apps
- config.json
{
        "applications": {
                "tokyodevs": {
                        "type": "python 3.6",
                        "user": "nobody",
                        "processes": 2,
                        "path": "/usr/share/apps/mfdw_site",
                        "home": "/usr/share/apps/oncloudenv",
                        "module": "mfdw_site.wsgi"
                }
        },

        "listeners": {
                "*:8001": {
                        "pass": "applications/tokyodevs"
                }
        }
}

- /etc/nginx/conf.d/streeck.us.conf
upstream unit_backend {server 10.0.0.5:8001;}

server {
    listen 80; server_name 10.0.0.5;
    access_log /var/log/nginx/10.0.0.5.access.log;
    error_log /var/log/nginx/10.0.0.5.error.log;
    root /usr/share/apps/mfdw_site;
    location = /favicon.ico {access_log off; log_not_found off;}
    location /static {root /usr/share/apps/mfdw_site;}
    location / {
        proxy_pass http://unit_backend;
        proxy_set_header Host $host;
    }
    location /.well-known {allow all;}
}

