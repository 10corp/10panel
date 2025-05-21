# 10panel
Alternative open source hosting panel by 10Corp

# Enterprise Hosting Control Panel Development with Blesta Integration

## Summary
Develop an enterprise-grade hosting control panel using **Symfony 7**, inspired by **cPanel**’s user-friendly design and **ApisCP**’s security principles, with built-in **Blesta** for domain management, billing, and ticketing, similar to cPanel’s WHMCS integration. The panel will provide a modular, multi-tenant solution for managing web hosting (using **Nginx** with **PHP-FPM**, defaulting to **PHP 8.4** LTS), databases, email, DNS, FTP, backups, security, and more, optimized for maximum control and performance with unique features like default **Varnish** caching and automatic **cgroups**. Use **HTTP/1.1** for web serving, with no HTTP/2 support.

## Description
Build a hosting control panel with the following out-of-the-box features, ensuring modularity, scalability, and security. The panel must be intuitive (cPanel-inspired) and secure (ApisCP-inspired) with role-based access control (RBAC), input validation, audit logging, and REST API support. Use **Nginx** with **PHP-FPM** (multi-version support, default PHP 8.4 LTS) for web hosting, paired with **Varnish** for HTTP caching and **Redis** for application-level caching. Integrate **Blesta v5+** for domain management, billing, and ticketing with single sign-on (SSO). The project includes 17 epics, covering all aspects of hosting management, from web hosting to QA and documentation.

### Why Varnish + Redis, and Why Nginx with PHP 8.4?
- **Varnish**: Default reverse proxy to cache HTTP responses, reducing Nginx load and speeding up content delivery. Customizable VCL templates enable per-site caching policies, ideal for multi-tenant hosting.
- **Redis**: In-memory object caching for user sessions, API responses, and DNS queries, minimizing database queries for faster panel performance.
- **Nginx**: Chosen for granular configuration (e.g., custom directives, modules) and high performance with HTTP/1.1, ensuring low latency and high throughput for enterprise hosting.
- **PHP 8.4 LTS**: Default for long-term support (3 years active + 2 years security), improved JIT compilation, and better memory management. Multi-version support (7.4, 8.0–8.3) ensures compatibility.
- **Combination**: Nginx, PHP-FPM, Varnish, and Redis form a high-performance, controllable stack for web serving and panel operations, optimized for multi-tenant environments.

## Epics, User Stories, and Tasks

### EPIC: Web Hosting Core
**Summary**: Implement core functionality for web hosting with Nginx, PHP-FPM, Varnish, and Let’s Encrypt, using HTTP/1.1.

**User Stories & Tasks**:
- **Story**: As a user, I want to create a website with Nginx and PHP-FPM so I can host my domain.
  - **Task**: Implement Nginx virtual host auto-generation per domain in `/etc/nginx/conf.d/`.
  - **Task**: Configure PHP-FPM with default PHP 8.4 and multi-version support (7.4, 8.0–8.3).
  - **Task**: Integrate Let’s Encrypt SSL provisioning and renewal.
  - **Task**: Enable Varnish with customizable VCL templates per site.
  - **Task**: Add URL rewrite support (Nginx rewrite rules equivalent to Apache’s `.htaccess`).

**Labels**: web-hosting, nginx, php-fpm, letsencrypt, varnish

### EPIC: Resource Management with cgroups
**Summary**: Implement automatic resource restriction per hosting account using cgroups v2.

**User Stories & Tasks**:
- **Story**: As an admin, I want to enforce resource limits (CPU, RAM) per account to ensure stable multi-tenant hosting.
  - **Task**: Set up cgroups v2 with memory, CPU, and process limits.
  - **Task**: Create Symfony CLI commands to manage cgroups dynamically.
  - **Task**: Auto-assign cgroups to new accounts during creation.

**Labels**: cgroups, resource-limits, multi-tenant, symfony

### EPIC: Database Management
**Summary**: Provide MariaDB for client data and PostgreSQL for panel core.

**User Stories & Tasks**:
- **Story**: As a user, I want to create and manage databases securely via the panel.
  - **Task**: Configure MariaDB for user databases and accounts.
  - **Task**: Set up PostgreSQL for internal panel data (e.g., virtual hosts, DNS records).
  - **Task**: Implement UI for database creation and user access (read-only for clients, full access for admins).
  - **Task**: Enable secure password generation for database users.

**Labels**: mariadb, postgresql, database, symfony

### EPIC: Email Hosting
**Summary**: Enable users to send/receive emails and prevent spam.

**User Stories & Tasks**:
- **Story**: As a user, I want to manage email mailboxes and prevent spam.
  - **Task**: Set up Postfix for SMTP and Dovecot for IMAP/POP3.
  - **Task**: Configure rspamd for spam filtering.
  - **Task**: Add DKIM, SPF, and DMARC setup helpers in the DNS UI.
  - **Task**: Integrate Roundcube webmail for email access.

**Labels**: email, postfix, dovecot, spam-filtering, roundcube

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
**Summary**: Provide secure file transfer options.

**User Stories & Tasks**:
- **Story**: As a user, I want FTP/SFTP access to my files.
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
**Summary**: Harden security with modern tools.

**User Stories & Tasks**:
- **Story**: As an admin, I want to protect servers from attacks.
  - **Task**: Configure FirewallD with predefined zones and rules.
  - **Task**: Integrate CrowdSec with Nginx for brute-force and intrusion protection.
  - **Task**: Add ModSecurity WAF for Nginx.
  - **Task**: Implement 2FA/MFA for panel login and audit logging for all actions.

**Labels**: security, firewall, crowdsec, modsecurity, 2fa

### EPIC: Cron Jobs
**Summary**: Provide scheduled job execution per user.

**User Stories & Tasks**:
- **Story**: As a user, I want to schedule cron jobs via the panel.
  - **Task**: Build UI for cron job management (create, edit, disable).
  - **Task**: Support multiple cron jobs per account using Symfony Scheduler.
  - **Task**: Ensure cron jobs respect cgroups limits.

**Labels**: cron, scheduler, symfony

### EPIC: File Manager
**Summary**: Enable web-based file operations.

**User Stories & Tasks**:
- **Story**: As a user, I want to manage my files from the browser.
  - **Task**: Implement file upload, edit, rename, and delete operations.
  - **Task**: Add permission and ownership editor in the UI.
  - **Task**: Enable remote URL upload/download functionality.

**Labels**: file-manager, symfony, files

### EPIC: Monitoring & Metrics
**Summary**: Show server and usage stats.

**User Stories & Tasks**:
- **Story**: As an admin, I want to view resource usage metrics.
  - **Task**: Integrate Prometheus and Grafana for monitoring.
  - **Task**: Create real-time graphs for CPU, RAM, disk, and bandwidth per account.
  - **Task**: Set up alerting for resource limit breaches and logs viewer for web, mail, and database.

**Labels**: monitoring, grafana, prometheus, metrics

### EPIC: User & Reseller Management
**Summary**: Provide multi-level user and reseller functionality.

**User Stories & Tasks**:
- **Story**: As a reseller, I want to manage my clients and their limits.
  - **Task**: Implement roles: Admin, Reseller, Client with RBAC.
  - **Task**: Add UI for quota management (e.g., disk, bandwidth, accounts).
  - **Task**: Enable multi-language support for the UI.

**Labels**: users, roles, reseller, i18n

### EPIC: Blesta Integration (Domain Management, Billing & Ticketing)
**Summary**: Integrate Blesta with SSO for domain management, billing, and ticketing.

**User Stories & Tasks**:
- **Story**: As a client, I want to manage domain registrations and transfers within the panel.
  - **Task**: Configure Blesta’s domain management module with at least two registrar plugins (e.g., Namecheap, OpenSRS).
  - **Task**: Implement UI for domain registration, transfer, renewal, and WHOIS privacy.
  - **Task**: Integrate with DNS Management epic for DNS record updates (e.g., A, MX, TXT).
- **Story**: As a client, I want to view and pay invoices from the panel.
  - **Task**: Install Blesta v5+ with multi-currency support and 35+ payment gateways (e.g., Stripe, PayPal).
  - **Task**: Implement SSO using Symfony’s Security component.
  - **Task**: Customize Blesta’s client area to match panel branding.
  - **Task**: Create UI for invoice viewing and payment processing.
- **Story**: As a client, I want to submit and track support tickets.
  - **Task**: Configure Blesta’s ticketing system with client and admin views.
  - **Task**: Implement UI for ticket creation and tracking.
  - **Task**: Ensure ticket notifications (email to client and admin).
- **Story**: As an admin, I want to automate account creation and suspension.
  - **Task**: Develop Blesta module to sync accounts with Symfony backend.
  - **Task**: Automate suspension/termination based on unpaid invoices.
  - **Task**: Use Blesta’s cPanel Extended module, adapted for this panel.
- **Story**: As a client, I want one-click app installs.
  - **Task**: Integrate Softaculous with Blesta for app installs (e.g., WordPress).
  - **Task**: Implement UI for Softaculous app installs.
  - **Task**: Ensure app installs respect cgroups and Nginx configs.

**Labels**: blesta, billing, ticketing, domain-management, softaculous, sso, symfony

### EPIC: API Access
**Summary**: Expose full REST API with authentication and permissions.

**User Stories & Tasks**:
- **Story**: As a developer, I want to automate hosting via API.
  - **Task**: Build REST API with Symfony API Platform for all panel features.
  - **Task**: Add JWT authentication with role-based permissions.
  - **Task**: Document API with Swagger/OpenAPI specs.

**Labels**: api, rest, jwt, openapi

### EPIC: DevOps & Deployment
**Summary**: Support modern deployment workflows.

**User Stories & Tasks**:
- **Story**: As a developer, I want to deploy code via Git and use staging environments.
  - **Task**: Add Git deployment via webhook for client sites.
  - **Task**: Create staging environment support for testing.
  - **Task**: Implement optional Docker integration for apps.

**Labels**: git, devops, docker, ci-cd

### EPIC: Additional Features & Branding
**Summary**: Enhance UX and provide white-label support.

**User Stories & Tasks**:
- **Story**: As a provider, I want to brand the panel and simplify SSL management.
  - **Task**: Implement white-label branding and theming for the UI.
  - **Task**: Automate Let’s Encrypt SSL renewal with error handling.
  - **Task**: Plan Softaculous-like one-click app installs via Blesta.

**Labels**: branding, ssl, theming, softaculous

### EPIC: QA, Testing & Documentation
**Summary**: Ensure the system is stable and well-documented.

**User Stories & Tasks**:
- **Story**: As a QA engineer, I want to test features via UI and API.
  - **Task**: Test panel features (hosting, SSL, Blesta SSO, domain management).
  - **Task**: Test API endpoints with Postman/cURL.
  - **Task**: Document system setup and API with OpenAPI/Swagger.
  - **Task**: Create wiki with config guides for Nginx, PHP-FPM, Blesta, PostgreSQL.

**Labels**: testing, qa, swagger, documentation

## Acceptance Criteria
- [ ] **Web Hosting Core**: Nginx serves virtual hosts with PHP-FPM (default PHP 8.4, multi-version support), Varnish caching, and Let’s Encrypt SSL using HTTP/1.1. Test with two domains (one with PHP 8.4 and SSL, one with PHP 8.3 without SSL).
- [ ] **Resource Management**: cgroups v2 limits CPU, memory, and processes for one test account. Symfony commands adjust limits dynamically.
- [ ] **Databases**: PostgreSQL stores panel data; MariaDB stores user data. UI creates/manages databases and users securely.
- [ ] **Email Hosting**: Postfix sends and Dovecot receives emails. rspamd flags spam. DKIM/SPF/DMARC records are configured via UI.
- [ ] **DNS Management**: BIND9 manages local DNS. Cloudflare/AWS Route 53 APIs update A/MX records. DNSSEC is optional.
- [ ] **FTP/SFTP**: vsftpd and OpenSSH provide secure file access with per-user quotas.
- [ ] **Backup & Restore**: Automated and manual backups work for files, databases, and emails. UI restores data successfully.
- [ ] **Security & Firewall**: FirewallD and CrowdSec block malicious traffic. ModSecurity protects Nginx. 2FA/MFA works for login.
- [ ] **Cron Jobs**: UI schedules and edits cron jobs for one user.
- [ ] **File Manager**: UI supports file operations (upload, edit, delete) with permission management.
- [ ] **Monitoring & Metrics**: Real-time graphs show CPU/RAM usage. Alerts trigger on limit breaches. Logs are viewable.
- [ ] **User & Reseller Management**: Admin, Reseller, and Client roles work with quotas. Multi-language UI is supported.
- [ ] **Blesta Integration**: Blesta is integrated with SSO, allowing clients to manage domains, invoices, tickets, and app installs. Test domain registration, multi-currency billing, ticketing, and WordPress install for one client.
- [ ] **API Access**: REST API with JWT authentication manages all features (e.g., `/api/vhosts`, `/api/billing/invoices`).
- [ ] **DevOps & Deployment**: Git pull deploys code. Staging environment works. Docker support is optional.
- [ ] **Additional Features**: Let’s Encrypt renews SSL automatically. UI supports branding/theming. One-click app installs are planned via Blesta.
- [ ] **QA, Testing & Documentation**: All features are tested via UI and API. System setup and API are documented in a wiki and OpenAPI/Swagger.

## Notes
- **cPanel Inspiration**: Design the UI and API for simplicity, with structured JSON responses (e.g., `{"id": 1, "domain": "example.com", "status": "active"}`). Ensure Blesta integration provides seamless SSO, similar to cPanel’s WHMCS. Prepare for a Next.js dashboard.
- **ApisCP Security**: Implement RBAC with Symfony’s Security component (`ROLE_ADMIN`, `ROLE_RESELLER`, `ROLE_CLIENT`). Validate inputs (e.g., `@Assert\Hostname`). Log all actions to an audit trail using Doctrine listeners. Support 2FA/MFA with `scheb/2fa-bundle`.
- **Nginx Configuration**:
  - Use `/etc/nginx/conf.d/` for site-specific configs.
  - Configure PHP-FPM with PHP 8.4 default, supporting 7.4, 8.0–8.3.
  - Integrate ModSecurity for WAF and Varnish for caching, using HTTP/1.1.
- **Blesta Configuration**:
  - Install Blesta v5+ with API integration.
  - Use cPanel Extended module (ModulesGarden) for account automation.
  - Configure registrar modules (e.g., Namecheap, OpenSRS) for domain management.
  - Integrate Softaculous for one-click app installs.
- **Modularity**: Use Symfony bundles (e.g., `VirtualHostBundle`, `BillingBundle`) for maintainability.
- **Scalability**: Design stateless services. Prepare for optional Docker deployment.
- **Testing**: Test on RHEL 10+ with all components. Use Postman/cURL for API testing. Verify PHP 8.4, HTTP/1.1, Blesta SSO, and domain management.
- **Documentation**: Document all configurations in a wiki. Provide OpenAPI/Swagger specs for the API.

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
