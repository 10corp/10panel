# Enterprise Hosting Control Panel

![Project Status](https://img.shields.io/badge/status-in%20development-orange)
![License](https://img.shields.io/badge/license-MIT-blue)

## Overview
The **Enterprise Hosting Control Panel** is a modular, multi-tenant web hosting management platform built with **Symfony 7**, designed to rival **cPanel** while prioritizing **security** (inspired by **ApisCP**) and **performance**. It integrates **Nginx** as an HTTP/3-only reverse proxy (no HTTP/1.1 or HTTP/2) with **PHP-FPM** (default PHP 8.4 LTS), **Varnish** for caching static content, **Redis** for object caching, and **Blesta** for domain management, billing, and ticketing, offering seamless single sign-on (SSO). The panel supports web hosting, databases, email, DNS, FTP, backups, security, and more, with a focus on automation, scalability, and extensibility.

This project is ideal for hosting providers seeking a customizable, secure, and high-performance control panel for managing client accounts, resellers, and server resources in a multi-tenant environment.

## Key Features
- **Web Hosting Core**:
  - Nginx as HTTP/3-only reverse proxy, proxying static content to Varnish over HTTP/1.1.
  - PHP-FPM with default PHP 8.4 LTS, multi-version support (7.4, 8.0–8.3).
  - Let’s Encrypt SSL with TLS 1.3 for HTTP/3.
  - Varnish caching for static content with HTTP/1.1-compatible VCL templates.
  - URL rewrite support.
- **Resource Management**:
  - Automatic cgroups v2 for per-account CPU, memory, and process limits.
  - Symfony CLI commands for dynamic resource management.
- **Databases**:
  - PostgreSQL for panel data, MariaDB for user accounts.
  - Secure database and user creation via UI.
- **Email Hosting**:
  - Postfix (SMTP) and Dovecot (IMAP/POP3) with rspamd spam filtering.
  - DKIM, SPF, DMARC setup and Roundcube webmail.
- **DNS Management**:
  - BIND9 for local DNS, with Cloudflare and AWS Route 53 API integration.
  - UI for managing A, MX, TXT, CNAME, SRV records; optional DNSSEC.
- **FTP/SFTP**:
  - vsftpd for FTP and OpenSSH for SFTP with per-user quotas.
- **Backup & Restore**:
  - Automated and manual backups for files, databases, and emails.
  - Support for Google Drive, OneDrive, FTP/SFTP targets.
- **Security & Firewall**:
  - FirewallD and CrowdSec for intrusion protection, with UDP port 443 for HTTP/3.
  - ModSecurity WAF for Nginx and 2FA/MFA for login.
  - Secure password policies and audit logging.
- **Cron Jobs**:
  - Per-account cron job scheduler with UI.
- **File Manager**:
  - Web-based file operations (upload, edit, delete) with permission editor.
- **Monitoring & Metrics**:
  - Real-time CPU, RAM, disk, and bandwidth graphs with Prometheus and Grafana.
  - Alerts and logs viewer.
- **User & Reseller Management**:
  - Roles: Admin, Reseller, Client with quotas and multi-language UI.
- **Blesta Integration**:
  - Domain management (registration, transfer, renewal) via registrar modules.
  - Multi-currency billing with 35+ payment gateways.
  - Ticketing system and Softaculous for one-click app installs.
- **API Access**:
  - REST API with JWT authentication over HTTP/3.
- **DevOps & Deployment**:
  - Git deployment with webhooks and staging environments.
  - Optional Docker support for apps.
- **Additional Features**:
  - White-label branding and theming.
  - Automated Let’s Encrypt SSL renewal for HTTP/3.
  - Planned Softaculous-style one-click app installs via Blesta.

### Unique Selling Points
- **HTTP/3-Only Hosting**: Nginx handles client connections over QUIC/UDP, proxying static content to Varnish for caching.
- **Varnish Caching**: Internal HTTP/1.1 caching for static assets, optimized for performance.
- **Automatic cgroups**: Resource limits for stable multi-tenant hosting.
- **Blesta Integration**: Seamless domain management, billing, and ticketing with SSO.
- **Security-First Design**: FirewallD, CrowdSec, ModSecurity, and 2FA, inspired by ApisCP.
- **PHP 8.4 LTS**: Default for long-term support and performance.
- **Modular Architecture**: Symfony bundles for extensibility.

## Project Structure
The project is organized into 17 Jira epics:

| Epic | Objective | Labels |
|------|-----------|--------|
| Web Hosting Core | Nginx as HTTP/3 reverse proxy, PHP-FPM, Varnish, Let’s Encrypt. | web-hosting, nginx, php-fpm, letsencrypt, varnish, http3 |
| Resource Management | Per-account resource limits with cgroups v2. | cgroups, resource-limits, multi-tenant, symfony |
| Database Management | MariaDB for users, PostgreSQL for panel data. | mariadb, postgresql, database, symfony |
| Email Hosting | Postfix, Dovecot, rspamd for email services. | email, postfix, dovecot, spam-filtering, roundcube |
| DNS Management | BIND9, Cloudflare, AWS Route 53 for DNS. | dns, bind9, cloudflare, aws, dns-management |
| FTP/SFTP Access | vsftpd and OpenSSH for file transfer. | ftp, sftp, vsftpd, openssh |
| Backups | Automated/manual backups with restore UI. | backups, restore, scheduler, symfony |
| Security & Firewall | FirewallD, CrowdSec, ModSecurity, 2FA, HTTP/3 support. | security, firewall, crowdsec, modsecurity, 2fa |
| Cron Jobs | Per-user cron job scheduling. | cron, scheduler, symfony |
| File Manager | Web-based file management. | file-manager, symfony, files |
| Monitoring & Metrics | Real-time usage stats with Prometheus/Grafana. | monitoring, grafana, prometheus, metrics |
| User & Reseller Management | Admin, Reseller, Client roles with quotas. | users, roles, reseller, i18n |
| Blesta Integration | Domain management, billing, ticketing with SSO. | blesta, billing, ticketing, domain-management, softaculous, sso |
| API Access | REST API with JWT authentication over HTTP/3. | api, rest, jwt, openapi, http3 |
| DevOps & Deployment | Git, staging, optional Docker support. | git, devops, docker, ci-cd |
| Additional Features & Branding | White-label branding, SSL automation. | branding, ssl, theming, softaculous |
| QA, Testing & Documentation | Feature testing, API documentation, wiki. | testing, qa, swagger, documentation, http3 |

## Setup Instructions
1. **Environment**:
   - Deploy on RHEL 10+ with Nginx 1.25+, PHP-FPM (PHP 8.4), PostgreSQL, MariaDB, Varnish 7+, Redis, and Blesta v5+.
   - Install dependencies: BIND9, vsftpd, OpenSSH, Postfix, Dovecot, rspamd, FirewallD, CrowdSec, ModSecurity, Prometheus, Grafana.
2. **Installation**:
   - Clone the repository: `git clone <repository-url>`.
   - Install Symfony 7 dependencies: `composer install`.
   - Configure Nginx (`/etc/nginx/conf.d/`) for HTTP/3 (UDP port 443), proxying to Varnish (port 6081).
   - Configure Varnish on port 6081 for HTTP/1.1 caching.
   - Set up Blesta with API keys and registrar modules (e.g., Namecheap).
   - Initialize PostgreSQL and MariaDB databases with Doctrine ORM.
3. **Configuration**:
   - Update `.env` for database, API, and Blesta settings.
   - Configure SSO with Symfony’s Security component.
   - Set up FirewallD to allow UDP port 443 for HTTP/3.
   - Configure backup targets (Google Drive, OneDrive, FTP/SFTP).
4. **Testing**:
   - Run UI tests for domain management, billing, and ticketing over HTTP/3.
   - Test API endpoints with Postman/cURL over QUIC.
   - Verify Nginx-to-Varnish proxying, cgroups, and HTTP/3-only operation.

## Contributing
- Follow the Jira epics and tasks in the project board.
- Submit pull requests with clear commit messages referencing Jira issue IDs.
- Adhere to ApisCP-inspired security practices (RBAC, input validation, audit logging).
- Document all changes in the project wiki.

## License
This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
