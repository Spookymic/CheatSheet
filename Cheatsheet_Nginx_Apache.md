# Cheatsheet Nginx / Apache

## Nginx

### Commandes service
| Commande | Effet |
|---|---|
| `nginx -t` | tester la configuration |
| `nginx -s reload` | recharger sans coupure |
| `systemctl start|stop|restart nginx` | gestion du service |
| `systemctl enable nginx` | démarrage au boot |
| `nginx -v` | version |

### Structure
| Chemin | Rôle |
|---|---|
| `/etc/nginx/nginx.conf` | configuration principale |
| `/etc/nginx/sites-available/` | sites disponibles (Debian/Ubuntu) |
| `/etc/nginx/sites-enabled/` | sites actifs (liens) |
| `/etc/nginx/conf.d/*.conf` | configurations annexes |
| `/var/log/nginx/access.log` / `error.log` | logs |

### Virtual host (server block)
```nginx
server {
    listen 80;
    server_name exemple.com www.exemple.com;

    root /var/www/exemple;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location ~* \.(js|css|png|jpg)$ {
        expires 30d;
        add_header Cache-Control "public";
    }
}
```
Activer : `ln -s /etc/nginx/sites-available/exemple /etc/nginx/sites-enabled/` puis `nginx -t && nginx -s reload`.

### Reverse proxy + SSL
```nginx
server {
    listen 443 ssl http2;
    server_name app.exemple.com;

    ssl_certificate     /etc/ssl/certs/app.pem;
    ssl_certificate_key /etc/ssl/private/app.key;
    ssl_protocols       TLSv1.2 TLSv1.3;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

### Directives utiles
| Directive | Rôle |
|---|---|
| `listen 80;` / `listen 443 ssl;` | port d'écoute |
| `server_name` | noms de domaine du vhost |
| `root` / `index` | dossier racine / page d'accueil |
| `location /chemin { ... }` | bloc de règles par chemin |
| `proxy_pass URL` | reverse proxy |
| `return 301 https://$host$request_uri;` | redirection HTTP → HTTPS |
| `client_max_body_size 10m;` | taille max des uploads |
| `gzip on;` | compression |
| `access_log off;` / `error_log` | logs |

**Certbot** (Let's Encrypt) : `sudo certbot --nginx -d exemple.com -d www.exemple.com`.

## Apache

### Commandes service
| Commande | Effet |
|---|---|
| `apachectl -t` | tester la configuration |
| `apachectl graceful` | recharger sans coupure |
| `apachectl -S` | résumé des vhosts |
| `systemctl restart apache2` (Debian) / `httpd` (RHEL) | redémarrer |
| `a2ensite exemple` / `a2dissite exemple` | activer / désactiver un site |
| `a2enmod ssl` / `a2dismod` | activer / désactiver un module |
| `apache2 -v` / `httpd -v` | version |

### Structure
| Chemin | Rôle |
|---|---|
| `/etc/apache2/apache2.conf` | configuration principale (Debian) |
| `/etc/apache2/sites-available/` / `sites-enabled/` | vhosts |
| `/etc/httpd/conf/httpd.conf` | config principale (RHEL) |
| `/var/www/html/` | racine web par défaut |
| `/var/log/apache2/access.log` / `error.log` | logs |

### Virtual host
```apache
<VirtualHost *:80>
    ServerName exemple.com
    ServerAlias www.exemple.com
    DocumentRoot /var/www/exemple

    <Directory /var/www/exemple>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### SSL
```apache
<VirtualHost *:443>
    ServerName exemple.com
    DocumentRoot /var/www/exemple

    SSLEngine on
    SSLCertificateFile    /etc/ssl/certs/app.pem
    SSLCertificateKeyFile /etc/ssl/private/app.key
    SSLCertificateChainFile /etc/ssl/certs/chain.pem

    RewriteEngine on
    RewriteRule ^/api/(.*) http://127.0.0.1:3000/api/$1 [P,L]
</VirtualHost>
```
Redirection HTTP → HTTPS : `Redirect permanent / https://exemple.com/`.

### Modules courants
| Module | Rôle |
|---|---|
| `mod_ssl` | HTTPS |
| `mod_rewrite` | réécriture d'URL |
| `mod_proxy` / `mod_proxy_http` | reverse proxy |
| `mod_headers` / `mod_expires` | en-têtes / cache |
| `mod_php` | PHP |
| `mod_wsgi` | Python (WSGI) |

## Comparaison rapide
| Critère | Nginx | Apache |
|---|---|---|
| Modèle | événementiel (performant) | par processus / MPM |
| .htaccess | non (config centralisée) | oui (par répertoire) |
| Modules | statiques ou dynamiques | larges modules |
| Usage type | reverse proxy, haute charge | sites PHP traditionnels |
| Remarque | fréquent en frontal | traditionnel en partage |

## Dépannage
| Commande | Rôle |
|---|---|
| `nginx -t` / `apachectl -t` | tester la config |
| `tail -f /var/log/nginx/error.log` | logs d'erreur Nginx |
| `tail -f /var/log/apache2/error.log` | logs d'erreur Apache |
| `ss -tlnp \| grep -E '80|443'` | vérifier les ports en écoute |
| `curl -I http://localhost` | tester la réponse HTTP |
| `curl -I -H "Host: exemple.com" http://IP` | tester un vhost précis |
