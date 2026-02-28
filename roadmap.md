# Roadmap Belajar: Linux System Administration & Networking

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [github.com/Bangkah](https://github.com/Bangkah)  
**Versi:** 1.0 | Maret 2026

---

**Tujuan:** Menguasai fundamental Operating System dan Networking untuk menjadi System Administrator atau DevOps Engineer yang kompeten.

---

## Overview

| Bagian | Topik | Estimasi |
|--------|-------|----------|
| I | Fundamental Operating System (Bab 1-6) | 3 minggu |
| II | Fundamental Networking (Bab 7-11) | 2 minggu |
| III | Integrasi OS + Networking (Bab 12-15) | 2 minggu |

**Total Estimasi:** 7 minggu (dengan praktik intensif)

---

## Daftar Isi

- [Bagian I — Fundamental Operating System](#bagian-i--fundamental-operating-system)
  - [Bab 1 — Apa Itu Operating System](#bab-1--apa-itu-operating-system)
  - [Bab 2 — Struktur Sistem Linux](#bab-2--struktur-sistem-linux)
  - [Bab 3 — Process Management](#bab-3--process-management)
  - [Bab 4 — Permission & Security Dasar](#bab-4--permission--security-dasar)
  - [Bab 5 — System Service & systemd](#bab-5--system-service--systemd)
  - [Bab 6 — File System & Disk](#bab-6--file-system--disk)
- [Bagian II — Fundamental Networking](#bagian-ii--fundamental-networking)
  - [Bab 7 — Dasar Jaringan Komputer](#bab-7--dasar-jaringan-komputer)
  - [Bab 8 — IP Address & Subnetting](#bab-8--ip-address--subnetting)
  - [Bab 9 — TCP, UDP & Port](#bab-9--tcp-udp--port)
  - [Bab 10 — DNS & Routing](#bab-10--dns--routing)
  - [Bab 11 — Firewall & NAT](#bab-11--firewall--nat)
- [Bagian III — Integrasi OS + Networking](#bagian-iii--integrasi-os--networking)
  - [Bab 12 — Server Exposure](#bab-12--server-exposure)
  - [Bab 13 — SSH & Remote Access](#bab-13--ssh--remote-access)
  - [Bab 14 — Logging & Monitoring](#bab-14--logging--monitoring)
  - [Bab 15 — Mini Project Final](#bab-15--mini-project-final)

---

# BAGIAN I — FUNDAMENTAL OPERATING SYSTEM

**Goal:** Memahami cara kerja Operating System dari level kernel hingga user space.

---

## Bab 1 — Apa Itu Operating System

**File:** [Bab1_Apa_Itu_Operating_System.md](Bab1_Apa_Itu_Operating_System.md)

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Definisi OS | Pengertian, fungsi, dan jenis-jenis OS | ⬜ |
| Kernel vs User Space | Ring protection, privilege levels | ⬜ |
| Cara kerja program dijalankan | Compile, link, load, execute | ⬜ |
| System call | Interface ke kernel, syscall mechanism | ⬜ |

**Tools yang dipelajari:**
- `strace` - trace system calls
- `uname` - system information
- `/proc` filesystem

**Estimasi waktu:** 4-5 jam

---

## Bab 2 — Struktur Sistem Linux

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Filesystem Hierarchy | Standar struktur direktori Linux | ⬜ |
| Direktori penting | Fungsi masing-masing direktori | ⬜ |

**Struktur Direktori:**

```
/
├── bin/      → Binary executable (user commands)
├── sbin/     → System binaries (admin commands)
├── etc/      → Configuration files
├── home/     → User home directories
├── var/      → Variable data (logs, cache, spool)
├── usr/      → User programs & data
├── proc/     → Virtual filesystem (process info)
└── dev/      → Device files
```

**Studi Kasus:**
- Kenapa config ada di `/etc`?

**Estimasi waktu:** 3-4 jam

---

## Bab 3 — Process Management

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| PID | Process identification | ⬜ |
| Parent & Child Process | Hierarki proses, fork() | ⬜ |
| Daemon | Background processes | ⬜ |
| Zombie Process | Orphan & zombie handling | ⬜ |

**Tools yang dipelajari:**

| Tool | Fungsi |
|------|--------|
| `ps` | Snapshot proses |
| `top` | Realtime process monitor |
| `htop` | Interactive process viewer |
| `kill` | Send signals to process |
| `nice` / `renice` | Process priority |

**Estimasi waktu:** 4-5 jam

---

## Bab 4 — Permission & Security Dasar

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| rwx | Read, Write, Execute permissions | ⬜ |
| Numeric mode | 755 vs 644 | ⬜ |
| chmod | Change file mode | ⬜ |
| chown | Change file owner | ⬜ |
| sudo | Superuser do | ⬜ |
| Least Privilege | Security principle | ⬜ |

**Permission Matrix:**

```
         Owner    Group    Others
         -----    -----    ------
Read       4        4         4
Write      2        2         2
Execute    1        1         1
         -----    -----    ------
755:       7        5         5     (rwxr-xr-x)
644:       6        4         4     (rw-r--r--)
```

**Estimasi waktu:** 3-4 jam

---

## Bab 5 — System Service & systemd

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Daemon | Apa itu daemon dan cara kerjanya | ⬜ |
| systemctl | Service management | ⬜ |
| Enable/Disable | Auto-start configuration | ⬜ |
| journalctl | System logs analysis | ⬜ |

**Commands Penting:**

```bash
# Service management
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl status <service>
systemctl enable <service>
systemctl disable <service>

# Log analysis
journalctl -u <service>
journalctl -f              # follow mode
journalctl --since "1 hour ago"
```

**Estimasi waktu:** 3-4 jam

---

## Bab 6 — File System & Disk

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Mount | Mounting filesystems | ⬜ |
| Partition | Disk partitioning | ⬜ |
| df, du | Disk usage analysis | ⬜ |
| ext4 | Linux filesystem | ⬜ |
| swap | Virtual memory | ⬜ |

**Commands Penting:**

```bash
df -h           # Disk free (human readable)
du -sh *        # Directory sizes
lsblk           # List block devices
mount           # Show mounted filesystems
fdisk -l        # Partition information
```

**Estimasi waktu:** 4-5 jam

---

# BAGIAN II — FUNDAMENTAL NETWORKING

> **Goal:** Memahami konsep jaringan komputer dari physical layer hingga application layer.

---

## Bab 7 — Dasar Jaringan Komputer

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Apa itu jaringan | Konsep dasar networking | ⬜ |
| LAN vs WAN | Network scope | ⬜ |
| OSI vs TCP/IP | Network models | ⬜ |

**Model Perbandingan:**

```
    OSI Model              TCP/IP Model
┌─────────────────┐    ┌─────────────────┐
│ 7. Application  │    │                 │
├─────────────────┤    │   Application   │
│ 6. Presentation │    │                 │
├─────────────────┤    ├─────────────────┤
│ 5. Session      │    │   Transport     │
├─────────────────┤    ├─────────────────┤
│ 4. Transport    │    │   Internet      │
├─────────────────┤    ├─────────────────┤
│ 3. Network      │    │                 │
├─────────────────┤    │   Network       │
│ 2. Data Link    │    │   Access        │
├─────────────────┤    │                 │
│ 1. Physical     │    │                 │
└─────────────────┘    └─────────────────┘
```

**Estimasi waktu:** 3-4 jam

---

## Bab 8 — IP Address & Subnetting

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| IPv4 | 32-bit addressing | ⬜ |
| CIDR | Classless Inter-Domain Routing | ⬜ |
| Subnet mask | Network/host separation | ⬜ |
| Network & Broadcast | Address calculation | ⬜ |
| Private vs Public IP | Address ranges | ⬜ |

**Private IP Ranges:**

| Class | Range | CIDR |
|-------|-------|------|
| A | 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| B | 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| C | 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

**Estimasi waktu:** 4-5 jam

---

## Bab 9 — TCP, UDP & Port

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| 3-way handshake | TCP connection establishment | ⬜ |
| Socket | Network endpoint | ⬜ |
| Port binding | Service-port association | ⬜ |
| Port conflict | Kenapa satu port tidak bisa dipakai 2 service | ⬜ |

**Well-Known Ports:**

| Port | Service | Protocol |
|------|---------|----------|
| 22 | SSH | TCP |
| 80 | HTTP | TCP |
| 443 | HTTPS | TCP |
| 53 | DNS | TCP/UDP |
| 25 | SMTP | TCP |

**Estimasi waktu:** 3-4 jam

---

## Bab 10 — DNS & Routing

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Cara kerja DNS | Name resolution process | ⬜ |
| DNS Records | A, CNAME, MX, dll | ⬜ |
| Default route | Gateway configuration | ⬜ |
| Traceroute | Path discovery | ⬜ |
| Gateway | Network gateway concept | ⬜ |

**Commands Penting:**

```bash
dig example.com           # DNS lookup
nslookup example.com      # Name server lookup
host example.com          # Simple DNS lookup
traceroute example.com    # Trace route
ip route                  # Show routing table
```

**Estimasi waktu:** 4-5 jam

---

## Bab 11 — Firewall & NAT

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| UFW | Uncomplicated Firewall | ⬜ |
| iptables | Advanced firewall | ⬜ |
| NAT | Network Address Translation | ⬜ |
| Port forwarding | Redirect traffic | ⬜ |

**UFW Quick Reference:**

```bash
ufw enable                 # Enable firewall
ufw status                 # Check status
ufw allow 22/tcp           # Allow SSH
ufw allow 80/tcp           # Allow HTTP
ufw deny 23/tcp            # Deny Telnet
ufw delete allow 80/tcp    # Remove rule
```

**Estimasi waktu:** 4-5 jam

---

# BAGIAN III — INTEGRASI OS + NETWORKING

> **Goal:** Mengintegrasikan pengetahuan OS dan networking untuk build real-world server.

---

## Bab 12 — Server Exposure

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| Nginx Installation | Web server setup | ⬜ |
| Open port 80 | Firewall configuration | ⬜ |
| Reverse proxy | Proxy configuration | ⬜ |

**Basic Nginx Setup:**

```bash
# Install
sudo apt install nginx

# Start & enable
sudo systemctl start nginx
sudo systemctl enable nginx

# Allow firewall
sudo ufw allow 'Nginx Full'

# Test
curl http://localhost
```

**Estimasi waktu:** 3-4 jam

---

## Bab 13 — SSH & Remote Access

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| OpenSSH | SSH server & client | ⬜ |
| SSH key | Key-based authentication | ⬜ |
| Hardening SSH | Security best practices | ⬜ |
| Fail2ban | Brute-force protection | ⬜ |

**SSH Hardening Checklist:**

- [ ] Disable root login
- [ ] Use key-based auth only
- [ ] Change default port (optional)
- [ ] Limit user access
- [ ] Install fail2ban
- [ ] Use strong key algorithms

**Estimasi waktu:** 4-5 jam

---

## Bab 14 — Logging & Monitoring

| Topik | Deskripsi | Status |
|-------|-----------|--------|
| /var/log | Log file locations | ⬜ |
| journalctl | Systemd journal | ⬜ |
| Analisis error | Troubleshooting | ⬜ |

**Important Log Files:**

| File | Deskripsi |
|------|-----------|
| `/var/log/syslog` | System messages |
| `/var/log/auth.log` | Authentication logs |
| `/var/log/nginx/access.log` | Nginx access |
| `/var/log/nginx/error.log` | Nginx errors |

**Estimasi waktu:** 3-4 jam

---

## Bab 15 — Mini Project Final

### Objective

Bangun server production-ready dengan semua komponen yang sudah dipelajari.

### Requirements Checklist

| Komponen | Deskripsi | Status |
|----------|-----------|--------|
| Ubuntu Server | Base OS installation | ⬜ |
| Firewall aktif | UFW configured | ⬜ |
| SSH secure | Key-based + hardened | ⬜ |
| Nginx running | Web server operational | ⬜ |
| Domain + SSL | HTTPS dengan Let's Encrypt | ⬜ |
| IDS sederhana | Suricata untuk intrusion detection | ⬜ |

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    INTERNET                                 │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │      FIREWALL       │
               │       (UFW)         │
               │   Port 22, 80, 443  │
               └──────────┬──────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │     SURICATA        │
               │  (IDS Monitoring)   │
               └──────────┬──────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │       NGINX         │
               │  (Reverse Proxy)    │
               │    + SSL/TLS        │
               └──────────┬──────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │    APPLICATION      │
               │    (Your App)       │
               └─────────────────────┘
```

**Estimasi waktu:** 8-10 jam

---

## Progress Tracker

```
Bagian I  [░░░░░░░░░░] 0%   (0/6 bab)
Bagian II [░░░░░░░░░░] 0%   (0/5 bab)
Bagian III[░░░░░░░░░░] 0%   (0/4 bab)
──────────────────────────────────────
Total     [░░░░░░░░░░] 0%   (0/15 bab)
```

---

## Environment Setup

### Minimum Requirements

| Komponen | Requirement |
|----------|-------------|
| OS | Ubuntu 22.04 LTS / Debian 12 |
| RAM | 2GB minimum, 4GB recommended |
| Storage | 20GB minimum |
| Network | Internet access |

### Recommended Setup

- **Option 1:** Virtual Machine (VirtualBox / VMware)
- **Option 2:** Cloud VPS (DigitalOcean / Linode / Vultr)
- **Option 3:** WSL2 on Windows (untuk pembelajaran awal)

---

## Referensi Utama

### Buku
- *The Linux Command Line* - William Shotts
- *How Linux Works* - Brian Ward
- *Computer Networking: A Top-Down Approach* - Kurose & Ross

### Online Resources
- [Linux Documentation Project](https://tldp.org/)
- [DigitalOcean Tutorials](https://www.digitalocean.com/community/tutorials)
- [Arch Wiki](https://wiki.archlinux.org/)

---

## Catatan

> Gunakan kolom status untuk melacak progress Anda:
> - [ ] = Belum dikerjakan
> - [~] = Sedang dikerjakan  
> - [x] = Selesai

---

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)
