# wazuh-ossec Installation Guide

wazuh-ossec is a free and open-source host intrusion detection. OSSEC provides host-based intrusion detection system

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 10GB for logs
  - Network: Agent protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 1514 (default wazuh-ossec port)
  - Auth on 1515
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

# Install wazuh-ossec
sudo dnf install -y wazuh_ossec

# Enable and start service
sudo systemctl enable --now wazuh-ossec

# Configure firewall
sudo firewall-cmd --permanent --add-port=1514/tcp
sudo firewall-cmd --reload

# Verify installation
wazuh-ossec --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install wazuh-ossec
sudo apt install -y wazuh_ossec

# Enable and start service
sudo systemctl enable --now wazuh-ossec

# Configure firewall
sudo ufw allow 1514

# Verify installation
wazuh-ossec --version
```

### Arch Linux

```bash
# Install wazuh-ossec
sudo pacman -S wazuh_ossec

# Enable and start service
sudo systemctl enable --now wazuh-ossec

# Verify installation
wazuh-ossec --version
```

### Alpine Linux

```bash
# Install wazuh-ossec
apk add --no-cache wazuh_ossec

# Enable and start service
rc-update add wazuh-ossec default
rc-service wazuh-ossec start

# Verify installation
wazuh-ossec --version
```

### openSUSE/SLES

```bash
# Install wazuh-ossec
sudo zypper install -y wazuh_ossec

# Enable and start service
sudo systemctl enable --now wazuh-ossec

# Configure firewall
sudo firewall-cmd --permanent --add-port=1514/tcp
sudo firewall-cmd --reload

# Verify installation
wazuh-ossec --version
```

### macOS

```bash
# Using Homebrew
brew install wazuh_ossec

# Start service
brew services start wazuh_ossec

# Verify installation
wazuh-ossec --version
```

### FreeBSD

```bash
# Using pkg
pkg install wazuh_ossec

# Enable in rc.conf
echo 'wazuh-ossec_enable="YES"' >> /etc/rc.conf

# Start service
service wazuh-ossec start

# Verify installation
wazuh-ossec --version
```

### Windows

```bash
# Using Chocolatey
choco install wazuh_ossec

# Or using Scoop
scoop install wazuh_ossec

# Verify installation
wazuh-ossec --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/wazuh_ossec

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
wazuh-ossec --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable wazuh-ossec

# Start service
sudo systemctl start wazuh-ossec

# Stop service
sudo systemctl stop wazuh-ossec

# Restart service
sudo systemctl restart wazuh-ossec

# Check status
sudo systemctl status wazuh-ossec

# View logs
sudo journalctl -u wazuh-ossec -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add wazuh-ossec default

# Start service
rc-service wazuh-ossec start

# Stop service
rc-service wazuh-ossec stop

# Restart service
rc-service wazuh-ossec restart

# Check status
rc-service wazuh-ossec status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'wazuh-ossec_enable="YES"' >> /etc/rc.conf

# Start service
service wazuh-ossec start

# Stop service
service wazuh-ossec stop

# Restart service
service wazuh-ossec restart

# Check status
service wazuh-ossec status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start wazuh_ossec
brew services stop wazuh_ossec
brew services restart wazuh_ossec

# Check status
brew services list | grep wazuh_ossec
```

### Windows Service Manager

```powershell
# Start service
net start wazuh-ossec

# Stop service
net stop wazuh-ossec

# Using PowerShell
Start-Service wazuh-ossec
Stop-Service wazuh-ossec
Restart-Service wazuh-ossec

# Check status
Get-Service wazuh-ossec
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream wazuh_ossec_backend {
    server 127.0.0.1:1514;
}

server {
    listen 80;
    server_name wazuh_ossec.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name wazuh_ossec.example.com;

    ssl_certificate /etc/ssl/certs/wazuh_ossec.example.com.crt;
    ssl_certificate_key /etc/ssl/private/wazuh_ossec.example.com.key;

    location / {
        proxy_pass http://wazuh_ossec_backend;
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
    ServerName wazuh_ossec.example.com
    Redirect permanent / https://wazuh_ossec.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName wazuh_ossec.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/wazuh_ossec.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/wazuh_ossec.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:1514/
    ProxyPassReverse / http://127.0.0.1:1514/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend wazuh_ossec_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/wazuh_ossec.pem
    redirect scheme https if !{ ssl_fc }
    default_backend wazuh_ossec_backend

backend wazuh_ossec_backend
    balance roundrobin
    server wazuh_ossec1 127.0.0.1:1514 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R wazuh_ossec:wazuh_ossec /etc/wazuh_ossec
sudo chmod 750 /etc/wazuh_ossec

# Configure firewall
sudo firewall-cmd --permanent --add-port=1514/tcp
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
sudo systemctl status wazuh-ossec

# View logs
sudo journalctl -u wazuh-ossec -f

# Monitor resource usage
top -p $(pgrep wazuh_ossec)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/wazuh_ossec"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/wazuh_ossec-backup-$DATE.tar.gz" /etc/wazuh_ossec /var/lib/wazuh_ossec

echo "Backup completed: $BACKUP_DIR/wazuh_ossec-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop wazuh-ossec

# Restore from backup
tar -xzf /backup/wazuh_ossec/wazuh_ossec-backup-*.tar.gz -C /

# Start service
sudo systemctl start wazuh-ossec
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u wazuh-ossec -n 100
sudo tail -f /var/log/wazuh_ossec/wazuh_ossec.log

# Check configuration
wazuh-ossec --version

# Check permissions
ls -la /etc/wazuh_ossec
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 1514

# Test connectivity
telnet localhost 1514

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep wazuh_ossec)

# Check disk I/O
iotop -p $(pgrep wazuh_ossec)

# Check connections
ss -an | grep 1514
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  wazuh_ossec:
    image: wazuh_ossec:latest
    ports:
      - "1514:1514"
    volumes:
      - ./config:/etc/wazuh_ossec
      - ./data:/var/lib/wazuh_ossec
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update wazuh_ossec

# Debian/Ubuntu
sudo apt update && sudo apt upgrade wazuh_ossec

# Arch Linux
sudo pacman -Syu wazuh_ossec

# Alpine Linux
apk update && apk upgrade wazuh_ossec

# openSUSE
sudo zypper update wazuh_ossec

# FreeBSD
pkg update && pkg upgrade wazuh_ossec

# Always backup before updates
tar -czf /backup/wazuh_ossec-pre-update-$(date +%Y%m%d).tar.gz /etc/wazuh_ossec

# Restart after updates
sudo systemctl restart wazuh-ossec
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/wazuh_ossec

# Clean old logs
find /var/log/wazuh_ossec -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/wazuh_ossec
```

## Additional Resources

- Official Documentation: https://docs.wazuh_ossec.org/
- GitHub Repository: https://github.com/wazuh_ossec/wazuh_ossec
- Community Forum: https://forum.wazuh_ossec.org/
- Best Practices Guide: https://docs.wazuh_ossec.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
