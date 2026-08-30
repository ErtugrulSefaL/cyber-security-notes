---
Main Topic: Network_Security
Sub Topic: Passive_Reconnaissance
Links:
  - https://tryhackme.com/room/passiverecon
  - https://www.shodan.io
Date: 21-03-2026
Related Notes:
Tags:
---

# Passive_Reconnaissance

## Command-line tools:

| Tool       | Purpose                | Usage                                 |
| ---------- | ---------------------- | ------------------------------------- |
| `whois`    | to query WHOIS servers | `whois DOMAIN_NAME`                   |
| `nslookup` | to query DNS servers   | `nslookup OPTIONS DOMAIN_NAME SERVER` |
| `dig`      | to query DNS servers   | `dig @SERVER DOMAIN_NAME TYPE`        |

| Query type | Result             |
| ---------- | ------------------ |
| A          | IPv4 Addresses     |
| AAAA       | IPv6 Addresses     |
| CNAME      | Canonical Name     |
| MX         | Mail Servers       |
| SOA        | Start of Authority |
| TXT        | TXT Records        |

> [!NOTE] Servers
> Cloudflare offers `1.1.1.1` and `1.0.0.1`, Google offers `8.8.8.8` and `8.8.4.4`, and Quad9 offers `9.9.9.9` and `149.112.112.112`


## Online Tools

- [DNSDumpster](https://dnsdumpster.com/)
- [Shodan.io (opens in new tab)](https://www.shodan.io/)