# 10panel
Alternative open source hosting panel by 10Corp

# Custom Hosting Control Panel Development

## Summary
Develop a custom hosting control panel using Symfony 7 with a modular architecture, inspired by cPanel’s user-friendly design and ApisCP’s security principles. The panel will manage web hosting, DNS, email, FTP, and security features, integrating with modern tools and services as specified below.

## Description
Build a hosting control panel with the following components, ensuring modularity, scalability, and security. The panel should be intuitive (cPanel-inspired) and secure (ApisCP-inspired) with role-based access control (RBAC), input validation, and audit logging.

### Components and Requirements

| Component | Purpose | Tools/Libraries | Details |
|-----------|---------|-----------------|---------|
| **Web Server** | Serve websites with automatic SSL | Caddy | Use Caddy as the web server. Generate Caddyfile configurations for virtual hosts. Support Let’s Encrypt for automatic SSL. Ensure compatibility with PHP applications (e.g., via `php_fastcgi`). |
| **Panel Database** | Store hosting control panel data (e.g., vhosts, DNS records) | PostgreSQL | Use PostgreSQL for core panel data (e.g., virtual hosts, DNS zones). Ensure schema supports modular services. Implement migrations with Doctrine ORM. |
| **User Database** | Store panel user accounts and preferences | MariaDB | Use MariaDB for user authentication and preferences. Separate from PostgreSQL to isolate user data. Support RBAC for admin and user roles. |
| **Reverse Proxy** | Cache and optimize web traffic | Varnish | Configure Varnish as the default reverse proxy for Caddy. Optimize caching for static assets and dynamic content. Provide admin interface to manage cache policies. |
| **Object Cache** | Cache frequently accessed data | Redis | Use Redis for object caching to improve panel performance (e.g., cache user sessions, DNS queries). Ensure secure connections (e.g., TLS). |
| **Resource Limits** | Restrict user resource usage | cgroups | Implement cgroups to limit CPU, memory, and I/O per user/domain. Provide admin interface to configure limits dynamically. |
| **DNS** | Manage DNS records with external provider integration | BIND, Cloudflare API, AWS Route 53 API | Use BIND for local DNS management. Integrate with Cloudflare and AWS Route 53 for external DNS provider support. Allow users to manage A, CNAME, MX, and TXT records via the panel. |
| **FTP** | File transfer for users | vsftpd | Configure vsftpd for secure FTP access. Support per-user directories and quotas. Integrate with panel user accounts in MariaDB. |
| **MTA (Mail Server)** | Send and receive emails | Postfix, Dovecot | Use Postfix for SMTP and Dovecot for IMAP/POP3. Support virtual mailboxes tied to panel users. Ensure secure connections (TLS). |
| **Spam Filter** | Detect and block spam emails | rspamd | Integrate rspamd for spam and virus scanning. Provide admin interface to view and manage spam reports. |
| **Malware Scanner** | Block infected email attachments | ClamAV | Integrate ClamAV for scanning email attachments and uploaded files. Log and alert on detected threats. |
| **Mail Log Parser** | Detect spam, bulk, or abuse patterns | Custom parser, pflogsumm, or logwatch | Implement a log parser to analyze Postfix/Dovecot logs for abuse patterns (e.g., spam, bulk sending). Provide reports in the panel. |
| **Rate Limiter** | Prevent abuse of email and API | Postfix policy service, Redis | Use Redis-based rate limiting for API endpoints and Postfix policy service for email sending limits. Configure thresholds via the panel. |
| **SSL** | Secure all services | Let’s Encrypt | Use Let’s Encrypt for automatic SSL certificate issuance and renewal for web (Caddy), email (Postfix/Dovecot), and FTP (vsftpd). |
| **Firewall** | Protect server from threats | FirewallD, CrowdSec | Integrate FirewallD for basic firewall rules. Use CrowdSec for behavior-based threat detection and community-driven blocklists. Provide admin interface to manage rules and view blocked IPs. |
| **Panel Backend** | Manage and visualize hosting services | Symfony 7 | Use Symfony 7 for the backend. Implement REST API endpoints for all components (e.g., `/api/vhosts`, `/api/dns`). Ensure RBAC, input validation, and audit logging for all actions. |

### Acceptance Criteria
- [ ] **Web Server**: Caddy serves virtual hosts with correct configurations (domain, document root, SSL). Test with at least two domains, one with SSL enabled.
- [ ] **Panel Database**: PostgreSQL stores virtual host and DNS data with Doctrine ORM. Migrations run without errors.
- [ ] **User Database**: MariaDB stores user accounts with RBAC (admin and user roles). Login and role-based access work as expected.
- [ ] **Reverse Proxy**: Varnish caches content correctly, with configurable policies via the panel.
- [ ] **Object Cache**: Redis caches user sessions and improves panel performance (e.g., < 100ms response time for cached requests).
- [ ] **Resource Limits**: cgroups enforce CPU and memory limits for at least one test user/domain.
- [ ] **DNS**: BIND manages local DNS records. Cloudflare and AWS Route 53 APIs create/update A and MX records successfully.
- [ ] **FTP**: vsftpd allows secure file uploads/downloads for panel users, with quotas enforced.
- [ ] **Email**: Postfix sends and Dovecot receives emails for virtual mailboxes. TLS is enforced.
- [ ] **Spam Filter**: rspamd flags spam emails and provides reports in the panel.
- [ ] **Malware Scanner**: ClamAV detects and blocks infected attachments in emails and FTP uploads.
- [ ] **Mail Log Parser**: Log parser identifies at least one abuse pattern (e.g., bulk email) and displays in the panel.
- [ ] **Rate Limiter**: API and email sending are rate-limited using Redis and Postfix policy service.
- [ ] **SSL**: Let’s Encrypt certificates are issued and renewed automatically for Caddy, Postfix, Dovecot, and vsftpd.
- [ ] **Firewall**: FirewallD blocks unauthorized traffic. CrowdSec detects and blocks malicious IPs, with reports in the panel.
- [ ] **Panel Backend**: Symfony 7 API endpoints are secure (RBAC, input validation) and log all actions (e.g., vhost creation).

### Notes
- **cPanel Inspiration**: Design the panel with a clean, intuitive API that can integrate with a Next.js dashboard. Ensure API responses are structured (e.g., JSON with `id`, `domain`, `createdAt`).
- **ApisCP Security**: Implement RBAC with Symfony’s Security component. Validate all inputs (e.g., domain names with `@Assert\Hostname`). Log all sensitive actions (e.g., vhost creation) to an audit trail.
- **Modularity**: Use Symfony bundles for each component (e.g., `VirtualHostBundle`, `DnsBundle`) to ensure maintainability.
- **Scalability**: Design services to be stateless where possible. Prepare for optional Docker deployment.
- **Testing**: Test all components on a RHEL 10+ environment. Use Postman or cURL for API testing (e.g., `curl -X POST http://localhost/api/vhosts -d '{"domain":"example.com","documentRoot":"/var/www/example.com","sslEnabled":true}'`).

### Priority
High

### Labels
- hosting-panel
- symfony
- caddy
- postgresql
- mariadb
- security
