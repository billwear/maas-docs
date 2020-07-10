MAAS doesn't support TLS encryption natively.  If you are not interested in [setting up an HAProxy](/t/high-availability/804#heading--load-balancing-with-haproxy-optional), you can enable TLS independently in the web server software (e.g. Apache, Nginx) which users access directly.  The examples below explain how to create this configuration.

Note that MAAS doesn't bind to port 80; instead, MAAS binds to port 5240.

<h2 id="heading--nginx">nginx example config</h2>

    server {
     listen 443 ssl;

     server_name _;
     ssl_certificate /etc/nginx/ssl/nginx.crt;
     ssl_certificate_key /etc/nginx/ssl/nginx.key;

     location / {
      proxy_pass http://localhost:5240;
      include /etc/nginx/proxy_params;
     }

     location /MAAS/ws {
      proxy_pass http://localhost:5240/MAAS/ws;
                    proxy_http_version 1.1;
                    proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "Upgrade";
     }
    }

<h2 id="heading--apache2">apache2 example config</h2>

    <VirtualHost *:443>
     SSLEngine On

     SSLCertificateFile /etc/apache2/ssl/apache2.crt
     SSLCertificateKeyFile /etc/apache2/ssl/apache2.key

     RewriteEngine On
            RewriteCond %{REQUEST_URI} ^/MAAS/ws [NC]
            RewriteRule /(.*) ws://localhost:5240/MAAS/ws [P,L]

            ProxyPreserveHost On
            ProxyPass / http://localhost:5240/
            ProxyPassReverse / http://localhost:5240/
    </VirtualHost>