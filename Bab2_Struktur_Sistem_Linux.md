# BAB 2: STRUKTUR SISTEM LINUX

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)

---

## Daftar Isi

1. [Pendahuluan](#pendahuluan)
2. [Filosofi Linux: Everything is a File](#1-filosofi-linux-everything-is-a-file)
3. [Filesystem Hierarchy Standard (FHS)](#2-filesystem-hierarchy-standard-fhs)
4. [Direktori Root (/)](#3-direktori-root-)
5. [Direktori Binary: /bin, /sbin, /usr/bin, /usr/sbin](#4-direktori-binary-bin-sbin-usrbin-usrsbin)
6. [Direktori Konfigurasi: /etc](#5-direktori-konfigurasi-etc)
7. [Direktori User: /home dan /root](#6-direktori-user-home-dan-root)
8. [Direktori Variable: /var](#7-direktori-variable-var)
9. [Direktori User Programs: /usr](#8-direktori-user-programs-usr)
10. [Virtual Filesystems: /proc dan /sys](#9-virtual-filesystems-proc-dan-sys)
11. [Direktori Device: /dev](#10-direktori-device-dev)
12. [Direktori Lainnya](#11-direktori-lainnya)
13. [Tutorial Praktis](#tutorial-praktis)
14. [Praktikum](#praktikum)
15. [Studi Kasus](#studi-kasus)
16. [Tantangan](#tantangan)
17. [Soal Latihan](#soal-latihan)
18. [Rangkuman](#rangkuman)
19. [Referensi](#referensi)
20. [Glosarium](#glosarium)

---

## PENDAHULUAN

Selamat datang di Bab 2! Setelah memahami konsep Operating System pada Bab 1, sekarang kita akan menyelami **struktur internal sistem Linux**. Memahami struktur direktori Linux adalah kunci untuk menjadi administrator sistem yang kompeten.

### Tujuan Pembelajaran

Setelah menyelesaikan bab ini, Anda akan mampu:
1. Menjelaskan filosofi "Everything is a File" di Linux
2. Memahami standar Filesystem Hierarchy Standard (FHS)
3. Menjelaskan fungsi setiap direktori utama di Linux
4. Menavigasi sistem Linux dengan percaya diri
5. Menemukan file konfigurasi, log, dan binary dengan cepat

### Prasyarat

- Menyelesaikan Bab 1: Apa Itu Operating System
- Akses ke sistem Linux (Ubuntu/Debian)
- Pemahaman dasar command line (`cd`, `ls`, `pwd`)

---

## 1. FILOSOFI LINUX: EVERYTHING IS A FILE

### 1.1 Konsep Fundamental

Di Unix/Linux, **hampir semua hal direpresentasikan sebagai file**. Ini adalah filosofi desain yang membuat sistem menjadi konsisten dan powerful.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    "EVERYTHING IS A FILE"                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   FILE BIASA    │  Text files, images, executables                  │
│   │   (Regular)     │  Contoh: /etc/passwd, /bin/ls                     │
│   └─────────────────┘                                                   │
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   DIREKTORI     │  Container untuk files lain                       │
│   │   (Directory)   │  Contoh: /home, /etc, /var                        │
│   └─────────────────┘                                                   │
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   DEVICE        │  Hardware devices                                 │
│   │   (Device File) │  Contoh: /dev/sda, /dev/tty                       │
│   └─────────────────┘                                                   │
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   SYMBOLIC LINK │  Pointer ke file lain                             │
│   │   (Symlink)     │  Contoh: /bin -> /usr/bin                         │
│   └─────────────────┘                                                   │
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   SOCKET        │  Inter-process communication                      │
│   │   (Socket File) │  Contoh: /var/run/docker.sock                     │
│   └─────────────────┘                                                   │
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   PIPE          │  Named pipes (FIFO)                               │
│   │   (Named Pipe)  │  Untuk IPC                                        │
│   └─────────────────┘                                                   │
│                                                                         │
│   ┌─────────────────┐                                                   │
│   │   PROSES        │  Process information                              │
│   │   (/proc)       │  Contoh: /proc/1234/status                        │
│   └─────────────────┘                                                   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Tipe File di Linux

Gunakan `ls -l` untuk melihat tipe file (karakter pertama):

| Karakter | Tipe | Deskripsi |
|----------|------|-----------|
| `-` | Regular file | File biasa (text, binary, dll) |
| `d` | Directory | Direktori/folder |
| `l` | Symbolic link | Pointer ke file/direktori lain |
| `c` | Character device | Device yang diakses per karakter (keyboard, serial) |
| `b` | Block device | Device yang diakses per block (hard disk, USB) |
| `s` | Socket | Unix domain socket |
| `p` | Named pipe | FIFO untuk IPC |

### 1.3 Contoh Praktis

```bash
$ ls -l /
total 68
lrwxrwxrwx   1 root root     7 Jan 15 10:00 bin -> usr/bin
drwxr-xr-x   4 root root  4096 Feb 28 14:23 boot
drwxr-xr-x  20 root root  4240 Mar  1 08:00 dev
drwxr-xr-x 133 root root 12288 Mar  1 07:45 etc
drwxr-xr-x   3 root root  4096 Feb 20 10:15 home
...

# Penjelasan:
# l = symbolic link (/bin -> /usr/bin)
# d = directory (/boot, /dev, /etc, /home)
```

### 1.4 Keuntungan Filosofi Ini

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    KEUNTUNGAN "EVERYTHING IS A FILE"                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  1. UNIFIED INTERFACE                                                   │
│     ┌───────────────────────────────────────────────────────────────┐   │
│     │  Semua operasi menggunakan syscall yang sama:                 │   │
│     │  open(), read(), write(), close()                             │   │
│     │                                                               │   │
│     │  # Baca file biasa                                            │   │
│     │  cat /etc/passwd                                              │   │
│     │                                                               │   │
│     │  # Baca info CPU (virtual file)                               │   │
│     │  cat /proc/cpuinfo                                            │   │
│     │                                                               │   │
│     │  # Tulis ke device                                            │   │
│     │  echo "hello" > /dev/tty                                      │   │
│     └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  2. COMPOSABILITY (Unix Pipes)                                          │
│     ┌───────────────────────────────────────────────────────────────┐   │
│     │  Kombinasikan tools dengan mudah:                             │   │
│     │  cat /var/log/syslog | grep error | wc -l                     │   │
│     └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  3. SIMPLICITY                                                          │
│     ┌───────────────────────────────────────────────────────────────┐   │
│     │  Tidak perlu API khusus untuk setiap tipe resource            │   │
│     │  Tools standar (cat, grep, echo) bekerja di mana saja         │   │
│     └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 2. FILESYSTEM HIERARCHY STANDARD (FHS)

### 2.1 Apa itu FHS?

**Filesystem Hierarchy Standard (FHS)** adalah standar yang mendefinisikan struktur direktori dan isi direktori di sistem Unix-like. FHS di-maintain oleh Linux Foundation.

### 2.2 Tujuan FHS

1. **Konsistensi** - Struktur sama di berbagai distribusi Linux
2. **Interoperabilitas** - Software tahu di mana mencari file
3. **Predictability** - Admin tahu di mana menaruh file

### 2.3 Overview Struktur

```
                              /
                              │
    ┌─────────┬─────────┬─────┴─────┬─────────┬─────────┐
    │         │         │           │         │         │
   bin      boot       dev        etc       home      lib
    │         │         │           │         │         │
    │     vmlinuz    ┌──┴──┐    ┌───┴───┐   user1   lib64
    │     initrd     │     │    │       │   user2
    │               sda   tty passwd  hosts
    │               null  zero
    │
    ├─────────┬─────────┬─────────┬─────────┬─────────┐
    │         │         │         │         │         │
   media    mnt       opt       proc      root      run
    │         │         │         │         │         │
  cdrom   (mount    google    cpuinfo   .bashrc   (runtime
  usb      point)   chrome    meminfo             data)
                              1/
                              2/
    │
    ├─────────┬─────────┬─────────┬─────────┐
    │         │         │         │         │
   sbin      srv       sys       tmp       usr
    │         │         │         │         │
  fdisk    (service  (kernel   (temp    ┌──┴──┐
  mount      data)    info)    files)   │     │
                                       bin   lib
                                       sbin  share
                                       local
    │
    └─────────┐
              │
             var
              │
      ┌───────┴───────┐
      │       │       │
     log    cache   spool
      │       │       │
   syslog   apt     cron
   auth.log         mail
```

### 2.4 Kategori Direktori

FHS membagi direktori berdasarkan dua kriteria:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        KATEGORISASI FHS                                 │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│                        SHAREABLE vs LOCAL                               │
│  ┌─────────────────────────────┬─────────────────────────────────┐      │
│  │        SHAREABLE            │           LOCAL                 │      │
│  │  (Bisa di-share via NFS)    │  (Spesifik untuk host ini)      │      │
│  ├─────────────────────────────┼─────────────────────────────────┤      │
│  │  /usr (programs)            │  /etc (local config)            │      │
│  │  /opt (add-on software)     │  /boot (boot files)             │      │
│  │  /home (user data)          │  /var/lock (lock files)         │      │
│  │  /var/mail                  │  /var/run (runtime data)        │      │
│  └─────────────────────────────┴─────────────────────────────────┘      │
│                                                                         │
│                       STATIC vs VARIABLE                                │
│  ┌─────────────────────────────┬─────────────────────────────────┐      │
│  │          STATIC             │          VARIABLE               │      │
│  │  (Jarang berubah)           │  (Sering berubah)               │      │
│  ├─────────────────────────────┼─────────────────────────────────┤      │
│  │  /bin, /sbin                │  /var/log (logs)                │      │
│  │  /usr (programs)            │  /var/cache (cache)             │      │
│  │  /opt (software)            │  /var/spool (queues)            │      │
│  │  /boot (kernel)             │  /tmp (temporary)               │      │
│  └─────────────────────────────┴─────────────────────────────────┘      │
│                                                                         │
│  Implikasi:                                                             │
│  • Static directories bisa di-mount read-only                          │
│  • Variable directories butuh write access                              │
│  • Shareable directories bisa di-mount dari server                      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 3. DIREKTORI ROOT (/)

### 3.1 Pengertian

**Root directory (`/`)** adalah direktori paling atas dalam hierarki filesystem Linux. Semua file dan direktori lain berada di bawah root.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           ROOT DIRECTORY (/)                            │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│                               /                                         │
│                               │                                         │
│         ┌───────┬───────┬─────┼─────┬───────┬───────┐                   │
│         │       │       │     │     │       │       │                   │
│        bin    boot     dev   etc   home    lib    media                 │
│                                                                         │
│         ┌───────┬───────┬─────┬─────┬───────┬───────┐                   │
│         │       │       │     │     │       │       │                   │
│        mnt     opt    proc  root   run    sbin    srv                   │
│                                                                         │
│         ┌───────┬───────┬─────┐                                         │
│         │       │       │     │                                         │
│        sys     tmp     usr   var                                        │
│                                                                         │
│  • Simbol: / (forward slash)                                            │
│  • Pemilik: root                                                        │
│  • Permission: 755 (drwxr-xr-x)                                         │
│  • TIDAK ADA direktori parent dari /                                    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 3.2 Karakteristik Penting

| Aspek | Nilai |
|-------|-------|
| Path | `/` |
| Pemilik | root |
| Grup | root |
| Permission | 755 (drwxr-xr-x) |
| Parent | Tidak ada (. dan .. menunjuk ke dirinya sendiri) |

### 3.3 Perintah Dasar Navigasi

```bash
# Ke root directory
cd /

# Lihat isi root
ls -la /

# Lihat disk usage di root
du -sh /*

# Cari file dari root
find / -name "passwd" 2>/dev/null
```

---

## 4. DIREKTORI BINARY: /bin, /sbin, /usr/bin, /usr/sbin

### 4.1 Evolusi Struktur Binary

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    EVOLUSI DIREKTORI BINARY                             │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  DULU (Tradisional Unix):                                               │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                                                                   │  │
│  │  /bin    → Essential user commands (ls, cp, cat)                  │  │
│  │  /sbin   → Essential system admin commands (mount, fdisk)         │  │
│  │  /usr/bin  → Non-essential user commands                          │  │
│  │  /usr/sbin → Non-essential admin commands                         │  │
│  │                                                                   │  │
│  │  Alasan: /usr bisa di-mount terpisah, mungkin tidak tersedia     │  │
│  │          saat boot atau recovery mode                             │  │
│  │                                                                   │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                                                         │
│  SEKARANG (Modern Linux - Merged /usr):                                 │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                                                                   │  │
│  │  /bin  ──────┐                                                    │  │
│  │              │                                                    │  │
│  │              ├──► /usr/bin  (Semua binary user)                   │  │
│  │              │                                                    │  │
│  │  /sbin ─────┐│                                                    │  │
│  │             ││                                                    │  │
│  │             ├┴─► /usr/sbin (Semua binary admin)                   │  │
│  │             │                                                    │  │
│  │  Symlinks:                                                        │  │
│  │  /bin  -> /usr/bin                                                │  │
│  │  /sbin -> /usr/sbin                                               │  │
│  │                                                                   │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 4.2 /bin - Essential User Binaries

**Lokasi:** `/bin` (symlink ke `/usr/bin` di sistem modern)

**Fungsi:** Berisi perintah-perintah dasar yang dibutuhkan oleh **semua user** dan diperlukan saat **single-user mode**.

```bash
# Isi /bin (contoh)
$ ls /bin | head -20
bash          # Bourne Again Shell
cat           # Concatenate files
chmod         # Change mode
chown         # Change owner
cp            # Copy
date          # Display date
dd            # Disk dump
df            # Disk free
echo          # Print text
grep          # Search patterns
gzip          # Compress
ls            # List directory
mkdir         # Make directory
mv            # Move/rename
pwd           # Print working directory
rm            # Remove
sh            # Shell
sleep         # Delay
tar           # Archive
touch         # Update timestamp
```

### 4.3 /sbin - System Binaries

**Lokasi:** `/sbin` (symlink ke `/usr/sbin` di sistem modern)

**Fungsi:** Berisi perintah untuk **administrasi sistem**, biasanya membutuhkan privilege root.

```bash
# Isi /sbin (contoh)
$ ls /sbin | head -20
fdisk         # Partition tool
fsck          # Filesystem check
halt          # Halt system
ifconfig      # Network config (legacy)
init          # System init
ip            # IP configuration
iptables      # Firewall
mkfs          # Make filesystem
mount         # Mount filesystem
reboot        # Reboot system
route         # Routing table (legacy)
shutdown      # Shutdown system
sysctl        # Kernel parameters
systemctl     # Systemd control
udevadm       # Udev admin
umount        # Unmount filesystem
```

### 4.4 Perbedaan bin vs sbin

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         /bin vs /sbin                                   │
├──────────────────────────────────┬──────────────────────────────────────┤
│             /bin                 │              /sbin                   │
├──────────────────────────────────┼──────────────────────────────────────┤
│ • User commands                  │ • System/admin commands              │
│ • Bisa dijalankan semua user     │ • Biasanya butuh root                │
│ • Untuk tugas sehari-hari        │ • Untuk maintenance sistem           │
│                                  │                                      │
│ Contoh:                          │ Contoh:                              │
│ • ls (list files)                │ • fdisk (partition disk)             │
│ • cat (view files)               │ • mount (mount fs)                   │
│ • grep (search)                  │ • reboot (restart)                   │
│ • cp, mv, rm                     │ • iptables (firewall)                │
└──────────────────────────────────┴──────────────────────────────────────┘
```

### 4.5 /usr/bin dan /usr/sbin

Di sistem modern, `/bin` dan `/sbin` adalah symlink:

```bash
$ ls -la /bin
lrwxrwxrwx 1 root root 7 Jan 15 10:00 /bin -> usr/bin

$ ls -la /sbin
lrwxrwxrwx 1 root root 8 Jan 15 10:00 /sbin -> usr/sbin
```

### 4.6 /usr/local/bin

**Fungsi:** Untuk program yang di-install **secara manual** oleh admin (bukan dari package manager).

```bash
# Struktur /usr/local
/usr/local/
├── bin/       # Local binaries
├── sbin/      # Local system binaries
├── lib/       # Local libraries
├── include/   # Local header files
├── share/     # Local shared data
└── src/       # Local source code
```

**Contoh penggunaan:**

```bash
# Install binary secara manual
sudo cp my-script /usr/local/bin/
sudo chmod +x /usr/local/bin/my-script

# Binary ini akan tersedia di PATH
my-script
```

---

## 5. DIREKTORI KONFIGURASI: /etc

### 5.1 Pengertian

**`/etc`** adalah direktori yang berisi **semua file konfigurasi sistem**. Nama "etc" berasal dari "et cetera" (dan lain-lain), namun sekarang sering diartikan sebagai "Editable Text Configuration".

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /etc                                       │
│                    "Editable Text Configuration"                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /etc berisi:                                                           │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  System-wide configuration files                            │    │
│  │  Per-application configuration                               │    │
│  │  Startup scripts (init/systemd)                              │    │
│  │  User/group databases                                        │    │
│  │  Network configuration                                       │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  Karakteristik:                                                         │
│  • Hanya berisi static configuration (bukan data dinamis)              │
│  • Bisa di-backup untuk replicate system config                        │
│  • Harus readable oleh programs, biasanya owned by root                │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.2 File-File Penting di /etc

#### 5.2.1 User & Authentication

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    FILES: USER & AUTHENTICATION                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /etc/passwd                                                            │
│  ─────────────                                                          │
│  Database user (tanpa password)                                         │
│                                                                         │
│  Format: username:x:UID:GID:comment:home:shell                          │
│                                                                         │
│  Contoh:                                                                │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ root:x:0:0:root:/root:/bin/bash                                   │  │
│  │ atha:x:1000:1000:Muhammad Dhiyaul Atha:/home/atha:/bin/bash       │  │
│  │ www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin              │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                                                         │
│  /etc/shadow                                                            │
│  ────────────                                                           │
│  Password hash (restricted access)                                      │
│  Permission: 640 (rw-r-----)                                            │
│                                                                         │
│  Format: username:hash:lastchange:min:max:warn:inactive:expire          │
│                                                                         │
│  /etc/group                                                             │
│  ───────────                                                            │
│  Database group                                                         │
│                                                                         │
│  Format: groupname:x:GID:members                                        │
│                                                                         │
│  Contoh:                                                                │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ root:x:0:                                                         │  │
│  │ sudo:x:27:atha                                                    │  │
│  │ docker:x:998:atha                                                 │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│                                                                         │
│  /etc/sudoers                                                           │
│  ─────────────                                                          │
│  Konfigurasi sudo (JANGAN edit langsung, gunakan visudo!)               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

#### 5.2.2 Network Configuration

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      FILES: NETWORK CONFIGURATION                       │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /etc/hostname                                                          │
│  ──────────────                                                         │
│  Nama host dari mesin ini                                               │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ ubuntu-server                                                   │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  /etc/hosts                                                             │
│  ───────────                                                            │
│  Local DNS resolution (sebelum query DNS server)                        │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ 127.0.0.1       localhost                                       │    │
│  │ 127.0.1.1       ubuntu-server                                   │    │
│  │ 192.168.1.100   my-server.local                                 │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  /etc/resolv.conf                                                       │
│  ─────────────────                                                      │
│  DNS server configuration                                               │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ nameserver 8.8.8.8                                              │    │
│  │ nameserver 8.8.4.4                                              │    │
│  │ search example.com                                              │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  /etc/network/interfaces (Debian/Ubuntu legacy)                         │
│  /etc/netplan/*.yaml (Ubuntu modern)                                    │
│  ────────────────────────────────────────                               │
│  Network interface configuration                                        │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

#### 5.2.3 System Configuration

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      FILES: SYSTEM CONFIGURATION                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /etc/fstab                                                             │
│  ───────────                                                            │
│  Filesystem table - auto-mount saat boot                                │
│                                                                         │
│  Format: device  mountpoint  fstype  options  dump  pass                │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ UUID=abc123  /        ext4   defaults         0    1           │    │
│  │ UUID=def456  /home    ext4   defaults         0    2           │    │
│  │ /dev/sdb1    /backup  ext4   defaults,noauto  0    0           │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  /etc/os-release                                                        │
│  ────────────────                                                       │
│  Informasi distribusi                                                   │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ NAME="Ubuntu"                                                   │    │
│  │ VERSION="22.04.3 LTS (Jammy Jellyfish)"                         │    │
│  │ ID=ubuntu                                                       │    │
│  │ ID_LIKE=debian                                                  │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  /etc/shells                                                            │
│  ────────────                                                           │
│  Daftar shell yang valid                                                │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ /bin/sh                                                         │    │
│  │ /bin/bash                                                       │    │
│  │ /bin/zsh                                                        │    │
│  │ /usr/bin/fish                                                   │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  /etc/environment                                                       │
│  ─────────────────                                                      │
│  System-wide environment variables                                      │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │ PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin"        │    │
│  │ LANG="en_US.UTF-8"                                              │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 5.3 Subdirektori Penting di /etc

```bash
/etc/
├── apt/                    # APT package manager config
│   ├── sources.list        # Repository sources
│   └── sources.list.d/     # Additional sources
├── cron.d/                 # Cron job files
├── cron.daily/             # Daily cron scripts
├── cron.hourly/            # Hourly cron scripts
├── default/                # Default configs for services
├── init.d/                 # SysV init scripts (legacy)
├── logrotate.d/            # Log rotation configs
├── nginx/                  # Nginx configuration
│   ├── nginx.conf          # Main config
│   ├── sites-available/    # Available sites
│   └── sites-enabled/      # Enabled sites (symlinks)
├── ssh/                    # SSH configuration
│   ├── sshd_config         # SSH server config
│   └── ssh_config          # SSH client config
├── ssl/                    # SSL certificates
│   ├── certs/              # Public certificates
│   └── private/            # Private keys
├── systemd/                # Systemd configuration
│   └── system/             # System unit files
└── ufw/                    # UFW firewall config
```

### 5.4 Best Practices untuk /etc

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      BEST PRACTICES: /etc                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   DO:                                                                 │
│  ────                                                                   │
│  • Backup /etc sebelum perubahan besar                                  │
│  • Version control /etc dengan etckeeper (git)                          │
│  • Baca dokumentasi sebelum edit                                        │
│  • Test config sebelum restart service                                  │
│  • Gunakan visudo untuk edit sudoers                                    │
│                                                                         │
│  ❌ DON'T:                                                              │
│  ──────                                                                 │
│  • Edit binary files di /etc                                            │
│  • Hapus file tanpa tahu fungsinya                                      │
│  • Edit langsung tanpa backup                                           │
│  • Ubah permission sembarangan                                          │
│                                                                         │
│   TIPS:                                                               │
│  ─────                                                                  │
│  # Test nginx config sebelum restart                                    │
│  sudo nginx -t                                                          │
│                                                                         │
│  # Test sshd config                                                     │
│  sudo sshd -t                                                           │
│                                                                         │
│  # Backup /etc                                                          │
│  sudo tar -czvf etc-backup-$(date +%Y%m%d).tar.gz /etc                  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 6. DIREKTORI USER: /home DAN /root

### 6.1 /home - User Home Directories

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /home                                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Struktur:                                                              │
│  /home/                                                                 │
│  ├── atha/                    # User atha                               │
│  │   ├── .bashrc              # Bash config                             │
│  │   ├── .profile             # Login profile                           │
│  │   ├── .ssh/                # SSH keys & config                       │
│  │   │   ├── id_rsa           # Private key                             │
│  │   │   ├── id_rsa.pub       # Public key                              │
│  │   │   ├── known_hosts      # Known SSH hosts                         │
│  │   │   └── authorized_keys  # Allowed keys                            │
│  │   ├── .config/             # App configs (XDG)                       │
│  │   ├── .local/              # Local user data                         │
│  │   ├── Documents/                                                     │
│  │   ├── Downloads/                                                     │
│  │   └── Projects/                                                      │
│  ├── user2/                                                             │
│  └── user3/                                                             │
│                                                                         │
│  Karakteristik:                                                         │
│  • Setiap user punya direktori sendiri                                  │
│  • Default permission: 755 atau 700                                     │
│  • Location defined di /etc/passwd                                      │
│  • ~ adalah shortcut ke home directory                                  │
│                                                                         │
│  Variable:                                                              │
│  $HOME = /home/username                                                 │
│  ~ = shortcut untuk $HOME                                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 6.2 Dotfiles (Hidden Configuration)

File yang dimulai dengan titik (.) adalah **hidden files**. Biasanya berisi konfigurasi:

```bash
# Lihat semua file termasuk hidden
ls -la ~

# Common dotfiles
~/.bashrc         # Bash shell config (non-login)
~/.bash_profile   # Bash login shell config
~/.profile        # Generic login config
~/.zshrc          # Zsh shell config
~/.vimrc          # Vim editor config
~/.gitconfig      # Git global config
~/.ssh/           # SSH configuration directory
~/.config/        # XDG config directory (modern apps)
~/.local/         # XDG local data directory
```

### 6.3 /root - Root User Home

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /root                                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  • Home directory untuk user root                                       │
│  • BUKAN bagian dari /home                                              │
│  • Alasan: /home mungkin di partition terpisah yang belum               │
│            di-mount saat recovery mode                                  │
│                                                                         │
│  Struktur:                                                              │
│  /root/                                                                 │
│  ├── .bashrc                                                            │
│  ├── .profile                                                           │
│  ├── .ssh/                                                              │
│  └── ...                                                                │
│                                                                         │
│  Permission: 700 (drwx------)                                           │
│  Hanya root yang bisa akses!                                            │
│                                                                         │
│  ⚠️  PERINGATAN:                                                        │
│  Jangan login sebagai root untuk tugas sehari-hari!                     │
│  Gunakan sudo untuk perintah yang butuh privilege.                      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 6.4 Perbedaan /home dan /root

| Aspek | /home/user | /root |
|-------|------------|-------|
| Untuk | User biasa | Root/superuser |
| Location | Di bawah /home | Langsung di / |
| Default permission | 755 atau 700 | 700 |
| Bisa di-mount terpisah? | Ya (umum) | Tidak |
| Akses | User masing-masing | Hanya root |

---

## 7. DIREKTORI VARIABLE: /var

### 7.1 Pengertian

**`/var`** berisi data yang **bervariasi** (berubah) saat sistem berjalan. Berbeda dengan `/usr` yang static, `/var` berisi data yang selalu berubah seperti log, cache, dan queue.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /var                                       │
│                    "Variable Data Files"                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /var/                                                                  │
│  ├── cache/       # Application cache                                   │
│  │   ├── apt/     # APT package cache                                   │
│  │   └── man/     # Man page cache                                      │
│  │                                                                      │
│  ├── lib/         # Variable state information                          │
│  │   ├── apt/     # APT state                                           │
│  │   ├── docker/  # Docker data                                         │
│  │   ├── mysql/   # MySQL databases                                     │
│  │   └── dpkg/    # DPKG state                                          │
│  │                                                                      │
│  ├── log/         # Log files ⭐                                        │
│  │   ├── syslog   # System log                                          │
│  │   ├── auth.log # Authentication log                                  │
│  │   ├── kern.log # Kernel log                                          │
│  │   ├── nginx/   # Nginx logs                                          │
│  │   └── apache2/ # Apache logs                                         │
│  │                                                                      │
│  ├── mail/        # User mailboxes                                      │
│  │                                                                      │
│  ├── run/         # Runtime data (symlink ke /run)                      │
│  │   └── *.pid    # Process ID files                                    │
│  │                                                                      │
│  ├── spool/       # Queue directories                                   │
│  │   ├── cron/    # Cron jobs                                           │
│  │   └── mail/    # Mail queue                                          │
│  │                                                                      │
│  ├── tmp/         # Temporary files (preserved across reboot)           │
│  │                                                                      │
│  └── www/         # Web server files                                    │
│      └── html/    # Default web root                                    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 7.2 /var/log - System Logs

```
┌─────────────────────────────────────────────────────────────────────────┐
│                            /var/log                                     │
│                    "System Log Files"                                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  File/Direktori          │ Deskripsi                                    │
│  ────────────────────────┼───────────────────────────────────────────   │
│  syslog                  │ System messages (rsyslog)                    │
│  auth.log                │ Authentication logs (login, sudo, ssh)       │
│  kern.log                │ Kernel messages                              │
│  dmesg                   │ Boot & hardware messages                     │
│  dpkg.log                │ Package manager log                          │
│  apt/                    │ APT logs                                     │
│  nginx/                  │ Nginx access & error logs                    │
│  apache2/                │ Apache logs                                  │
│  mysql/                  │ MySQL logs                                   │
│  journal/                │ Systemd journal (binary)                     │
│  faillog                 │ Failed login attempts                        │
│  lastlog                 │ Last login info per user                     │
│  wtmp                    │ Login history (binary)                       │
│  btmp                    │ Bad login attempts (binary)                  │
│                                                                         │
│  Commands:                                                              │
│  ─────────                                                              │
│  # View log                                                             │
│  tail -f /var/log/syslog                                                │
│                                                                         │
│  # View auth log                                                        │
│  sudo tail -100 /var/log/auth.log                                       │
│                                                                         │
│  # Search error di semua log                                            │
│  sudo grep -r "error" /var/log/ 2>/dev/null                             │
│                                                                         │
│  # Systemd journal                                                      │
│  journalctl -xe                                                         │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 7.3 /var/lib - Persistent State

```bash
# Contoh isi /var/lib
/var/lib/
├── apt/                # APT state & downloaded packages
│   ├── lists/          # Package lists from repos
│   └── extended_states # APT extended state
├── docker/             # Docker images, containers, volumes
├── mysql/              # MySQL database files
├── postgresql/         # PostgreSQL database files
├── dpkg/               # DPKG database
│   ├── info/           # Package info files
│   └── status          # Package status
└── systemd/            # Systemd state
```

### 7.4 /var/cache - Cached Data

Data cache yang bisa di-regenerate jika dihapus:

```bash
/var/cache/
├── apt/                # Downloaded .deb packages
│   └── archives/       # Cached packages
├── man/                # Formatted man pages
└── fontconfig/         # Font cache
```

```bash
# Bersihkan APT cache
sudo apt clean          # Hapus semua cached packages
sudo apt autoclean      # Hapus old cached packages
```

### 7.5 /var/www - Web Server Root

```bash
/var/www/
└── html/               # Default web root
    └── index.html      # Default page

# Typical web setup
/var/www/
├── html/               # Default site
├── mysite.com/         # Virtual host 1
│   ├── public_html/
│   └── logs/
└── another.com/        # Virtual host 2
```

---

## 8. DIREKTORI USER PROGRAMS: /usr

### 8.1 Pengertian

**`/usr`** berisi data dan program yang **read-only dan shareable**. Nama "usr" awalnya adalah "user" tapi sekarang lebih berarti "Unix System Resources".

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /usr                                       │
│                    "Unix System Resources"                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /usr/                                                                  │
│  ├── bin/          # User commands (merged dengan /bin)                 │
│  ├── sbin/         # System admin commands (merged dengan /sbin)        │
│  ├── lib/          # Libraries for /usr/bin & /usr/sbin                 │
│  ├── lib64/        # 64-bit libraries                                   │
│  │                                                                      │
│  ├── include/      # C/C++ header files                                 │
│  │   └── *.h       # Header untuk development                           │
│  │                                                                      │
│  ├── local/        # Locally installed software ⭐                      │
│  │   ├── bin/      # Local binaries                                     │
│  │   ├── sbin/     # Local system binaries                              │
│  │   ├── lib/      # Local libraries                                    │
│  │   └── share/    # Local shared data                                  │
│  │                                                                      │
│  ├── share/        # Architecture-independent data                      │
│  │   ├── doc/      # Documentation                                      │
│  │   ├── man/      # Man pages                                          │
│  │   ├── icons/    # Icons                                              │
│  │   ├── fonts/    # Fonts                                              │
│  │   └── zsh/      # Zsh completions                                    │
│  │                                                                      │
│  └── src/          # Source code (optional)                             │
│      └── linux-headers-*  # Kernel headers                              │
│                                                                         │
│  Karakteristik:                                                         │
│  • Static data (bisa di-mount read-only)                               │
│  • Shareable via NFS                                                    │
│  • Tidak berisi data yang machine-specific                              │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 8.2 /usr/local - Hierarki Terpisah

`/usr/local` adalah hierarki terpisah untuk software yang di-install **secara lokal** (tidak dari package manager):

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           /usr/local                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Digunakan untuk:                                                       │
│  • Software yang di-compile dari source                                 │
│  • Custom scripts                                                       │
│  • Program yang tidak ada di repository                                 │
│                                                                         │
│  Struktur (mirrors /usr):                                               │
│  /usr/local/                                                            │
│  ├── bin/          # Executables                                        │
│  ├── sbin/         # Admin executables                                  │
│  ├── lib/          # Libraries                                          │
│  ├── include/      # Headers                                            │
│  ├── share/        # Shared data                                        │
│  │   └── man/      # Man pages                                          │
│  └── src/          # Source code                                        │
│                                                                         │
│  Contoh penggunaan:                                                     │
│  # Install binary manual                                                │
│  sudo install -m 755 my-app /usr/local/bin/                             │
│                                                                         │
│  # Compile dari source biasanya install ke sini                         │
│  ./configure --prefix=/usr/local                                        │
│  make && sudo make install                                              │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 8.3 /usr/share - Shared Data

Data yang tidak tergantung arsitektur:

```bash
/usr/share/
├── applications/    # .desktop files (application launchers)
├── doc/             # Documentation
├── fonts/           # Font files
├── icons/           # Icon themes
├── man/             # Manual pages
│   ├── man1/        # Section 1: User commands
│   ├── man2/        # Section 2: System calls
│   ├── man3/        # Section 3: Library functions
│   ├── man5/        # Section 5: File formats
│   └── man8/        # Section 8: Admin commands
├── mime/            # MIME types
└── zsh/             # Zsh completions & functions
```

---

## 9. VIRTUAL FILESYSTEMS: /proc DAN /sys

### 9.1 Pengertian Virtual Filesystem

**Virtual filesystem** adalah filesystem yang tidak menyimpan data di disk. Data di-generate oleh kernel secara dinamis. Dua yang paling penting adalah `/proc` dan `/sys`.

### 9.2 /proc - Process Information

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /proc                                      │
│                    "Process Information Pseudo-Filesystem"              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /proc/                                                                 │
│  ├── [PID]/          # Direktori untuk setiap proses                    │
│  │   ├── cmdline     # Command line arguments                           │
│  │   ├── cwd         # Symlink ke working directory                     │
│  │   ├── environ     # Environment variables                            │
│  │   ├── exe         # Symlink ke executable                            │
│  │   ├── fd/         # Open file descriptors                            │
│  │   ├── maps        # Memory mappings                                  │
│  │   ├── status      # Process status                                   │
│  │   └── ...                                                            │
│  │                                                                      │
│  ├── cpuinfo         # CPU information ⭐                               │
│  ├── meminfo         # Memory information ⭐                            │
│  ├── version         # Kernel version                                   │
│  ├── uptime          # System uptime                                    │
│  ├── loadavg         # Load average                                     │
│  ├── mounts          # Mounted filesystems                              │
│  ├── partitions      # Disk partitions                                  │
│  ├── swaps           # Swap areas                                       │
│  ├── net/            # Network information                              │
│  │   ├── tcp         # TCP connections                                  │
│  │   ├── udp         # UDP connections                                  │
│  │   └── dev         # Network devices                                  │
│  ├── sys/            # Kernel parameters (tunable) ⭐                   │
│  │   ├── kernel/     # Kernel settings                                  │
│  │   ├── net/        # Network settings                                 │
│  │   ├── vm/         # Virtual memory settings                          │
│  │   └── fs/         # Filesystem settings                              │
│  └── ...                                                                │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 9.3 Contoh Penggunaan /proc

```bash
# === System Information ===

# CPU Information
cat /proc/cpuinfo

# Memory Information
cat /proc/meminfo

# Kernel Version
cat /proc/version

# Uptime
cat /proc/uptime

# Load Average
cat /proc/loadavg


# === Process Information ===

# Lihat command line proses (ganti 1234 dengan PID)
cat /proc/1234/cmdline

# Lihat environment variables proses
cat /proc/1234/environ | tr '\0' '\n'

# Lihat open files proses
ls -la /proc/1234/fd

# Lihat memory map proses
cat /proc/1234/maps


# === Runtime Tuning ===

# Lihat parameter kernel
cat /proc/sys/kernel/hostname

# Ubah parameter (temporary, hilang setelah reboot)
echo "new-hostname" | sudo tee /proc/sys/kernel/hostname

# Enable IP forwarding
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward

# Sama dengan:
sudo sysctl net.ipv4.ip_forward=1
```

### 9.4 /sys - Sysfs

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /sys                                       │
│                    "Sysfs - Kernel Object Information"                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /sys/ berisi representasi struktur kernel objects:                     │
│  - Devices                                                              │
│  - Drivers                                                              │
│  - Filesystems                                                          │
│  - Kernel subsystems                                                    │
│                                                                         │
│  /sys/                                                                  │
│  ├── block/          # Block devices (disks)                            │
│  │   ├── sda/        # First disk                                       │
│  │   └── sdb/        # Second disk                                      │
│  │                                                                      │
│  ├── bus/            # Bus types                                        │
│  │   ├── pci/        # PCI devices                                      │
│  │   └── usb/        # USB devices                                      │
│  │                                                                      │
│  ├── class/          # Device classes                                   │
│  │   ├── net/        # Network interfaces                               │
│  │   ├── block/      # Block devices                                    │
│  │   ├── tty/        # Terminal devices                                 │
│  │   └── power_supply/ # Power (battery, AC)                            │
│  │                                                                      │
│  ├── devices/        # All devices                                      │
│  ├── firmware/       # Firmware interfaces                              │
│  ├── fs/             # Filesystem parameters                            │
│  ├── kernel/         # Kernel subsystems                                │
│  ├── module/         # Loaded kernel modules                            │
│  └── power/          # Power management                                 │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 9.5 Contoh Penggunaan /sys

```bash
# Lihat network interfaces
ls /sys/class/net/
# Output: enp0s3  lo

# Lihat info network interface
cat /sys/class/net/enp0s3/address      # MAC address
cat /sys/class/net/enp0s3/speed        # Link speed
cat /sys/class/net/enp0s3/operstate    # up/down

# Lihat block devices
ls /sys/block/
# Output: sda  sdb

# Lihat disk size (in sectors)
cat /sys/block/sda/size

# Control backlight (laptop)
cat /sys/class/backlight/*/brightness
echo 50 | sudo tee /sys/class/backlight/*/brightness

# Lihat battery status (laptop)
cat /sys/class/power_supply/BAT0/capacity
cat /sys/class/power_supply/BAT0/status
```

### 9.6 Perbedaan /proc vs /sys

| Aspek | /proc | /sys |
|-------|-------|------|
| **Fokus** | Process information | Device/driver information |
| **Isi Numerik** | PID directories | Device symlinks |
| **Sejarah** | Lebih tua (Unix) | Lebih baru (Linux 2.6+) |
| **Organisasi** | Flat/mixed | Hierarkis |
| **Tuning** | `/proc/sys/*` | Sebagian |

---

## 10. DIREKTORI DEVICE: /dev

### 10.1 Pengertian

**`/dev`** berisi **device files (device nodes)** - special files yang merepresentasikan hardware devices dan pseudo-devices.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /dev                                       │
│                    "Device Files"                                       │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Device files bukan file biasa, tapi interface ke kernel drivers.       │
│                                                                         │
│  Cara kerja:                                                            │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  User Program                                                   │    │
│  │       │                                                         │    │
│  │       ▼  open("/dev/sda", ...)                                  │    │
│  │  ┌─────────────────────────────────────────────────────────┐    │    │
│  │  │  Kernel looks up device (8, 0)                          │    │    │
│  │  │       │                                                 │    │    │
│  │  │       ▼                                                 │    │    │
│  │  │  SCSI Disk Driver                                       │    │    │
│  │  │       │                                                 │    │    │
│  │  │       ▼                                                 │    │    │
│  │  │  Physical Hard Drive                                    │    │    │
│  │  └─────────────────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  Device Numbers:                                                        │
│  • Major number: identifies driver                                      │
│  • Minor number: identifies specific device                             │
│                                                                         │
│  $ ls -l /dev/sda                                                       │
│  brw-rw---- 1 root disk 8, 0 Mar  1 08:00 /dev/sda                     │
│                          ↑  ↑                                           │
│                    major  minor                                         │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 10.2 Tipe Device

#### Block Devices (b)

Diakses dalam block (biasanya 512 bytes atau 4KB). Contoh: hard disk, USB drive.

```bash
$ ls -l /dev/sd*
brw-rw---- 1 root disk 8, 0 Mar  1 08:00 /dev/sda      # Disk 1
brw-rw---- 1 root disk 8, 1 Mar  1 08:00 /dev/sda1     # Partition 1
brw-rw---- 1 root disk 8, 2 Mar  1 08:00 /dev/sda2     # Partition 2
brw-rw---- 1 root disk 8,16 Mar  1 08:00 /dev/sdb      # Disk 2
```

#### Character Devices (c)

Diakses per karakter. Contoh: keyboard, serial port, terminals.

```bash
$ ls -l /dev/tty*
crw-rw-rw- 1 root tty 5, 0 Mar  1 08:00 /dev/tty       # Current terminal
crw--w---- 1 root tty 4, 0 Mar  1 08:00 /dev/tty0      # Console
crw--w---- 1 atha tty 136,0 Mar  1 09:00 /dev/pts/0    # Pseudo-terminal
```

### 10.3 Device Files Penting

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     IMPORTANT DEVICE FILES                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  STORAGE:                                                               │
│  ─────────                                                              │
│  /dev/sda, /dev/sdb, ...     # SCSI/SATA disks                          │
│  /dev/sda1, /dev/sda2, ...   # Disk partitions                          │
│  /dev/nvme0n1                # NVMe SSD                                 │
│  /dev/vda                    # VirtIO disk (VM)                         │
│  /dev/loop0                  # Loop device                              │
│                                                                         │
│  PSEUDO-DEVICES:                                                        │
│  ────────────────                                                       │
│  /dev/null       # Bit bucket (discards all input)                      │
│  /dev/zero       # Infinite stream of zeros                             │
│  /dev/random     # Blocking random number generator                     │
│  /dev/urandom    # Non-blocking random (use this!)                      │
│  /dev/full       # Always returns "disk full"                           │
│                                                                         │
│  TERMINALS:                                                             │
│  ───────────                                                            │
│  /dev/tty        # Current terminal                                     │
│  /dev/tty[0-6]   # Virtual consoles (Ctrl+Alt+F1-F6)                    │
│  /dev/pts/*      # Pseudo-terminals (SSH, terminal emulator)            │
│  /dev/console    # System console                                       │
│                                                                         │
│  SPECIAL:                                                               │
│  ────────                                                               │
│  /dev/stdin      # Symlink ke /proc/self/fd/0                           │
│  /dev/stdout     # Symlink ke /proc/self/fd/1                           │
│  /dev/stderr     # Symlink ke /proc/self/fd/2                           │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 10.4 Contoh Penggunaan

```bash
# Buang output ke /dev/null
command 2>/dev/null       # Buang stderr
command > /dev/null 2>&1  # Buang semua output

# Generate data random
head -c 100 /dev/urandom | base64

# Generate file dengan zeros (untuk testing)
dd if=/dev/zero of=testfile bs=1M count=100

# Secure delete (overwrite dengan random)
dd if=/dev/urandom of=/dev/sdb bs=1M status=progress

# Copy entire disk
dd if=/dev/sda of=/dev/sdb bs=4M status=progress

# Test "disk full" error handling
cat /dev/zero > /dev/full
# cat: write error: No space left on device
```

### 10.5 udev - Device Manager

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              udev                                       │
│                    "Dynamic Device Management"                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  udev adalah device manager untuk Linux kernel. Tugasnya:               │
│  • Membuat/menghapus device nodes di /dev secara dinamis                │
│  • Menangani hotplug events (USB, dll)                                  │
│  • Menjalankan scripts saat device ditambah/dihapus                     │
│                                                                         │
│  Flow:                                                                  │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  Hardware Event (USB plugged in)                                │    │
│  │       │                                                         │    │
│  │       ▼                                                         │    │
│  │  Kernel generates uevent                                        │    │
│  │       │                                                         │    │
│  │       ▼                                                         │    │
│  │  udevd receives event                                           │    │
│  │       │                                                         │    │
│  │       ▼                                                         │    │
│  │  Process rules (/etc/udev/rules.d/)                             │    │
│  │       │                                                         │    │
│  │       ▼                                                         │    │
│  │  Create /dev/sdX with proper permissions                        │    │
│  │  Run custom scripts if configured                               │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  Commands:                                                              │
│  udevadm info /dev/sda           # Info about device                    │
│  udevadm monitor                 # Watch events real-time               │
│  udevadm trigger                 # Re-trigger rules                     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 11. DIREKTORI LAINNYA

### 11.1 /boot - Boot Files

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /boot                                      │
│                    "Static Boot Files"                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  /boot/                                                                 │
│  ├── vmlinuz-5.15.0-generic      # Compressed Linux kernel              │
│  ├── initrd.img-5.15.0-generic   # Initial RAM disk                     │
│  ├── config-5.15.0-generic       # Kernel config                        │
│  ├── System.map-5.15.0-generic   # Kernel symbol map                    │
│  ├── grub/                       # GRUB bootloader                      │
│  │   ├── grub.cfg                # GRUB config                          │
│  │   └── fonts/                  # GRUB fonts                           │
│  └── efi/                        # EFI System Partition (UEFI)          │
│      └── EFI/                                                           │
│          └── ubuntu/                                                    │
│                                                                         │
│  ⚠️  PERINGATAN:                                                        │
│  Jangan hapus atau modifikasi file di /boot sembarangan!                │
│  Bisa membuat sistem tidak bisa boot.                                   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 11.2 /lib dan /lib64 - Libraries

```bash
/lib/                           # Essential shared libraries
├── x86_64-linux-gnu/           # Architecture-specific
│   ├── libc.so.6               # C library
│   ├── libm.so.6               # Math library
│   └── ...
├── modules/                    # Kernel modules
│   └── 5.15.0-generic/         # Modules for this kernel
│       ├── kernel/
│       └── modules.dep
├── firmware/                   # Hardware firmware
└── systemd/                    # Systemd libraries

/lib64 -> /usr/lib64            # Symlink (64-bit)
```

### 11.3 /mnt dan /media - Mount Points

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      /mnt vs /media                                     │
├──────────────────────────────────┬──────────────────────────────────────┤
│             /mnt                 │              /media                  │
├──────────────────────────────────┼──────────────────────────────────────┤
│ • Temporary mount point          │ • Auto-mount point                   │
│ • Untuk mount manual             │ • Untuk removable media              │
│ • Dikontrol admin                │ • Dikontrol system/udisks            │
│                                  │                                      │
│ Contoh:                          │ Contoh:                              │
│ sudo mount /dev/sdb1 /mnt        │ /media/atha/USB_DRIVE                │
│ sudo mount -t nfs server:/share /mnt│ /media/atha/CDROM                 │
└──────────────────────────────────┴──────────────────────────────────────┘
```

### 11.4 /opt - Optional Software

```bash
/opt/                           # Third-party software
├── google/
│   └── chrome/                 # Google Chrome
├── containerd/                 # Containerd
└── lampp/                      # XAMPP
```

Software yang di-install ke `/opt` biasanya self-contained dengan struktur sendiri.

### 11.5 /srv - Service Data

```bash
/srv/                           # Data for services
├── ftp/                        # FTP server data
├── http/                       # HTTP server data (alternative)
└── tftp/                       # TFTP server data
```

### 11.6 /tmp - Temporary Files

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              /tmp                                       │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  • Untuk temporary files                                                │
│  • Bisa dihapus kapan saja (biasanya saat reboot)                      │
│  • Permission: 1777 (drwxrwxrwt) - sticky bit!                         │
│  • Setiap user bisa create, tapi hanya owner yang bisa delete          │
│                                                                         │
│  Sticky bit (t):                                                        │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  $ ls -ld /tmp                                                  │    │
│  │  drwxrwxrwt 15 root root 4096 Mar  1 09:00 /tmp                 │    │
│  │         ↑                                                       │    │
│  │     sticky bit                                                  │    │
│  │                                                                 │    │
│  │  Artinya: Semua bisa write, tapi hanya owner yang bisa         │    │
│  │           hapus filenya sendiri.                                │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│  Modern Linux: /tmp sering di-mount sebagai tmpfs (RAM disk)           │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 11.7 /run - Runtime Data

```bash
/run/                           # Runtime variable data
├── lock/                       # Lock files
├── user/                       # Per-user runtime
│   └── 1000/                   # User UID 1000
├── systemd/                    # Systemd runtime
├── docker.sock                 # Docker socket
├── sshd.pid                    # SSH daemon PID
└── utmp                        # Currently logged users
```

`/run` adalah tmpfs (RAM disk) yang di-mount saat boot.

---

## TUTORIAL PRAKTIS

### Tutorial 1: Eksplorasi Filesystem

```bash
# 1. Lihat struktur direktori root
ls -la /

# 2. Lihat type setiap item
file /bin /etc /dev/null /proc/cpuinfo

# 3. Cek symlinks
ls -la /bin /sbin /lib

# 4. Lihat mount points
mount | column -t

# 5. Lihat disk usage per direktori
du -sh /* 2>/dev/null | sort -h
```

### Tutorial 2: Membaca Informasi Sistem dari /proc

```bash
# 1. CPU Info
cat /proc/cpuinfo | grep -E "model name|cores"

# 2. Memory Info
cat /proc/meminfo | head -5

# 3. Kernel Version
cat /proc/version

# 4. Uptime (dalam detik)
cat /proc/uptime

# 5. Load Average
cat /proc/loadavg

# 6. Lihat semua process
ls /proc | grep -E '^[0-9]+$' | wc -l
```

### Tutorial 3: Bekerja dengan /dev

```bash
# 1. Generate random string
head -c 20 /dev/urandom | base64

# 2. Buang output
ls /nonexistent 2>/dev/null

# 3. Test write ke /dev/null
dd if=/dev/zero of=/dev/null bs=1M count=100

# 4. Monitor devices
watch -n 1 'ls -la /dev/sd*'

# (Plug USB dan lihat perubahan)
```

### Tutorial 4: Eksplorasi /etc

```bash
# 1. Lihat hostname
cat /etc/hostname

# 2. Lihat users
cat /etc/passwd

# 3. Lihat groups
cat /etc/group

# 4. Lihat network config
cat /etc/resolv.conf
cat /etc/hosts

# 5. Lihat OS info
cat /etc/os-release

# 6. Lihat mounted filesystems
cat /etc/mtab  # atau mount

# 7. Lihat fstab
cat /etc/fstab
```

---

## PRAKTIKUM

### Praktikum 1: Mapping Filesystem

**Durasi:** 45 menit

**Tugas:**
1. Buat diagram struktur `/` di sistem Anda
2. Identifikasi mana yang symlink dan ke mana
3. Catat filesystem type setiap mount point

```bash
# Commands yang digunakan
ls -la /
mount | column -t
df -Th
```

### Praktikum 2: Analisis /var/log

**Durasi:** 60 menit

**Tugas:**
1. Buat daftar semua log files di `/var/log`
2. Temukan 5 error terbaru di sistem
3. Analisis login attempts dalam 24 jam terakhir

```bash
# Commands yang digunakan
ls -la /var/log
sudo grep -i error /var/log/syslog | tail -10
sudo grep "session opened" /var/log/auth.log | tail -5
journalctl --since "24 hours ago" -p err
```

### Praktikum 3: Monitoring Real-time dengan /proc

**Durasi:** 45 menit

**Tugas:**
Buat script untuk monitoring sistem:

```bash
#!/bin/bash
# system-monitor.sh

while true; do
    clear
    echo "=== System Monitor ==="
    echo
    echo "--- CPU Usage ---"
    head -5 /proc/stat
    echo
    echo "--- Memory ---"
    head -5 /proc/meminfo
    echo
    echo "--- Load Average ---"
    cat /proc/loadavg
    echo
    echo "--- Top 5 Processes by Memory ---"
    ps aux --sort=-%mem | head -6
    echo
    sleep 2
done
```

### Praktikum 4: Device Investigation

**Durasi:** 45 menit

**Tugas:**
1. List semua block devices
2. Identifikasi device type (disk, partition, loop)
3. Cek ukuran dan mount status

```bash
lsblk -f
ls -la /dev/sd*
cat /proc/partitions
```

---

## STUDI KASUS

### Studi Kasus 1: Kenapa Config Ada di /etc?

**Pertanyaan:** Mengapa file konfigurasi diletakkan di `/etc` dan bukan di tempat lain?

**Analisis:**

```
┌─────────────────────────────────────────────────────────────────────────┐
│              STUDI KASUS: KENAPA CONFIG DI /etc?                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  SEJARAH:                                                               │
│  ─────────                                                              │
│  • /etc awalnya = "et cetera" (dan lain-lain)                          │
│  • Di Unix awal, /etc berisi file yang tidak masuk kategori lain       │
│  • Seiring waktu, /etc khusus untuk konfigurasi                        │
│                                                                         │
│  ALASAN TEKNIS:                                                         │
│  ───────────────                                                        │
│                                                                         │
│  1. SEPARATION OF CONCERNS                                              │
│     ┌─────────────────────────────────────────────────────────────┐     │
│     │  /usr/bin/nginx  → Binary (static, shared)                  │     │
│     │  /etc/nginx/     → Config (editable, machine-specific)      │     │
│     │  /var/log/nginx/ → Data (variable)                          │     │
│     └─────────────────────────────────────────────────────────────┘     │
│     Benefit: Update binary tanpa kehilangan config                      │
│                                                                         │
│  2. EASY BACKUP                                                         │
│     ┌───────────────────────────────────────────────────────────────┐   │
│     │  # Backup semua config sekaligus                              │   │
│     │  tar -czvf etc-backup.tar.gz /etc                             │   │
│     │                                                               │   │
│     │  # Restore ke server baru                                     │   │
│     │  tar -xzvf etc-backup.tar.gz -C /                             │   │
│     └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  3. PERMISSION MODEL                                                    │
│     ┌───────────────────────────────────────────────────────────────┐   │
│     │  /etc adalah milik root                                       │   │
│     │  Config sensitif (shadow, ssl keys) terlindungi               │   │
│     │  User biasa tidak bisa modifikasi system config               │   │
│     └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  4. STANDARDIZATION                                                     │
│     ┌───────────────────────────────────────────────────────────────┐   │
│     │  Admin tahu: config pasti di /etc                             │   │
│     │  Scripts tahu: cari config di /etc                            │   │
│     │  Package manager: kelola config di /etc                       │   │
│     └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  ALTERNATIVE - User Config di ~/.config/:                               │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  /etc/nginx/nginx.conf      → System-wide config                │    │
│  │  ~/.config/git/config       → Per-user Git config               │    │
│  │  ~/.bashrc                  → Per-user bash config              │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Studi Kasus 2: Disk Penuh - Di Mana Problemnya?

**Skenario:** Server melaporkan disk 100% full. Bagaimana mencari penyebabnya?

```bash
# 1. Cek overall usage
df -h
# Filesystem      Size  Used Avail Use% Mounted on
# /dev/sda1        50G   47G   0G  100% /

# 2. Cari direktori terbesar
sudo du -sh /* 2>/dev/null | sort -h | tail -10

# 3. Biasanya yang besar: /var, /home, /tmp
sudo du -sh /var/* 2>/dev/null | sort -h | tail -5

# 4. Sering penyebab: /var/log
sudo du -sh /var/log/* | sort -h | tail -5

# 5. Cek log yang membengkak
ls -lhS /var/log/*.log | head -5
```

**Solusi umum:**

```bash
# Bersihkan old logs
sudo journalctl --vacuum-size=100M

# Bersihkan APT cache
sudo apt clean

# Hapus old kernels
sudo apt autoremove

# Setup log rotation
cat /etc/logrotate.d/rsyslog
```

### Studi Kasus 3: File Hilang Setelah Reboot

**Skenario:** Developer menyimpan file di `/tmp` dan hilang setelah reboot.

**Penjelasan:**

```
┌─────────────────────────────────────────────────────────────────────────┐
│              STUDI KASUS: FILE /tmp HILANG                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  MASALAH:                                                               │
│  File di /tmp hilang setelah reboot                                     │
│                                                                         │
│  PENJELASAN:                                                            │
│  /tmp dirancang untuk data SEMENTARA yang:                              │
│  • Bisa dihapus kapan saja                                              │
│  • Tidak dijamin persistent                                             │
│  • Sering di-mount sebagai tmpfs (RAM)                                  │
│  • Dibersihkan oleh systemd-tmpfiles pada boot                          │
│                                                                         │
│  BUKTI:                                                                 │
│  ┌───────────────────────────────────────────────────────────────┐      │
│  │  $ mount | grep tmp                                           │      │
│  │  tmpfs on /tmp type tmpfs (rw,nosuid,nodev)                   │      │
│  │                                                               │      │
│  │  $ cat /usr/lib/tmpfiles.d/tmp.conf                           │      │
│  │  # Clear /tmp on boot                                         │      │
│  │  q /tmp 1777 root root 10d                                    │      │
│  └───────────────────────────────────────────────────────────────┘      │
│                                                                         │
│  SOLUSI - Gunakan direktori yang tepat:                                 │
│  ┌───────────────────────────────────────────────────────────────┐      │
│  │  Data temporary (OK hilang)     → /tmp                        │      │
│  │  Data sementara tapi penting    → /var/tmp                    │      │
│  │  Data yang harus persist        → /home atau /var/lib         │      │
│  │  Application cache              → /var/cache                  │      │
│  └───────────────────────────────────────────────────────────────┘      │
│                                                                         │
│  PERBEDAAN /tmp vs /var/tmp:                                            │
│  • /tmp: dibersihkan setiap boot (10 hari default)                     │
│  • /var/tmp: dibersihkan setelah 30 hari                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## TANTANGAN

### Tantangan 1: Filesystem Detective 

**Level: Pemula**

Temukan jawabannya hanya dengan membaca file di `/proc` dan `/sys`:
1. Berapa jumlah CPU cores?
2. Berapa total RAM (dalam kB)?
3. Berapa uptime sistem (dalam detik)?
4. Apa hostname sistem?
5. Berapa PID dari init/systemd?

### Tantangan 2: Log Hunter 📋

**Level: Menengah**

1. Temukan 10 pesan error terakhir di seluruh `/var/log`
2. Hitung berapa kali sudo digunakan hari ini
3. List semua IP yang mencoba SSH ke server

```bash
# Hints
grep -r "error" /var/log/ 2>/dev/null
grep "sudo" /var/log/auth.log
grep "sshd" /var/log/auth.log | grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+'
```

### Tantangan 3: tukang Bersih-Bersih 🧹

**Level: Menengah**

Buat script untuk:
1. Cek disk usage
2. List top 10 direktori terbesar
3. Bersihkan apt cache
4. Cek dan list old log files (> 30 days)

### Tantangan 4: Config Backup System 💾

**Level: Lanjut**

Buat script backup yang:
1. Backup `/etc` ke tar.gz dengan timestamp
2. Simpan di `/var/backups`
3. Hapus backup lebih dari 7 hari
4. Log hasil ke file

### Tantangan 5: Custom Device 🔧

**Level: Expert**

1. Pelajari cara membuat custom udev rule
2. Buat rule yang menjalankan script saat USB tertentu dicolokkan
3. Script harus log ke file dengan timestamp

---

## SOAL LATIHAN

### Pilihan Ganda

**1.** Direktori mana yang berisi file konfigurasi sistem?
- [ ] A. /bin
- [x] B. /etc
- [ ] C. /var
- [ ] D. /usr

**2.** `/dev/null` adalah...
- [ ] A. File biasa yang kosong
- [x] B. Device yang membuang semua input
- [ ] C. Direktori kosong
- [ ] D. Symlink ke /dev/zero

**3.** File `/etc/passwd` berisi...
- [ ] A. Password user dalam plain text
- [x] B. Database user (tanpa password hash)
- [ ] C. Password root saja
- [ ] D. Daftar password yang valid

**4.** Direktori `/proc` adalah...
- [ ] A. Direktori untuk menyimpan procedures
- [ ] B. Direktori backup
- [x] C. Virtual filesystem untuk info kernel dan proses
- [ ] D. Direktori temporary

**5.** Perbedaan utama `/tmp` dan `/var/tmp` adalah...
- [x] A. /tmp dibersihkan lebih sering dari /var/tmp
- [ ] B. /tmp untuk root, /var/tmp untuk user
- [ ] C. Tidak ada perbedaan
- [ ] D. /var/tmp lebih cepat diakses

### Essay

**1.** Jelaskan filosofi "Everything is a File" di Linux. Berikan 3 contoh konkret bagaimana filosofi ini menguntungkan. (20 poin)

**2.** Bandingkan `/proc` dan `/sys`. Apa perbedaan fokus dan penggunaan keduanya? (15 poin)

**3.** Mengapa file konfigurasi disimpan di `/etc` dan bukan bersama dengan binary-nya di `/usr`? Jelaskan minimal 3 alasan. (20 poin)

**4.** Jelaskan perbedaan antara `/bin`, `/sbin`, `/usr/bin`, dan `/usr/local/bin`. Kapan menggunakan masing-masing? (20 poin)

**5.** Seorang user mengeluh file-nya di `/tmp` hilang setelah reboot. Jelaskan mengapa ini terjadi dan apa solusinya. (15 poin)

### Praktik

**1.** Buat script yang menampilkan:
- Versi kernel
- Jumlah CPU
- Total RAM
- Free disk space
- Uptime

Semua informasi harus diambil dari `/proc`, bukan dari command eksternal. (30 poin)

**2.** Buat script untuk monitoring `/var/log` yang:
- Menampilkan ukuran setiap log file
- Highlight file yang >100MB
- Suggest files yang bisa dirotate

(35 poin)

**3.** Buat dokumentasi struktur direktori sistem Anda yang mencakup:
- Tree view /
- Type (directory, symlink, mount point)
- Filesystem untuk setiap mount point
- Disk usage

(35 poin)

---

## RANGKUMAN

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        RANGKUMAN BAB 2                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   FILOSOFI "EVERYTHING IS A FILE":                                    │
│  • Files, directories, devices, sockets, pipes = semua file            │
│  • Unified interface (open, read, write, close)                        │
│  • Memudahkan composability dengan pipes                               │
│                                                                         │
│   DIREKTORI UTAMA:                                                    │
│  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  /        Root directory                                        │    │
│  │  /bin     Essential user commands                               │    │
│  │  /sbin    System/admin commands                                 │    │
│  │  /etc     Configuration files                                   │    │
│  │  /home    User home directories                                 │    │
│  │  /root    Root user home                                        │    │
│  │  /var     Variable data (logs, cache, spool)                    │    │
│  │  /usr     User programs & data                                  │    │
│  │  /proc    Process & kernel info (virtual)                       │    │
│  │  /sys     Kernel/device info (virtual)                          │    │
│  │  /dev     Device files                                          │    │
│  │  /tmp     Temporary files                                       │    │
│  │  /boot    Boot files (kernel, initrd, grub)                     │    │
│  └─────────────────────────────────────────────────────────────────┘    │
│                                                                         │
│   VIRTUAL FILESYSTEMS:                                                │
│  • /proc - Process info, kernel parameters, system info                │
│  • /sys  - Device & driver info, kernel objects                        │
│                                                                         │
│   DEVICE FILES:                                                       │
│  • Block devices (b): disk, partitions                                 │
│  • Character devices (c): terminals, serial                            │
│  • /dev/null, /dev/zero, /dev/urandom - Special devices                │
│                                                                         │
│   PENTING DIINGAT:                                                    │
│  • Config di /etc, binary di /usr/bin, logs di /var/log               │
│  • /tmp dibersihkan saat reboot                                        │
│  • /proc/sys untuk runtime kernel tuning                               │
│  • Modern Linux: /bin dan /sbin adalah symlink ke /usr                 │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## REFERENSI

### Buku
1. **Filesystem Hierarchy Standard** - Linux Foundation
2. **The Linux Command Line** - William Shotts (Chapter 3)
3. **How Linux Works** - Brian Ward (Chapter 2)

### Online Resources
1. **FHS Specification**: https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html
2. **Linux Documentation /proc**: https://www.kernel.org/doc/Documentation/filesystems/proc.txt
3. **Arch Wiki - File System**: https://wiki.archlinux.org/title/File_systems
4. **Man pages**: `man hier`, `man proc`, `man sysfs`

### Commands Reference
```bash
man hier          # Filesystem hierarchy description
man proc          # proc filesystem documentation
man sysfs         # sysfs documentation
man file-hierarchy # systemd file hierarchy
```

---

## GLOSARIUM

| Istilah | Definisi |
|---------|----------|
| **FHS** | Filesystem Hierarchy Standard - standar struktur direktori Linux |
| **Mount point** | Direktori tempat filesystem di-attach ke hierarki |
| **Device file** | Special file yang merepresentasikan hardware device |
| **Block device** | Device yang diakses per block (disk, USB) |
| **Character device** | Device yang diakses per karakter (terminal, serial) |
| **Virtual filesystem** | Filesystem yang tidak menyimpan data di disk |
| **Symlink** | Symbolic link - pointer ke file/direktori lain |
| **Sticky bit** | Permission bit yang mencegah deletion oleh non-owner |
| **tmpfs** | Temporary filesystem yang disimpan di RAM |
| **udev** | Linux device manager untuk dynamic device management |
| **Dotfile** | Hidden file yang dimulai dengan titik |
| **XDG** | XDG Base Directory Specification untuk user config |

---

## NEXT CHAPTER PREVIEW

**Bab 3: Process Management**

Di bab selanjutnya, kita akan mempelajari:
- Process ID (PID) dan hierarki proses
- Parent & child process
- Daemon processes
- Zombie processes
- Tools: ps, top, htop, kill, nice

---

*Selamat! Anda telah menyelesaikan Bab 2. Sekarang Anda memahami struktur sistem Linux dengan baik. Lanjutkan ke praktikum untuk memperdalam pemahaman!*

---

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)  
**Lisensi:** CC BY-SA 4.0
