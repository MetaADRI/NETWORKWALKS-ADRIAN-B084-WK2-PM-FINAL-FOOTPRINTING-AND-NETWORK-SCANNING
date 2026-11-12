# Week 2 Project: Footprinting and Network Scanning

**Networkwalks Cybersecurity Program | W2-PM-FINAL**

Hi, I'm Adrian (Bwalya Adrian Mange). This repo has my Week 2 project for the Networkwalks cybersecurity program. In this project I did two things: I footprinted the Networkwalks website using six Kali Linux tools, and I scanned my own home network with Zenmap to see what devices were alive on it.

The full write-up is the Word file in this repo: **[W2-PM-FINAL-Penetration-Testing-Report.docx](W2-PM-FINAL-Penetration-Testing-Report.docx)**. This README is just the quick version, so you don't have to open the report to get the idea.

| | |
|---|---|
| **Name** | Bwalya Adrian Mange |
| **Program / Batch** | B084 |
| **Report date** | 12 November 2026 |
| **Modules completed** | W2-PM1 (Footprinting with multiple Kali tools) and W2-PM5 (Network scanning with Zenmap) |

## Before anything else: scope

This is a learning project, and I only tested the targets the project instructions gave me:

- **networkwalks.com**, the public website and its DNS records
- **My own home network** (192.168.1.0/24)

Nothing else was touched. I only used passive or light tools and a ping scan. I did not try to exploit anything, guess passwords, or attack any service.

## Part 1: Footprinting networkwalks.com (W2-PM1)

I ran these from a Kali Linux VM on 12 November 2026. Each one shows a different piece of the picture, and put together they give you a decent profile of the site without ever attacking it.

| Tool | What I used it for | What I found |
|---|---|---|
| `whois` | Who owns the domain and who hosts it | Registered with GoDaddy, owner hidden behind a privacy service, name servers point to HostGator |
| `whatweb` | What technology the site runs | Apache, WordPress with the Download Manager plugin, jQuery, versions visible |
| `nslookup` | Domain to IP address | Resolves to 192.232.216.135 |
| `curl -I` | HTTP response headers | WordPress API address advertised, a few security headers missing |
| `wafw00f` | Is there a web application firewall? | Yes, ModSecurity |
| `dnsrecon` | DNS records | Mail server, SPF record, cPanel records, and the DNS software version is visible |

### Things I noticed

- Exact software versions show up in several places (whatweb and dnsrecon). That helps an attacker look for known bugs, and it also helps a defender check what needs updating.
- Some security headers were not in the response I checked: HSTS, Content-Security-Policy, X-Frame-Options and X-Content-Type-Options.
- Both name servers show their BIND version, and DNSSEC is not turned on.
- The SPF record ends with `~all` (soft fail), and I did not check for DMARC in this run.
- Good things too: there is a WAF, the owner details are private, the domain has registrar locks, and HTTP redirects to HTTPS.

The report has a proper findings table with ratings and recommendations. The ratings are my own opinion, since I did not exploit or confirm anything.

## Part 2: Scanning my own network with Zenmap (W2-PM5)

I ran Zenmap (Nmap 7.993) on my Windows PC on 11 November 2026, with the Ping scan profile:

```
nmap -sn 192.168.1.0/24
```

**Result: 256 addresses scanned, 6 hosts up.**

| # | IP address | MAC address (partly hidden) | Vendor |
|---|---|---|---|
| 1 | 192.168.1.1 | 74:24:9F:XX:XX:XX | Tibro (my router) |
| 2 | 192.168.1.21 | 74:D8:3E:XX:XX:XX | Intel Corporate |
| 3 | 192.168.1.130 | F0:E4:A2:XX:XX:XX | Huawei Technologies |
| 4 | 192.168.1.187 | 5C:E0:C5:XX:XX:XX | Intel Corporate |
| 5 | 192.168.1.239 | A4:02:B9:XX:XX:XX | Intel Corporate |
| 6 | 192.168.1.210 | A4:34:D9:XX:XX:XX | Intel Corporate (my own PC) |

My own PC is the last one. Nmap does not list a MAC for the machine it runs on, so I got mine from `ipconfig /all`.

Here is the topology map Zenmap drew, with my PC (localhost) in the middle:

![Zenmap topology](screenshots/09-zenmap-topology.png)

## What is in this repo

```
.
├── README.md
├── W2-PM-FINAL-Penetration-Testing-Report.docx   <- the full report
├── screenshots/        <- screenshots of every tool's output
├── text-outputs/       <- the saved text output of each Kali command
└── zenmap/             <- scan file and topology PDF from Zenmap
```

## A note on privacy

This repo is public, so I covered some details before uploading:

- The **MAC addresses** of the devices on my home network are covered with orange boxes in the screenshots, and the last three parts are hidden (`XX:XX:XX`) everywhere else. The first three parts only show the manufacturer.
- One **cookie value** and one **Google verification token** from the website's output are covered or replaced with `[REDACTED]`.
- The Zenmap scan file in this repo is a cleaned copy with the MAC addresses partly hidden.

Everything about networkwalks.com (IP address, name servers, headers) is public information that comes straight from the tools.

## What I took away from this

Before this project I knew footprinting was "the first step", but it was a different thing to see how much a site gives away without anyone attacking it. None of these tools broke anything. They just read what was already public. That is the reason defenders should run the same tools on their own systems, so they can see what an attacker would see and close the gaps.

## Disclaimer

This project is for learning only. Please do not run these tools on systems you do not own or do not have written permission to test. In most places, unauthorized scanning or access is a crime, even if nothing gets damaged.
