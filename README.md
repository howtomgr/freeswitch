# FreeSWITCH Installation Guide

FreeSWITCH is a free and open-source Softswitch. Scalable softswitch platform

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 5060/5080 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 5060/5080 (default freeswitch port)
- **Dependencies**:
  - freeswitch-meta-all
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install freeswitch
sudo dnf install -y freeswitch freeswitch-meta-all

# Enable and start service
sudo systemctl enable --now freeswitch

# Configure firewall
sudo firewall-cmd --permanent --add-service=freeswitch
sudo firewall-cmd --reload

# Verify installation
freeswitch --version || systemctl status freeswitch
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install freeswitch
sudo apt install -y freeswitch freeswitch-meta-all

# Enable and start service
sudo systemctl enable --now freeswitch

# Configure firewall
sudo ufw allow 5060/5080

# Verify installation
freeswitch --version || systemctl status freeswitch
```

### Arch Linux

```bash
# Install freeswitch
sudo pacman -S freeswitch

# Enable and start service
sudo systemctl enable --now freeswitch

# Verify installation
freeswitch --version || systemctl status freeswitch
```

### Alpine Linux

```bash
# Install freeswitch
apk add --no-cache freeswitch

# Enable and start service
rc-update add freeswitch default
rc-service freeswitch start

# Verify installation
freeswitch --version || rc-service freeswitch status
```

### openSUSE/SLES

```bash
# Install freeswitch
sudo zypper install -y freeswitch freeswitch-meta-all

# Enable and start service
sudo systemctl enable --now freeswitch

# Configure firewall
sudo firewall-cmd --permanent --add-service=freeswitch
sudo firewall-cmd --reload

# Verify installation
freeswitch --version || systemctl status freeswitch
```

### macOS

```bash
# Using Homebrew
brew install freeswitch

# Start service
brew services start freeswitch

# Verify installation
freeswitch --version
```

### FreeBSD

```bash
# Using pkg
pkg install freeswitch

# Enable in rc.conf
echo 'freeswitch_enable="YES"' >> /etc/rc.conf

# Start service
service freeswitch start

# Verify installation
freeswitch --version || service freeswitch status
```

### Windows

```powershell
# Using Chocolatey
choco install freeswitch

# Or using Scoop
scoop install freeswitch

# Verify installation
freeswitch --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/freeswitch

# Set up basic configuration
sudo tee /etc/freeswitch/freeswitch.conf << 'EOF'
# FreeSWITCH Configuration
max-sessions=1000
EOF

# Test configuration
sudo freeswitch -t || sudo freeswitch configtest

# Reload service
sudo systemctl reload freeswitch
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R freeswitch:freeswitch /etc/freeswitch
sudo chmod 750 /etc/freeswitch

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable freeswitch

# Start service
sudo systemctl start freeswitch

# Stop service
sudo systemctl stop freeswitch

# Restart service
sudo systemctl restart freeswitch

# Reload configuration
sudo systemctl reload freeswitch

# Check status
sudo systemctl status freeswitch

# View logs
sudo journalctl -u freeswitch -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add freeswitch default

# Start service
rc-service freeswitch start

# Stop service
rc-service freeswitch stop

# Restart service
rc-service freeswitch restart

# Check status
rc-service freeswitch status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'freeswitch_enable="YES"' >> /etc/rc.conf

# Start service
service freeswitch start

# Stop service
service freeswitch stop

# Restart service
service freeswitch restart

# Check status
service freeswitch status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start freeswitch
brew services stop freeswitch
brew services restart freeswitch

# Check status
brew services list | grep freeswitch
```

### Windows Service Manager

```powershell
# Start service
net start freeswitch

# Stop service
net stop freeswitch

# Using PowerShell
Start-Service freeswitch
Stop-Service freeswitch
Restart-Service freeswitch

# Check status
Get-Service freeswitch
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/freeswitch/freeswitch.conf << 'EOF'
max-sessions=1000
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart freeswitch
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream freeswitch_backend {
    server 127.0.0.1:5060/5080;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name freeswitch.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name freeswitch.example.com;

    ssl_certificate /etc/ssl/certs/freeswitch.example.com.crt;
    ssl_certificate_key /etc/ssl/private/freeswitch.example.com.key;

    location / {
        proxy_pass http://freeswitch_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName freeswitch.example.com
    Redirect permanent / https://freeswitch.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName freeswitch.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/freeswitch.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/freeswitch.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:5060/5080/
    ProxyPassReverse / http://127.0.0.1:5060/5080/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:5060/5080/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend freeswitch_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/freeswitch.pem
    redirect scheme https if !{ ssl_fc }
    default_backend freeswitch_backend

backend freeswitch_backend
    balance roundrobin
    option httpchk GET /health
    server freeswitch1 127.0.0.1:5060/5080 check
    server freeswitch2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R freeswitch:freeswitch /etc/freeswitch
sudo chmod 750 /etc/freeswitch

# Configure firewall
sudo firewall-cmd --permanent --add-service=freeswitch
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/freeswitch.conf << 'EOF'
[freeswitch]
enabled = true
port = 5060/5080
filter = freeswitch
logpath = /var/log/freeswitch/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/freeswitch.key \
    -out /etc/ssl/certs/freeswitch.crt

# Configure SSL in freeswitch
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE freeswitch_db;
CREATE USER freeswitch_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE freeswitch_db TO freeswitch_user;
EOF

# Configure freeswitch to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE freeswitch_db;
CREATE USER 'freeswitch_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON freeswitch_db.* TO 'freeswitch_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# FreeSWITCH specific tuning
max-sessions=1000
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
freeswitch soft nofile 65535
freeswitch hard nofile 65535
freeswitch soft nproc 32768
freeswitch hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'freeswitch'
    static_configs:
      - targets: ['localhost:5060/5080']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet freeswitch; then
    echo "FreeSWITCH is running"
    exit 0
else
    echo "FreeSWITCH is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/freeswitch << 'EOF'
/var/log/freeswitch/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 freeswitch freeswitch
    postrotate
        systemctl reload freeswitch > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# FreeSWITCH backup script
BACKUP_DIR="/backup/freeswitch"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop freeswitch

# Backup configuration
tar -czf "$BACKUP_DIR/freeswitch-config-$DATE.tar.gz" /etc/freeswitch

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/freeswitch-data-$DATE.tar.gz" /var/lib/freeswitch

# Start service
systemctl start freeswitch

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop freeswitch

# Restore configuration
sudo tar -xzf /backup/freeswitch/freeswitch-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/freeswitch/freeswitch-data-*.tar.gz -C /

# Set permissions
sudo chown -R freeswitch:freeswitch /etc/freeswitch
sudo chown -R freeswitch:freeswitch /var/lib/freeswitch

# Start service
sudo systemctl start freeswitch
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u freeswitch -n 100
sudo tail -f /var/log/freeswitch/*.log

# Check configuration
sudo freeswitch -t || sudo freeswitch configtest

# Check permissions
ls -la /etc/freeswitch
ls -la /var/lib/freeswitch
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 5060/5080
sudo netstat -tlnp | grep 5060/5080

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 5060/5080
nc -zv localhost 5060/5080
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep freeswitch)
htop -p $(pgrep freeswitch)

# Check connections
ss -ant | grep :5060/5080 | wc -l

# Monitor I/O
iotop -p $(pgrep freeswitch)
```

### Debug Mode

```bash
# Run in debug mode
sudo freeswitch -d
# or
sudo freeswitch debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  freeswitch:
    image: freeswitch:latest
    container_name: freeswitch
    ports:
      - "5060/5080:5060/5080"
    volumes:
      - ./config:/etc/freeswitch
      - ./data:/var/lib/freeswitch
    environment:
      - freeswitch_CONFIG=/etc/freeswitch/freeswitch.conf
    restart: unless-stopped
    networks:
      - freeswitch_net

networks:
  freeswitch_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: freeswitch
spec:
  replicas: 3
  selector:
    matchLabels:
      app: freeswitch
  template:
    metadata:
      labels:
        app: freeswitch
    spec:
      containers:
      - name: freeswitch
        image: freeswitch:latest
        ports:
        - containerPort: 5060/5080
        volumeMounts:
        - name: config
          mountPath: /etc/freeswitch
      volumes:
      - name: config
        configMap:
          name: freeswitch-config
---
apiVersion: v1
kind: Service
metadata:
  name: freeswitch
spec:
  selector:
    app: freeswitch
  ports:
  - port: 5060/5080
    targetPort: 5060/5080
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure FreeSWITCH
  hosts: all
  become: yes
  tasks:
    - name: Install freeswitch
      package:
        name: freeswitch
        state: present
    
    - name: Configure freeswitch
      template:
        src: freeswitch.conf.j2
        dest: /etc/freeswitch/freeswitch.conf
        owner: freeswitch
        group: freeswitch
        mode: '0640'
      notify: restart freeswitch
    
    - name: Start and enable freeswitch
      systemd:
        name: freeswitch
        state: started
        enabled: yes
  
  handlers:
    - name: restart freeswitch
      systemd:
        name: freeswitch
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update freeswitch

# Debian/Ubuntu
sudo apt update && sudo apt upgrade freeswitch

# Arch Linux
sudo pacman -Syu freeswitch

# Alpine Linux
apk update && apk upgrade freeswitch

# openSUSE
sudo zypper update freeswitch

# FreeBSD
pkg update && pkg upgrade freeswitch

# Always backup before updates
tar -czf /backup/freeswitch-pre-update-$(date +%Y%m%d).tar.gz /etc/freeswitch

# Restart after updates
sudo systemctl restart freeswitch
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/freeswitch -name "*.log" -mtime +30 -delete

# Verify integrity
sudo freeswitch --verify || sudo freeswitch check

# Update databases (if applicable)
sudo freeswitch-update-db

# Optimize performance
sudo freeswitch-optimize

# Check for security updates
sudo freeswitch --security-check
```

## Additional Resources

- Official Documentation: https://docs.freeswitch.org/
- GitHub Repository: https://github.com/freeswitch/freeswitch
- Community Forum: https://forum.freeswitch.org/
- Wiki: https://wiki.freeswitch.org/
- Comparison vs Asterisk, Kamailio, OpenSIPS, 3CX: https://docs.freeswitch.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
