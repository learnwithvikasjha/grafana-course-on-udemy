
# Install Nginx
```
sudo yum install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

# Configure Nginx to send traffic to Grafana
```
server {
    listen 443 ssl;
    server_name <your_domain_name>;

    ssl_certificate /etc/nginx/conf.d/<path-to-the-domain-certificate>;
    ssl_certificate_key /etc/nginx/conf.d/<path-to-the-domain-certificate-key>;

    access_log /var/log/nginx/nginx_grafana_access.log;
    error_log /var/log/nginx/nginx_grafana_error.log;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

}
```

# Restart Nginx

```
sudo systemctl restart nginx
```
