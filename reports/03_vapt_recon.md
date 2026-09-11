# VAPT: Reconnaissance & Vulnerability Assessment

## Part 1: Nmap Service/Version Scan

**Target:** scanme.nmap.org (45.33.32.156) — Nmap's official public test server, authorized for scanning
**Command:** `nmap -sV -T4 scanme.nmap.org`

| Port | State | Service | Version |
|---|---|---|---|
| 22/tcp | open | SSH | OpenSSH 6.6.1p1 (Ubuntu) |
| 80/tcp | open | HTTP | Apache 2.4.7 |
| 135/tcp | filtered | MSRPC | - |
| 139/tcp | filtered | NetBIOS-SSN | - |
| 445/tcp | filtered | Microsoft-DS | - |
| 9929/tcp | open | Nping-echo | - |
| 31337/tcp | open | tcpwrapped | unidentified |

### Findings
1. **Outdated SSH service (OpenSSH 6.6.1p1, ~2014-era Ubuntu)** — old
   software versions may carry known CVEs; recommend version audit.
2. **Outdated Apache (2.4.7)** — same concern.
3. **Port 31337 open, service unidentified ("tcpwrapped")** — historically
   associated with backdoor/trojan activity (e.g. Back Orifice); would
   warrant manual investigation in a real assessment.
4. **Filtered SMB-related ports (135/139/445)** — firewall blocking direct
   probing; can't confirm true state.

## Part 2: Nmap NSE Vulnerability Scan

**Target:** php.testinvicti.com (107.20.213.223) — Invicti's public authorized security-testing site
**Command:** `nmap -sV --script vuln php.testinvicti.com`

### Findings & Risk Ratings

| Finding | CVSS-style Risk | Details |
|---|---|---|
| Outdated Apache 2.2.8 / PHP 5.2.6 stack | High | Both are long-EOL versions with multiple known CVEs; no longer receive security patches |
| Slowloris DoS vulnerability (CVE-2007-6750) | Medium | Server vulnerable to slow-HTTP denial-of-service attacks |
| HTTP TRACE method enabled | Low–Medium | Can be used in Cross-Site Tracing (XST) attacks to bypass HttpOnly cookie protections |
| Directory listing / sensitive path exposure (.svn, phpinfo.php) | High | Exposes source control metadata and full PHP configuration (info disclosure) |
| Overly permissive cross-domain policy | Medium | Misconfigured crossdomain.xml could allow unauthorized cross-origin requests |

### Mitigation Recommendations
- Upgrade Apache and PHP to current supported versions
- Disable TRACE method (`TraceEnable off` in Apache config)
- Remove/restrict access to `.svn` directories and `phpinfo.php` in production
- Tighten `crossdomain.xml` policy to explicit trusted domains only
- Apply rate-limiting/timeout protections against Slowloris-style attacks (e.g. mod_reqtimeout)

## Tooling Note
Nikto was installed and configured (Strawberry Perl, XML::Writer module) but
encountered a tool-level compatibility bug on this Perl/Windows combination
(`Argument "..." isn't numeric in numeric ne` at nikto.pl line 155). Confirmed
this was not a network/DNS/firewall issue — direct Perl HTTP requests
(LWP::Simple) and curl both connected successfully to the same targets.
Nmap's NSE vuln scripts were used as a substitute vulnerability scanner.