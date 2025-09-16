# bandwidthd Installation Guide

bandwidthd is a free and open-source bandwidth monitoring. BandwidthD tracks bandwidth usage and provides graphs

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
  - CPU: 1 core minimum
  - RAM: 256MB minimum
  - Storage: 5GB for data
  - Network: HTTP interface
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default bandwidthd port)
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

# Install bandwidthd
sudo dnf install -y bandwidthd

# Enable and start service
sudo systemctl enable --now bandwidthd

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
bandwidthd --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install bandwidthd
sudo apt install -y bandwidthd

# Enable and start service
sudo systemctl enable --now bandwidthd

# Configure firewall
sudo ufw allow 80

# Verify installation
bandwidthd --version
```

### Arch Linux

```bash
# Install bandwidthd
sudo pacman -S bandwidthd

# Enable and start service
sudo systemctl enable --now bandwidthd

# Verify installation
bandwidthd --version
```

### Alpine Linux

```bash
# Install bandwidthd
apk add --no-cache bandwidthd

# Enable and start service
rc-update add bandwidthd default
rc-service bandwidthd start

# Verify installation
bandwidthd --version
```

### openSUSE/SLES

```bash
# Install bandwidthd
sudo zypper install -y bandwidthd

# Enable and start service
sudo systemctl enable --now bandwidthd

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
bandwidthd --version
```

### macOS

```bash
# Using Homebrew
brew install bandwidthd

# Start service
brew services start bandwidthd

# Verify installation
bandwidthd --version
```

### FreeBSD

```bash
# Using pkg
pkg install bandwidthd

# Enable in rc.conf
echo 'bandwidthd_enable="YES"' >> /etc/rc.conf

# Start service
service bandwidthd start

# Verify installation
bandwidthd --version
```

### Windows

```bash
# Using Chocolatey
choco install bandwidthd

# Or using Scoop
scoop install bandwidthd

# Verify installation
bandwidthd --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/bandwidthd

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
bandwidthd --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable bandwidthd

# Start service
sudo systemctl start bandwidthd

# Stop service
sudo systemctl stop bandwidthd

# Restart service
sudo systemctl restart bandwidthd

# Check status
sudo systemctl status bandwidthd

# View logs
sudo journalctl -u bandwidthd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add bandwidthd default

# Start service
rc-service bandwidthd start

# Stop service
rc-service bandwidthd stop

# Restart service
rc-service bandwidthd restart

# Check status
rc-service bandwidthd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'bandwidthd_enable="YES"' >> /etc/rc.conf

# Start service
service bandwidthd start

# Stop service
service bandwidthd stop

# Restart service
service bandwidthd restart

# Check status
service bandwidthd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start bandwidthd
brew services stop bandwidthd
brew services restart bandwidthd

# Check status
brew services list | grep bandwidthd
```

### Windows Service Manager

```powershell
# Start service
net start bandwidthd

# Stop service
net stop bandwidthd

# Using PowerShell
Start-Service bandwidthd
Stop-Service bandwidthd
Restart-Service bandwidthd

# Check status
Get-Service bandwidthd
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream bandwidthd_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name bandwidthd.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name bandwidthd.example.com;

    ssl_certificate /etc/ssl/certs/bandwidthd.example.com.crt;
    ssl_certificate_key /etc/ssl/private/bandwidthd.example.com.key;

    location / {
        proxy_pass http://bandwidthd_backend;
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
    ServerName bandwidthd.example.com
    Redirect permanent / https://bandwidthd.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName bandwidthd.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/bandwidthd.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/bandwidthd.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend bandwidthd_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/bandwidthd.pem
    redirect scheme https if !{ ssl_fc }
    default_backend bandwidthd_backend

backend bandwidthd_backend
    balance roundrobin
    server bandwidthd1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R bandwidthd:bandwidthd /etc/bandwidthd
sudo chmod 750 /etc/bandwidthd

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status bandwidthd

# View logs
sudo journalctl -u bandwidthd -f

# Monitor resource usage
top -p $(pgrep bandwidthd)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/bandwidthd"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/bandwidthd-backup-$DATE.tar.gz" /etc/bandwidthd /var/lib/bandwidthd

echo "Backup completed: $BACKUP_DIR/bandwidthd-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop bandwidthd

# Restore from backup
tar -xzf /backup/bandwidthd/bandwidthd-backup-*.tar.gz -C /

# Start service
sudo systemctl start bandwidthd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u bandwidthd -n 100
sudo tail -f /var/log/bandwidthd/bandwidthd.log

# Check configuration
bandwidthd --version

# Check permissions
ls -la /etc/bandwidthd
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep bandwidthd)

# Check disk I/O
iotop -p $(pgrep bandwidthd)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  bandwidthd:
    image: bandwidthd:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/bandwidthd
      - ./data:/var/lib/bandwidthd
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update bandwidthd

# Debian/Ubuntu
sudo apt update && sudo apt upgrade bandwidthd

# Arch Linux
sudo pacman -Syu bandwidthd

# Alpine Linux
apk update && apk upgrade bandwidthd

# openSUSE
sudo zypper update bandwidthd

# FreeBSD
pkg update && pkg upgrade bandwidthd

# Always backup before updates
tar -czf /backup/bandwidthd-pre-update-$(date +%Y%m%d).tar.gz /etc/bandwidthd

# Restart after updates
sudo systemctl restart bandwidthd
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/bandwidthd

# Clean old logs
find /var/log/bandwidthd -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/bandwidthd
```

## Additional Resources

- Official Documentation: https://docs.bandwidthd.org/
- GitHub Repository: https://github.com/bandwidthd/bandwidthd
- Community Forum: https://forum.bandwidthd.org/
- Best Practices Guide: https://docs.bandwidthd.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
