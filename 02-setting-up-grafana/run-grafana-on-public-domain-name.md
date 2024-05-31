# Accessing Grafana on a Public Domain Name

In this lesson, let's see how can we access Grafana on a public domain name over https securely.

# Routing Traffic to Grafana Using Nginx

This document demonstrates how to route incoming traffic from `grafana.virtuallabs.online` to a Grafana backend running on port 3000 using Nginx.

## Diagram

```mermaid
graph TD
    A[Users] -->|HTTP/HTTPS Request to grafana.virtuallabs.online| B[Cloudflare DNS]
    B -->|A Record (Public IP Address of Grafana) for grafana.virtuallabs.online| C[Nginx Server]
    C -->|Reverse Proxy to port 3000| D[Grafana Backend]
```

# Install Nginx
```
sudo yum install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

# Configure Nginx to send traffic to Grafana
```
# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name <your_domain_name>;

    # Redirect all traffic to HTTPS
    return 301 https://$host$request_uri;
}

# HTTPS server configuration
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
