# BAB 5: SYSTEM SERVICE & SYSTEMD

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)

---

## Daftar Isi

1. [Pendahuluan](#pendahuluan)
2. [Konsep: Daemon dan Service](#1-konsep-daemon-dan-service)
3. [Kenalan dengan systemd](#2-kenalan-dengan-systemd)
4. [Unit systemd: service, socket, timer, target](#3-unit-systemd-service-socket-timer-target)
5. [`systemctl`: Manajemen Service](#4-systemctl-manajemen-service)
6. [Autostart: `enable` vs `start`](#5-autostart-enable-vs-start)
7. [`journalctl`: Analisis Log](#6-journalctl-analisis-log)
8. [Lokasi Unit File dan Override](#7-lokasi-unit-file-dan-override)
9. [Tutorial Praktis](#tutorial-praktis)
10. [Praktikum](#praktikum)
11. [Studi Kasus](#studi-kasus)
12. [Tantangan](#tantangan)
13. [Soal Latihan](#soal-latihan)
14. [Rangkuman](#rangkuman)
15. [Referensi](#referensi)
16. [Glosarium](#glosarium)

---

## PENDAHULUAN

Di Linux modern, banyak proses “layanan” berjalan di background: SSH, cron, web server, database, dsb. Layanan seperti ini biasanya dikelola oleh **init system**. Pada mayoritas distro populer, init system tersebut adalah **systemd**.

Di bab ini, Anda belajar:
- Apa itu daemon dan service
- Cara mengontrol service dengan `systemctl`
- Cara mengaktifkan autostart (enable/disable)
- Cara membaca log service dengan `journalctl`

### Tujuan Pembelajaran

Setelah menyelesaikan bab ini, Anda akan mampu:
1. Menjelaskan apa itu daemon dan bedanya dengan proses biasa
2. Memahami peran systemd sebagai PID 1 dan service manager
3. Mengelola service dengan `systemctl` (start/stop/restart/status)
4. Mengatur autostart dengan `enable/disable`
5. Menganalisis log service dengan `journalctl`
6. Memahami konsep unit file dan override konfigurasi

### Prasyarat

- Menyelesaikan Bab 1–4
- Akses ke Linux yang memakai systemd (Ubuntu/Debian/RHEL modern)

---

## 1. KONSEP: DAEMON DAN SERVICE

- **Daemon**: proses background yang berjalan lama dan biasanya menyediakan fungsi tertentu (contoh: `sshd`, `cron`, `nginx`).
- **Service**: istilah operasional untuk daemon + konfigurasi + mekanisme start/stop.

Ciri umum daemon:
- berjalan tanpa interaksi terminal
- start saat boot atau saat dibutuhkan
- log-nya tersimpan di file log atau journald

---

## 2. KENALAN DENGAN SYSTEMD

### 2.1 Systemd sebagai PID 1

Pada sistem yang memakai systemd, PID 1 biasanya `systemd`.

```bash
ps -p 1 -o pid,ppid,comm,args
```

### 2.2 Systemd itu apa?

Secara praktis, systemd menyediakan:
- manajemen service (start/stop/restart)
- dependency (service A butuh service B)
- logging terpusat (journald)
- unit lain seperti timer (pengganti cron di beberapa skenario)

---

## 3. UNIT SYSTEMD: SERVICE, SOCKET, TIMER, TARGET

Systemd mengelola “unit”. Beberapa unit yang umum:

| Unit | Akhiran | Fungsi |
|------|---------|--------|
| Service | `.service` | proses/daemon utama |
| Socket | `.socket` | socket activation |
| Timer | `.timer` | jadwal menjalankan unit |
| Target | `.target` | grup unit (milestone boot) |

Lihat daftar unit service:

```bash
systemctl list-units --type=service --all | head
```

---

## 4. `SYSTEMCTL`: MANAJEMEN SERVICE

### 4.1 Status service

```bash
systemctl status ssh
# atau
systemctl status ssh.service
```

Di output `status`, perhatikan:
- `Active:` (running/failed)
- `Loaded:` (enabled/disabled, path unit file)
- log ringkas (baris terakhir)

### 4.2 Start/Stop/Restart

```bash
sudo systemctl start <service>
sudo systemctl stop <service>
sudo systemctl restart <service>

# Reload: minta service reload config (jika mendukung)
sudo systemctl reload <service>

# Reload-or-restart: kalau tidak support reload, restart
sudo systemctl reload-or-restart <service>
```

### 4.3 Cek apakah service enabled

```bash
systemctl is-enabled <service>
systemctl is-active <service>
```

### 4.4 Lihat unit file

```bash
systemctl cat <service>
```

---

## 5. AUTOSTART: `ENABLE` VS `START`

Ini sumber kebingungan paling sering:

- `start`: menjalankan service **sekarang** (runtime)
- `enable`: mengatur agar service **otomatis start saat boot** (persistent)

Contoh:

```bash
# Start sekarang
sudo systemctl start ssh

# Enable autostart saat boot
sudo systemctl enable ssh

# Disable autostart
sudo systemctl disable ssh

# Stop sekarang
sudo systemctl stop ssh
```

Tips:
- Jika Anda butuh service jalan sekarang dan setelah reboot: lakukan `enable --now`.

```bash
sudo systemctl enable --now <service>
```

---

## 6. `JOURNALCTL`: ANALISIS LOG

### 6.1 Log untuk service tertentu

```bash
# Log untuk unit tertentu
journalctl -u <service>

# Lihat log terbaru
journalctl -u <service> -n 50

# Follow (seperti tail -f)
journalctl -u <service> -f

# Filter waktu
journalctl -u <service> --since "1 hour ago"
```

### 6.2 Lihat log boot

```bash
# Boot sekarang
journalctl -b

# Boot sebelumnya
journalctl -b -1
```

### 6.3 Level prioritas (error/warning)

```bash
# Prioritas error (3) sampai emerg (0)
journalctl -p err -b
```

---

## 7. LOKASI UNIT FILE DAN OVERRIDE

### 7.1 Lokasi umum

Umumnya unit file berada di:
- `/usr/lib/systemd/system/` (atau di beberapa distro: `/lib/systemd/system/`) — unit dari package
- `/etc/systemd/system/` — unit custom/override admin

Cari lokasi unit:

```bash
systemctl status <service> | sed -n '1,15p'
systemctl show -p FragmentPath <service>
```

### 7.2 Override dengan drop-in (cara aman)

Daripada mengedit unit file package langsung, gunakan override:

```bash
sudo systemctl edit <service>
```

Ini akan membuat file drop-in di `/etc/systemd/system/<service>.service.d/override.conf`.

Setelah ubah unit:

```bash
sudo systemctl daemon-reload
sudo systemctl restart <service>
```

---

## TUTORIAL PRAKTIS

### Tutorial 1: Identifikasi Service yang Berjalan

```bash
systemctl list-units --type=service --state=running | head -25
```

### Tutorial 2: Baca Status dan Log Service

Pilih service yang ada (misalnya `ssh`, `cron`, `systemd-journald`).

```bash
systemctl status ssh
journalctl -u ssh -n 30
```

### Tutorial 3: Enable vs Start

```bash
systemctl is-enabled ssh
systemctl is-active ssh

# (Jangan jalankan stop/disable pada mesin production tanpa paham dampaknya)
```

### Tutorial 4: Cari Unit File dan Lihat Isinya

```bash
systemctl show -p FragmentPath ssh
systemctl cat ssh
```

---

## PRAKTIKUM

### Praktikum 1: Service Health Check

**Durasi:** 45 menit

**Tugas:**
1. Pilih 3 service di sistem Anda
2. Untuk masing-masing, catat:
   - status aktif (active/inactive/failed)
   - enabled/disabled
   - 20 baris log terakhir
3. Jika ada yang failed, catat error utamanya

```bash
# contoh untuk satu service
systemctl status <service>
systemctl is-enabled <service>
journalctl -u <service> -n 20
```

### Praktikum 2: Simulasi Service Gagal Start (Aman)

**Durasi:** 60 menit

**Tujuan:** belajar membaca status dan journald saat service gagal.

Buat service sederhana yang sengaja error (tanpa menyentuh service penting):

1) Buat script:

```bash
sudo tee /usr/local/bin/fail-demo.sh > /dev/null << 'EOF'
#!/bin/bash
echo "fail-demo starting"
exit 1
EOF

sudo chmod 755 /usr/local/bin/fail-demo.sh
```

2) Buat unit file:

```bash
sudo tee /etc/systemd/system/fail-demo.service > /dev/null << 'EOF'
[Unit]
Description=Fail Demo Service

[Service]
Type=simple
ExecStart=/usr/local/bin/fail-demo.sh

[Install]
WantedBy=multi-user.target
EOF
```

3) Jalankan dan investigasi:

```bash
sudo systemctl daemon-reload
sudo systemctl start fail-demo
systemctl status fail-demo
journalctl -u fail-demo -n 50
```

4) Cleanup:

```bash
sudo systemctl stop fail-demo 2>/dev/null || true
sudo systemctl disable fail-demo 2>/dev/null || true
sudo rm -f /etc/systemd/system/fail-demo.service
sudo rm -f /usr/local/bin/fail-demo.sh
sudo systemctl daemon-reload
```

---

## STUDI KASUS

### Studi Kasus 1: Service “active” tapi aplikasi tidak bisa diakses

**Skenario:** `systemctl status` bilang aktif, tapi port tidak terbuka atau aplikasi tetap error.

**Checklist:**
1. Cek log service: `journalctl -u <service> -f`
2. Pastikan service bind ke alamat/port yang benar
3. Cek apakah ada dependency yang belum siap

```bash
systemctl status <service>
journalctl -u <service> -n 100
```

### Studi Kasus 2: Service auto-restart terus

**Gejala:** log berulang dan CPU tinggi.

**Checklist:**
1. Baca bagian `Restart=` di unit file
2. Lihat error root cause di journal

```bash
systemctl cat <service>
journalctl -u <service> -n 200
```

---

## TANTANGAN

### Tantangan 1: Log Time Travel

**Level: Pemula**

1. Ambil log untuk satu service `--since "1 hour ago"`
2. Ambil log boot sebelumnya (`-b -1`)
3. Jelaskan bedanya

### Tantangan 2: Unit File Reader

**Level: Menengah**

1. Pilih satu service
2. Baca unit file-nya dengan `systemctl cat`
3. Temukan `ExecStart=` dan jelaskan command apa yang sebenarnya dijalankan

---

## SOAL LATIHAN

### Pilihan Ganda

**1.** Perbedaan utama `systemctl start` dan `systemctl enable` adalah...
- [x] A. `start` menjalankan sekarang, `enable` mengatur autostart saat boot
- [ ] B. `enable` menjalankan sekarang, `start` mengatur autostart
- [ ] C. Keduanya sama
- [ ] D. Keduanya hanya untuk melihat status

**2.** Perintah untuk melihat log service tertentu adalah...
- [ ] A. `systemctl log <service>`
- [x] B. `journalctl -u <service>`
- [ ] C. `tail -u <service>`
- [ ] D. `cat /var/log/<service>` selalu

**3.** Tempat yang aman untuk override unit file package adalah...
- [ ] A. edit langsung `/usr/lib/systemd/system/<service>.service`
- [x] B. `systemctl edit <service>` (drop-in override)
- [ ] C. hapus unit file dan buat ulang
- [ ] D. rename unit file

### Essay

**1.** Jelaskan workflow troubleshooting service yang gagal start menggunakan `systemctl status` dan `journalctl`. (25 poin)

**2.** Jelaskan kenapa mengedit unit file di `/usr/lib/systemd/system/` langsung bukan praktik yang baik. (20 poin)

---

## RANGKUMAN

- Daemon adalah proses background; service adalah cara operasional mengelolanya.
- Systemd (sering PID 1) mengelola unit seperti `.service` dan menyediakan logging via journal.
- `systemctl` dipakai untuk start/stop/restart/status.
- `enable/disable` mengatur autostart saat boot; `start/stop` untuk kondisi runtime.
- `journalctl -u <service>` adalah senjata utama untuk membaca log service.
- Gunakan drop-in override (`systemctl edit`) agar konfigurasi tidak hilang saat update package.

---

## REFERENSI

- `man systemctl`
- `man systemd`
- `man systemd.service`
- `man journalctl`

---

## GLOSARIUM

- **systemd**: init system dan service manager di Linux modern.
- **Unit**: objek yang dikelola systemd (service, socket, timer, target, dll).
- **Service**: unit yang menjalankan proses/daemon.
- **Enable**: mengatur unit start otomatis saat boot.
- **Journal/journald**: sistem logging terpusat milik systemd.
