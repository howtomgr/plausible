# plausible Installation Guide

plausible is a free and open-source privacy-friendly web analytics. Plausible provides simple, privacy-compliant web analytics without cookies, serving as an ethical alternative to Google Analytics

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
  - CPU: 2+ cores recommended
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 10GB for analytics data
  - Network: HTTPS for tracking
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8000 (default plausible port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
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

# Install plausible
sudo dnf install -y plausible

# Enable and start service
sudo systemctl enable --now plausible

# Configure firewall
sudo firewall-cmd --permanent --add-port=8000/tcp
sudo firewall-cmd --reload

# Verify installation
plausible version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install plausible
sudo apt install -y plausible

# Enable and start service
sudo systemctl enable --now plausible

# Configure firewall
sudo ufw allow 8000

# Verify installation
plausible version
```

### Arch Linux

```bash
# Install plausible
sudo pacman -S plausible

# Enable and start service
sudo systemctl enable --now plausible

# Verify installation
plausible version
```

### Alpine Linux

```bash
# Install plausible
apk add --no-cache plausible

# Enable and start service
rc-update add plausible default
rc-service plausible start

# Verify installation
plausible version
```

### openSUSE/SLES

```bash
# Install plausible
sudo zypper install -y plausible

# Enable and start service
sudo systemctl enable --now plausible

# Configure firewall
sudo firewall-cmd --permanent --add-port=8000/tcp
sudo firewall-cmd --reload

# Verify installation
plausible version
```

### macOS

```bash
# Using Homebrew
brew install plausible

# Start service
brew services start plausible

# Verify installation
plausible version
```

### FreeBSD

```bash
# Using pkg
pkg install plausible

# Enable in rc.conf
echo 'plausible_enable="YES"' >> /etc/rc.conf

# Start service
service plausible start

# Verify installation
plausible version
```

### Windows

```bash
# Using Chocolatey
choco install plausible

# Or using Scoop
scoop install plausible

# Verify installation
plausible version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/plausible

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
plausible version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable plausible

# Start service
sudo systemctl start plausible

# Stop service
sudo systemctl stop plausible

# Restart service
sudo systemctl restart plausible

# Check status
sudo systemctl status plausible

# View logs
sudo journalctl -u plausible -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add plausible default

# Start service
rc-service plausible start

# Stop service
rc-service plausible stop

# Restart service
rc-service plausible restart

# Check status
rc-service plausible status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'plausible_enable="YES"' >> /etc/rc.conf

# Start service
service plausible start

# Stop service
service plausible stop

# Restart service
service plausible restart

# Check status
service plausible status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start plausible
brew services stop plausible
brew services restart plausible

# Check status
brew services list | grep plausible
```

### Windows Service Manager

```powershell
# Start service
net start plausible

# Stop service
net stop plausible

# Using PowerShell
Start-Service plausible
Stop-Service plausible
Restart-Service plausible

# Check status
Get-Service plausible
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream plausible_backend {
    server 127.0.0.1:8000;
}

server {
    listen 80;
    server_name plausible.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name plausible.example.com;

    ssl_certificate /etc/ssl/certs/plausible.example.com.crt;
    ssl_certificate_key /etc/ssl/private/plausible.example.com.key;

    location / {
        proxy_pass http://plausible_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName plausible.example.com
    Redirect permanent / https://plausible.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName plausible.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/plausible.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/plausible.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend plausible_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/plausible.pem
    redirect scheme https if !{ ssl_fc }
    default_backend plausible_backend

backend plausible_backend
    balance roundrobin
    server plausible1 127.0.0.1:8000 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R plausible:plausible /etc/plausible
sudo chmod 750 /etc/plausible

# Configure firewall
sudo firewall-cmd --permanent --add-port=8000/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status plausible

# View logs
sudo journalctl -u plausible -f

# Monitor resource usage
top -p $(pgrep plausible)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/plausible"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/plausible-backup-$DATE.tar.gz" /etc/plausible /var/lib/plausible

echo "Backup completed: $BACKUP_DIR/plausible-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop plausible

# Restore from backup
tar -xzf /backup/plausible/plausible-backup-*.tar.gz -C /

# Start service
sudo systemctl start plausible
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u plausible -n 100
sudo tail -f /var/log/plausible/plausible.log

# Check configuration
plausible version

# Check permissions
ls -la /etc/plausible
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8000

# Test connectivity
telnet localhost 8000

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep plausible)

# Check disk I/O
iotop -p $(pgrep plausible)

# Check connections
ss -an | grep 8000
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  plausible:
    image: plausible:latest
    ports:
      - "8000:8000"
    volumes:
      - ./config:/etc/plausible
      - ./data:/var/lib/plausible
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update plausible

# Debian/Ubuntu
sudo apt update && sudo apt upgrade plausible

# Arch Linux
sudo pacman -Syu plausible

# Alpine Linux
apk update && apk upgrade plausible

# openSUSE
sudo zypper update plausible

# FreeBSD
pkg update && pkg upgrade plausible

# Always backup before updates
tar -czf /backup/plausible-pre-update-$(date +%Y%m%d).tar.gz /etc/plausible

# Restart after updates
sudo systemctl restart plausible
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/plausible

# Clean old logs
find /var/log/plausible -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/plausible
```

## Additional Resources

- Official Documentation: https://docs.plausible.org/
- GitHub Repository: https://github.com/plausible/plausible
- Community Forum: https://forum.plausible.org/
- Best Practices Guide: https://docs.plausible.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
