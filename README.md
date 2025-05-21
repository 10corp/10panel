# Enterprise Hosting Control Panel Development with Blesta Integration

## Summary
Develop an enterprise-grade hosting control panel using **Symfony 7**, inspired by **cPanel**’s user-friendly design and **ApisCP**’s security principles, with **Blesta** for domain management, billing, and ticketing. The panel provides a modular, multi-tenant solution for managing web hosting (using **Nginx** as an HTTP/3-only reverse proxy with **PHP-FPM**, default **PHP 8.4** LTS, **Varnish** caching, and **Phusion Passenger**), databases, email, DNS, FTP, backups, security, system optimization, comprehensive monitoring, and advanced resource management with **Athena**. Enhanced with **ApisCP-inspired features** like synthetic filesystems, secure /tmp, **Rampart** firewall, and **Athena** for optimized performance.

## Description
Build a hosting control panel with out-of-the-box features, ensuring modularity, scalability, and security. The panel must be intuitive (cPanel-inspired) and secure (ApisCP-inspired) with role-based access control (RBAC), input validation, audit logging, and REST API support. Use **Nginx** with **PHP-FPM** (multi-version, optimized source builds) for HTTP/3-only web hosting (no HTTP/1.1 or HTTP/2), proxying static content to **Varnish** over HTTP/1.1. Integrate **Let’s Encrypt** for SSL, **Redis** for caching, **ClamAV** for malware protection, and **Blesta v5+** for domain management, billing, and ticketing with SSO. Enhance with **ApisCP features** (e.g., synthetic filesystems, quota management, tuned profiles), **Rampart** firewall (O(1) ipset, protocol filtering), and **Athena** for CPU/IO weighting, I/O throttling, PID counters, and memory limits across HTTP, IMAP, POP3, FTP, CP, SSH, and subprocesses. Include comprehensive monitoring with automated recovery. The project comprises 18 epics.

### Why Athena with Nginx HTTP/3 and Rampart?
- **Nginx with HTTP/3**: Nginx 1.25+ supports HTTP/3 (QUIC/UDP), proxying static content to Varnish, monitored for QUIC health and page accessibility.
- **Athena Resource Management**: Provides CPU/IO weighting, I/O throttling (bandwidth, IOPS), PID counters with thread ceilings, and memory limits for HTTP, IMAP, POP3, FTP, CP, SSH, and subprocesses, ensuring faster performance and lower load averages.
- **Rampart Firewall**: Uses ipset for O(1) blacklist performance, protocol filtering, automatic ban expiration, and API integration, covering the same services as Athena.
- **Monitoring**: Tracks system load, CPU, memory, storage, services (Nginx, MariaDB, PostgreSQL, Postfix, Dovecot, rspamd, vsftpd, ApisCP), Rampart metrics (blacklists, protocol violations), and Athena metrics (CPU/IO, PID, memory).
- **ApisCP Features**: Synthetic filesystems, secure /tmp, and PAM security ensure robust isolation, monitored for compliance.
- **System Optimization**: tuned, sysctl tuning, and network window optimization enhance performance, monitored for stability.

## Epics, User Stories, and Tasks

### EPIC: Web Hosting Core
**Summary**: Implement high-performance web hosting with Nginx as an HTTP/3-only reverse proxy, PHP-FPM, Varnish caching, Let’s Encrypt SSL, Phusion Passenger, and ApisCP-inspired features.

**User Stories & Tasks**:
- **Story**: As a user, I want to create a website with Nginx and PHP-FPM using HTTP/3, with optimized PHP and multi-language support, so I can host my domain efficiently.
  - **Task**: Implement Nginx virtual host auto-generation in `/etc/nginx/conf.d/` with `http3 on` and UDP port 443, disabling HTTP/1.1 and HTTP/2.
  - **Task**: Configure Nginx as a reverse proxy to forward static content (e.g., `/static`) to Varnish over HTTP/1.1 on port 6081.
  - **Task**: Build optimized PHP 8.4 LTS from source with performance flags (e.g., `--enable-opcache`, `--with-zlib`), supporting versions 7.4, 8.0–8.3.
  - **Task**: Integrate Let’s Encrypt SSL with TLS 1.3 ciphers for HTTP/3.
  - **Task**: Set up Varnish with HTTP/1.1-compatible VCL templates for static content caching.
  - **Task**: Install Phusion Passenger for Ruby, Python, and Node.js application support.
  - **Task**: Configure pyenv, rbenv, goenv, and nvm for multi-version Python, Ruby, Go, and Node.js support.
  - **Task**: Set up bootstrapper components for automated server setup (e.g., package installation, user creation).
  - **Task**: Install RPM channels for custom package repositories (e.g., EPEL, Remi).
  - **Task**: Add URL rewrite support (Nginx rewrite rules).

**Labels**: web-hosting, nginx, php-fpm, letsencrypt, varnish, http3, passenger, apiscp

### EPIC: Resource Management with cgroups
**Summary**: Implement advanced resource restriction with Athena, cgroups v2, quotas, and ApisCP synthetic filesystems.

**User Stories & Tasks**:
- **Story**: As an admin, I want to enforce fine-grained resource limits with CPU/IO weighting, I/O throttling, PID counters, and memory limits for stable multi-tenant hosting.
  - **Task**: Set up Athena for CPU/IO weighting, configuring independent priorities for each site or group via plans.
  - **Task**: Configure Athena I/O throttling for bandwidth and IOPS limits per account.
  - **Task**: Implement Athena PID counters with thread ceilings to prevent resource monopolization.
  - **Task**: Set up Athena memory limits for all user-spawned processes.
  - **Task**: Ensure Athena resource enforcement applies to HTTP (Nginx), IMAP/POP3 (Dovecot), SMTP (Postfix), FTP (vsftpd), CP (control panel), SSH (OpenSSH), and subprocesses.
  - **Task**: Integrate cgroups v2 for basic CPU, memory, and process limits, complementing Athena.
  - **Task**: Implement disk and inode quota management, aligned with Athena throttling.
  - **Task**: Create ApisCP synthetic filesystem for virtualized user environments (e.g., isolated home directories).
  - **Task**: Configure filesystem mount flags (e.g., `noexec`, `nosuid`, `nodev`) for security.
  - **Task**: Create Symfony CLI commands to manage Athena plans, cgroups, quotas, and filesystems dynamically.
  - **Task**: Auto-assign Athena plans, cgroups, and quotas to new accounts.

**Labels**: athena, cgroups, quotas, apiscp-filesystem, resource-limits, multi-tenant, symfony

### EPIC: Database Management
**Summary**: Provide user-defined MariaDB and PostgreSQL distributions for client and panel data.

**User Stories & Tasks**:
- **Story**: As a user, I want to create and manage databases with custom MariaDB/PostgreSQL versions securely via the panel.
  - **Task**: Support user-defined MariaDB distributions (e.g., 10.6, 11.x) with custom repository configuration.
  - **Task**: Support user-defined PostgreSQL distributions (e.g., 15, 16) with custom repository configuration.
  - **Task**: Implement UI for database creation, user access, and version selection (read-only for clients, full access for admins).
  - **Task**: Enable secure password generation for database users.

**Labels**: mariadb, postgresql, database, symfony

### EPIC: Email Hosting
**Summary**: Enable email services with Postfix, Dovecot, and rspamd, monitored and protected by Rampart and Athena.

**User Stories & Tasks**:
- **Story**: As a user, I want to manage email mailboxes and prevent spam securely, with monitored and resource-controlled services.
  - **Task**: Set up Postfix for SMTP and Dovecot for IMAP/POP3.
  - **Task**: Configure rspamd for spam filtering, replacing SpamAssassin.
  - **Task**: Add DKIM, SPF, and DMARC setup helpers in the DNS UI.
  - **Task**: Integrate Roundcube webmail for email access.

**Labels**: email, postfix, dovecot, rspamd, roundcube

### EPIC: DNS Management
**Summary**: Let users manage DNS records locally and via APIs.

**User Stories & Tasks**:
- **Story**: As a user, I want to manage DNS records via the panel UI.
  - **Task**: Set up BIND9 with DNS zone templates for domains.
  - **Task**: Implement DNS record editor for A, AAAA, MX, TXT, CNAME, SRV records.
  - **Task**: Integrate Cloudflare and AWS Route 53 APIs for external DNS management.
  - **Task**: Add optional DNSSEC support.

**Labels**: dns, bind9, cloudflare, aws, dns-management

### EPIC: FTP/SFTP Access
**Summary**: Provide secure file transfer options, protected by Rampart and controlled by Athena.

**User Stories & Tasks**:
- **Story**: As a user, I want FTP/SFTP access to my files securely with resource limits.
  - **Task**: Configure vsftpd with user isolation for FTP access.
  - **Task**: Enable OpenSSH for SFTP access.
  - **Task**: Implement per-user quotas and permission settings via the backend.

**Labels**: ftp, sftp, vsftpd, openssh

### EPIC: Backups
**Summary**: Ensure reliable backup/restore across cloud and FTP.

**User Stories & Tasks**:
- **Story**: As a user, I want automated and manual backups for my data.
  - **Task**: Implement Symfony scheduler for automated backups of files, databases, and emails.
  - **Task**: Support Google Drive, OneDrive, and FTP/SFTP as backup targets.
  - **Task**: Create UI for manual backup triggers and restore operations.

**Labels**: backups, restore, scheduler, symfony

### EPIC: Security & Firewall
**Summary**: Harden security with Rampart, firewalld, fail2ban, ClamAV, PAM, and secure /tmp.

**User Stories & Tasks**:
- **Story**: As an admin, I want to protect servers from attacks and malware with high-performance firewalling.
  - **Task**: Configure Rampart with ipset for O(1) blacklist performance, covering HTTP, IMAP, POP3, SMTP, FTP, CP, and SSH.
  - **Task**: Set up Rampart protocol filtering to block abusive clients based on non-sensical protocol exchanges.
  - **Task**: Configure Rampart automatic expiration for temporary bans and permanent blacklists for repeat offenders.
  - **Task**: Integrate Rampart API (`rampart_ban`, `rampart_unban`) with the control panel’s REST API.
  - **Task**: Configure firewalld with zones and rules, allowing UDP port 443 for HTTP/3 and supporting Rampart’s ipset.
  - **Task**: Set up fail2ban with whitelisting for SSH, Nginx, and Dovecot as a complementary tool.
  - **Task**: Install ClamAV with malware scrubbing for file uploads and email attachments.
  - **Task**: Configure PAM security for authentication hardening (e.g., password policies, session limits).
  - **Task**: Secure /tmp with a separate tmpfs mount (e.g., `noexec`, `nosuid`, `nodev`).
  - **Task**: Integrate CrowdSec with Nginx for distributed threat intelligence (optional).

**Labels**: security, rampart, firewalld, fail2ban, clamav, pam, tmp-security, crowdsec

### EPIC: System Optimization & Security
**Summary**: Optimize system performance and security with tuned, watchdog, NSS caching, /etc journaling, sysctl tuning, and network window optimization.

**User Stories & Tasks**:
- **Story**: As an admin, I want to optimize server performance and monitor system health securely.
  - **Task**: Configure tuned with hosting-specific profiles (e.g., `throughput-performance`) for CPU and I/O optimization.
  - **Task**: Set up watchdog for system health monitoring and automatic recovery from crashes.
  - **Task**: Implement NSS caching with nscd for faster name service lookups.
  - **Task**: Enable /etc journaling for tracking configuration changes (e.g., using etckeeper).
  - **Task**: Apply sysctl tuning for performance (e.g., `vm.swappiness=10`, `fs.file-max=2097152`).
  - **Task**: Optimize network window for TCP performance (e.g., `net.ipv4.tcp_rmem`, `net.ipv4.tcp_wmem`).

**Labels**: optimization, tuned, watchdog, nss-caching, journaling, sysctl, network

### EPIC: Monitoring & Metrics
**Summary**: Provide comprehensive monitoring for system resources, services, Rampart, and Athena with automated recovery.

**User Stories & Tasks**:
- **Story**: As an admin, I want to monitor system and service health with automated recovery for stable hosting.
  - **Task**: Integrate Prometheus and Grafana for real-time monitoring.
  - **Task**: Monitor system load (run-queue depth), CPU %, memory (physical + swap), and storage space (physical + inode).
  - **Task**: Monitor Nginx (HTTP/3) process health and page accessibility (e.g., HTTP 200 response).
  - **Task**: Monitor MariaDB process, connections, query performance, and connection deadlocks.
  - **Task**: Monitor PostgreSQL process, connections, and query performance.
  - **Task**: Monitor Postfix (SMTP) and Dovecot (IMAP/POP3) service and queue status.
  - **Task**: Monitor rspamd spam filtering service.
  - **Task**: Monitor vsftpd FTP service and connections.
  - **Task**: Monitor ApisCP control panel services (Symfony backend, API, cron jobs).
  - **Task**: Monitor /tmp flags (e.g., `noexec`, `nosuid`) for security compliance.
  - **Task**: Monitor Rampart blacklist size, protocol violation detections, and service-specific ban events.
  - **Task**: Monitor Athena CPU/IO weighting, I/O bandwidth/IOPS usage, PID counts, thread ceiling violations, and memory usage per account.
  - **Task**: Implement runaway system load detection with automated mitigation (e.g., reduce process priorities).
  - **Task**: Set up Argos recovers for automated service restarts (Nginx, MariaDB, PostgreSQL, Postfix, Dovecot, rspamd, vsftpd, ApisCP) on failure.
  - **Task**: Configure alerts for resource limit breaches, service failures, and Rampart/Athena events.
  - **Task**: Create logs viewer for web, mail, database, Rampart, and Athena activities.

**Labels**: monitoring, grafana, prometheus, metrics, rampart, athena

### EPIC: Cron Jobs
**Summary**: Provide scheduled job execution per user with Athena resource limits.

**User Stories & Tasks**:
- **Story**: As a user, I want to schedule cron jobs via the panel with resource controls.
  - **Task**: Build UI for cron job management (create, edit, disable).
  - **Task**: Support multiple cron jobs per account using Symfony Scheduler.
  - **Task**: Ensure cron jobs respect Athena CPU/IO, PID, and memory limits.

**Labels**: cron, scheduler, symfony, athena

### EPIC: File Manager
**Summary**: Enable web-based file operations with Athena resource controls.

**User Stories & Tasks**:
- **Story**: As a user, I want to manage my files from the browser with resource limits.
  - **Task**: Implement file upload, edit, rename, and delete operations.
  - **Task**: Add permission and ownership editor in the UI.
  - **Task**: Enable remote URL upload/download functionality.

**Labels**: file-manager, symfony, files, athena

### EPIC: User & Reseller Management
**Summary**: Provide multi-level user and reseller functionality with Athena plans.

**User Stories & Tasks**:
- **Story**: As a reseller, I want to manage my clients and their resource limits.
  - **Task**: Implement roles: Admin, Reseller, Client with RBAC.
  - **Task**: Add UI for quota and Athena plan management (e.g., disk, bandwidth, CPU/IO weights).
  - **Task**: Enable multi-language support for the UI.

**Labels**: users, roles, reseller, i18n, athena

### EPIC: Blesta Integration (Domain Management, Billing & Ticketing)
**Summary**: Integrate Blesta with SSO for domain management, billing, and ticketing.

**User Stories & Tasks**:
- **Story**: As a client, I want to manage domain registrations and transfers within the panel.
  - **Task**: Configure Blesta’s domain management module with at least two registrar plugins (e.g., Namecheap, OpenSRS).
  - **Task**: Implement UI for domain registration, transfer, renewal, and WHOIS privacy.
  - **Task**: Integrate with DNS Management epic for DNS record updates.
- **Story**: As a client, I want to view and pay invoices from the panel.
  - **Task**: Install Blesta v5+ with multi-currency support and 35+ payment gateways.
  - **Task**: Implement SSO using Symfony’s Security component.
  - **Task**: Customize Blesta’s client area to match panel branding.
  - **Task**: Create UI for invoice viewing and payment processing.
- **Story**: As a client, I want to submit and track support tickets.
  - **Task**: Configure Blesta’s ticketing system with client and admin views.
  - **Task**: Implement UI for ticket creation and tracking.
  - **Task**: Ensure ticket notifications (email to client and admin).
- **Story**: As an admin, I want to automate account creation and suspension.
  - **Task**: Develop Blesta module to sync accounts with Symfony backend and Athena plans.
  - **Task**: Automate suspension/termination based on unpaid invoices.
  - **Task**: Use Blesta’s cPanel Extended module, adapted for this panel.
- **Story**: As a client, I want one-click app installs.
  - **Task**: Integrate Softaculous with Blesta for app installs (e.g., WordPress).
  - **Task**: Implement UI for Softaculous app installs.
  - **Task**: Ensure app installs respect Athena resource limits and Nginx configs.

**Labels**: blesta, billing, ticketing, domain-management, softaculous, sso, symfony, athena

### EPIC: API Access
**Summary**: Expose full REST API with authentication, including Rampart and Athena management.

**User Stories & Tasks**:
- **Story**: As a developer, I want to automate hosting, firewall, and resource management via API.
  - **Task**: Build REST API with Symfony API Platform for all panel features, including Rampart (`rampart_ban`, `rampart_unban`) and Athena (plan configuration), supporting HTTP/3.
  - **Task**: Add JWT authentication with role-based permissions.
  - **Task**: Document API with Swagger/OpenAPI specs.

**Labels**: api, rest, jwt, openapi, http3, rampart, athena

### EPIC: DevOps & Deployment
**Summary**: Support modern deployment workflows with Athena resource controls.

**User Stories & Tasks**:
- **Story**: As a developer, I want to deploy code via Git and use staging environments with resource limits.
  - **Task**: Add Git deployment via webhook for client sites.
  - **Task**: Create staging environment support for testing.
  - **Task**: Implement optional Docker integration for apps, respecting Athena limits.

**Labels**: git, devops, docker, ci-cd, athena

### EPIC: Additional Features & Branding
**Summary**: Enhance UX and provide white-label support.

**User Stories & Tasks**:
- **Story**: As a provider, I want to brand the panel and simplify SSL management.
  - **Task**: Implement white-label branding and theming for the UI.
  - **Task**: Automate Let’s Encrypt SSL renewal with HTTP/3-compatible error handling.
  - **Task**: Plan Softaculous-like one-click app installs via Blesta.

**Labels**: branding, ssl, theming, softaculous

### EPIC: QA, Testing & Documentation
**Summary**: Ensure the system is stable and well-documented, including Rampart and Athena.

**User Stories & Tasks**:
- **Story**: As a QA engineer, I want to test features via UI and API, including firewall and resource management.
  - **Task**: Test panel features (hosting, SSL, Blesta SSO, domain management, Rampart, Athena) over HTTP/3.
  - **Task**: Test API endpoints (including `rampart_ban`, Athena plans) with Postman/cURL, ensuring HTTP/3 compatibility.
  - **Task**: Document system setup and API with OpenAPI/Swagger.
  - **Task**: Create wiki with config guides for Nginx, PHP-FPM, Varnish, Blesta, ApisCP features, Rampart, and Athena.

**Labels**: testing, qa, swagger, documentation, http3, rampart, athena

## Acceptance Criteria
- [ ] **Web Hosting Core**: Nginx serves HTTP/3-only virtual hosts with PHP-FPM (optimized PHP 8.4), Varnish caching via HTTP/1.1 proxy, Let’s Encrypt SSL, Phusion Passenger, pyenv/rbenv/goenv/nvm, bootstrapper components, and RPM channels. Test with two domains (one with PHP 8.4 and SSL, one with Ruby app via Passenger).
- [ ] **Resource Management**: Athena enforces CPU/IO weighting, I/O throttling (bandwidth, IOPS), PID counters, memory limits for HTTP, IMAP, POP3, FTP, CP, SSH, and subprocesses. cgroups v2, disk/inode quotas, ApisCP synthetic filesystem, and secure mount flags applied. Test resource limits and filesystem isolation for one account.
- [ ] **Databases**: User-defined MariaDB and PostgreSQL distributions supported. UI manages databases with version selection.
- [ ] **Email Hosting**: Postfix and Dovecot handle email with rspamd, DKIM/SPF/DMARC, and Roundcube. Monitored and resource-controlled by Athena/Rampart.
- [ ] **DNS Management**: BIND9 manages local DNS. Cloudflare/AWS Route 53 APIs update records. DNSSEC optional.
- [ ] **FTP/SFTP**: vsftpd and OpenSSH provide secure file access with Athena quotas and Rampart protection.
- [ ] **Backup & Restore**: Automated/manual backups work for files, databases, and emails with Google Drive/OneDrive/FTP targets.
- [ ] **Security & Firewall**: Rampart enforces O(1) blacklists, protocol filtering, automatic ban expiration, and API integration for HTTP, IMAP, POP3, SMTP, FTP, CP, SSH. firewalld allows UDP port 443. fail2ban (complementary), ClamAV, PAM, secure /tmp, and CrowdSec (optional) configured.
- [ ] **System Optimization**: tuned, watchdog, NSS caching, /etc journaling, sysctl tuning, and network window optimization applied.
- [ ] **Monitoring & Metrics**: Monitors system load, CPU, memory, storage, /tmp flags, Nginx, MariaDB (deadlocks), PostgreSQL, Postfix, Dovecot, rspamd, vsftfd, ApisCP, Rampart (blacklists, protocol violations), and Athena (CPU/IO, PID, memory). Argos recovers services on failure. Alerts and logs viewer implemented.
- [ ] **Cron Jobs**: UI schedules cron jobs with Athena limits.
- [ ] **File Manager**: UI supports file operations with Athena limits.
- [ ] **User & Reseller Management**: Admin, Reseller, Client roles with Athena plan management and multi-language UI.
- [ ] **Blesta Integration**: Blesta manages domains, billing, ticketing, and Softaculous app installs with SSO and Athena limits.
- [ ] **API Access**: REST API with JWT supports all features, including Rampart and Athena, over HTTP/3.
- [ ] **DevOps & Deployment**: Git deployment, staging, and optional Docker with Athena limits.
- [ ] **Additional Features**: White-label branding, automated SSL, and planned Softaculous installs.
- [ ] **QA, Testing & Documentation**: All features tested via UI/API over HTTP/3. System setup, Rampart, Athena, and API documented in wiki and OpenAPI/Swagger.

## Notes
- **cPanel Inspiration**: Design UI with Tailwind CSS for cPanel-like simplicity. Ensure Blesta SSO mimics cPanel’s WHMCS. Prepare for Next.js dashboard.
- **ApisCP Security**: Use Symfony’s Security component for RBAC (`ROLE_ADMIN`, `ROLE_RESELLER`, `ROLE_CLIENT`). Validate inputs (e.g., `@Assert\Hostname`). Log actions with Doctrine listeners. Support 2FA/MFA with `scheb/2fa-bundle`.
- **Nginx Configuration**:
  - Use `/etc/nginx/conf.d/` with `http3 on`, `listen 443 quic`, no HTTP/1.1 or HTTP/2.
  - Proxy static content to Varnish on port 6081 (e.g., `proxy_pass http://127.0.0.1:6081`).
  - Use TLS 1.3 ciphers (e.g., `ssl_protocols TLSv1.3;`).
- **Varnish Configuration**:
  - Listen on port 6081, using HTTP/1.1 for Nginx proxy.
  - Use VCL templates to cache static content with appropriate TTLs.
- **Athena Configuration**:
  - Configure plans for CPU/IO weighting, I/O throttling, PID counters, and memory limits.
  - Apply to HTTP, IMAP, POP3, FTP, CP, SSH, and subprocesses.
  - Integrate with Symfony CLI for dynamic management.
- **Rampart Configuration**:
  - Use ipset for O(1) blacklists, protocol filtering, and automatic expiration.
  - Integrate `rampart_ban`, `rampart_unban` with REST API.
  - Cover HTTP, IMAP, POP3, SMTP, FTP, CP, SSH.
- **Blesta Configuration**:
  - Install Blesta v5+ with API, registrar modules (e.g., Namecheap), and Softaculous.
  - Use cPanel Extended module (ModulesGarden) for account automation.
- **HTTP/3 Considerations**:
  - Ensure firewalld allows UDP port 443 for QUIC.
  - Test HTTP/3 with `curl --http3` or Chrome (QUIC protocol).
  - Verify Varnish caching and Athena limits for HTTP/3 traffic.
- **Modularity**: Use Symfony bundles (e.g., `VirtualHostBundle`, `ResourceBundle`) for maintainability.
- **Scalability**: Design stateless services. Support optional Docker deployment.
- **Testing**: Test on RHEL 10+ with Nginx 1.25+, PHP 8.4, Varnish 7+, Rampart, Athena. Verify HTTP/3-only, Rampart bans, Athena limits, Blesta SSO, and domain management. Simulate malicious traffic for Rampart and resource overuse for Athena.
- **Documentation**: Document Nginx, Varnish, Athena, Rampart, PHP-FPM, Blesta, and PostgreSQL in wiki. Provide OpenAPI/Swagger specs for API.

## Priority
High

## Labels
- hosting-panel
- symfony
- nginx
- php-8.4
- postgresql
- mariadb
- blesta
- security
- multi-tenant
- http3
- varnish
- rampart
- athena
