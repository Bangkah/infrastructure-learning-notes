# BAB 3: PROCESS MANAGEMENT

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)

---

## Daftar Isi

1. [Pendahuluan](#pendahuluan)
2. [Konsep Dasar: Program vs Process](#1-konsep-dasar-program-vs-process)
3. [PID, PPID, dan Hierarki Proses](#2-pid-ppid-dan-hierarki-proses)
4. [Process State (R/S/D/T/Z)](#3-process-state-rsd-tz)
5. [Scheduler, Time Slice, dan Context Switch](#4-scheduler-time-slice-dan-context-switch)
6. [Foreground/Background dan Job Control](#5-foregroundbackground-dan-job-control)
7. [Signals dan `kill`](#6-signals-dan-kill)
8. [Priority: `nice` dan `renice`](#7-priority-nice-dan-renice)
9. [Daemon, Orphan, dan Zombie](#8-daemon-orphan-dan-zombie)
10. [Monitoring dengan `ps`, `top`, dan `htop`](#9-monitoring-dengan-ps-top-dan-htop)
11. [Membaca Informasi Proses lewat `/proc`](#10-membaca-informasi-proses-lewat-proc)
12. [Tutorial Praktis](#tutorial-praktis)
13. [Praktikum](#praktikum)
14. [Studi Kasus](#studi-kasus)
15. [Tantangan](#tantangan)
16. [Soal Latihan](#soal-latihan)
17. [Rangkuman](#rangkuman)
18. [Referensi](#referensi)
19. [Glosarium](#glosarium)

---

## PENDAHULUAN

Bab ini membahas **manajemen proses** di Linux: bagaimana program menjadi proses, bagaimana Linux melacaknya, bagaimana Anda memonitor dan mengontrolnya, dan kenapa konsep seperti **signals, priority, daemon, zombie** sangat penting dalam pekerjaan sysadmin/DevOps.

### Tujuan Pembelajaran

Setelah menyelesaikan bab ini, Anda akan mampu:
1. Menjelaskan perbedaan program dan process
2. Memahami PID/PPID dan hierarki parent-child
3. Membaca dan menginterpretasikan state proses (R/S/D/T/Z)
4. Mengontrol proses dengan job control dan signals
5. Mengatur prioritas proses dengan `nice`/`renice`
6. Mengidentifikasi daemon, orphan, dan zombie
7. Menggunakan `ps`, `top`, dan `htop` untuk monitoring
8. Menggunakan `/proc` untuk inspeksi detail proses

### Prasyarat

- Menyelesaikan Bab 1 dan Bab 2
- Familiar dengan command line dasar (`cd`, `ls`, `cat`, `grep`)

---

## 1. KONSEP DASAR: PROGRAM VS PROCESS

### 1.1 Definisi

- **Program**: file executable di disk (misalnya `/usr/bin/python3`, `/bin/bash`). Sifatnya pasif.
- **Process (Proses)**: program yang **sedang berjalan** (aktif) dan memiliki konteks eksekusi: PID, memory map, file descriptor, environment, state, dan lain-lain.

Analogi:
- Program = resep masakan
- Process = aktivitas memasak yang sedang berlangsung

### 1.2 Apa saja yang dimiliki sebuah proses?

Secara umum proses punya:
- **PID** (Process ID)
- **PPID** (Parent PID)
- **User/Owner**
- **State** (running/sleeping/zombie)
- **File descriptors** (stdin/stdout/stderr, socket, file)
- **Address space** (stack/heap/text/data)
- **Scheduling info** (priority/nice)

---

## 2. PID, PPID, DAN HIERARKI PROSES

### 2.1 PID dan PPID

Linux mengidentifikasi proses dengan **PID** (angka). Proses dibuat oleh proses lain (kecuali proses pertama), sehingga ada **PPID**.

Command cepat:

```bash
# Lihat PID shell sekarang
echo $$

# Lihat PID + PPID + command
ps -o pid,ppid,cmd -p $$
```

### 2.2 Tree proses

Proses membentuk hierarki. Cara melihat pohon proses:

```bash
pstree -p | head -50

# Fokus ke satu PID
pstree -p $$
```

Jika `pstree` tidak ada:

```bash
sudo apt install psmisc
```

### 2.3 Proses pertama: PID 1

Di Linux modern, PID 1 biasanya adalah `systemd` (atau init lain). Dia menjadi “orang tua” untuk proses yang orphan.

```bash
ps -p 1 -o pid,ppid,comm,args
```

---

## 3. PROCESS STATE (R/S/D/T/Z)

Linux menandai state proses. Anda akan sering melihat huruf seperti `R`, `S`, `D`, `T`, `Z`.

### 3.1 Ringkasannya

| State | Nama | Arti singkat |
|------:|------|--------------|
| `R` | Running | Sedang berjalan / runnable (siap jalan) |
| `S` | Sleeping | Menunggu event (biasanya interruptible sleep) |
| `D` | Uninterruptible sleep | Menunggu I/O kernel; susah di-interrupt |
| `T` | Stopped/Traced | Dihentikan (job control) atau di-trace debugger |
| `Z` | Zombie | Proses sudah exit tapi belum “diambil” statusnya oleh parent |

Lihat state dari proses:

```bash
ps -o pid,stat,comm -p 1
ps -eo pid,stat,comm | head
```

### 3.2 Kenapa ada `D`?

`D` sering muncul saat proses menunggu operasi I/O kernel (misalnya disk atau NFS). Ini penting untuk diagnosis “kenapa proses stuck dan tidak bisa di-kill dengan SIGTERM”.

---

## 4. SCHEDULER, TIME SLICE, DAN CONTEXT SWITCH

### 4.1 Scheduler (penjadwal)

Scheduler menentukan proses mana yang dapat jatah CPU. Anda tidak perlu hafal detail implementasi, tapi perlu mengerti efeknya:
- CPU dibagi di antara banyak proses
- Proses bisa preempted (dipotong) untuk memberi jatah ke proses lain

### 4.2 Time slice

Time slice adalah “jatah waktu” CPU untuk proses sebelum scheduler berpindah ke proses lain.

### 4.3 Context switch

Context switch adalah saat CPU berpindah menjalankan proses/threads lain: register disimpan, proses lain dipulihkan.

Indikator kasar di sistem:

```bash
# Lihat context switches secara umum
vmstat 1 5
```

---

## 5. FOREGROUND/BACKGROUND DAN JOB CONTROL

Shell memiliki konsep **job control** untuk mengelola proses foreground dan background.

### 5.1 Menjalankan background

```bash
sleep 60 &

# Lihat jobs yang dikelola shell
jobs
```

### 5.2 Pindah foreground ↔ background

```bash
# Jalankan perintah di foreground
sleep 300

# Tekan Ctrl+Z untuk stop sementara (T state)

# Lanjutkan di background
bg

# Kembalikan ke foreground
fg
```

### 5.3 `nohup` (tetap jalan setelah logout)

```bash
nohup sleep 600 > nohup.out 2>&1 &
```

Catatan: `nohup` berguna saat Anda jalanin command via SSH dan ingin proses tetap hidup setelah sesi putus.

---

## 6. SIGNALS DAN `kill`

### 6.1 Apa itu signal?

Signal adalah mekanisme notifikasi ke proses. “Kill” tidak selalu berarti membunuh—`kill` adalah **pengirim signal**.

Lihat daftar signal:

```bash
kill -l
```

### 6.2 Signal penting

| Signal | Nama | Kegunaan |
|-------:|------|----------|
| `SIGTERM` (15) | terminate | Cara “baik” meminta proses berhenti |
| `SIGKILL` (9) | kill | Memaksa proses mati (tidak bisa ditangkap) |
| `SIGINT` (2) | interrupt | Biasanya dari Ctrl+C |
| `SIGSTOP` (19) | stop | Menghentikan proses (tidak bisa ditangkap) |
| `SIGCONT` (18) | continue | Melanjutkan proses yang stopped |
| `SIGHUP` (1) | hangup | Sering dipakai untuk reload config daemon |

### 6.3 Contoh penggunaan

```bash
# Cari PID suatu proses
pgrep -a sshd

# Minta berhenti secara halus
sudo kill -TERM <PID>

# Jika bandel
sudo kill -KILL <PID>

# Stop dan lanjutkan
kill -STOP <PID>
kill -CONT <PID>
```

Tips: gunakan SIGTERM dulu. SIGKILL dipakai kalau proses benar-benar stuck atau tidak merespons.

---

## 7. PRIORITY: `nice` DAN `renice`

Linux punya mekanisme prioritas.

- **nice value**: umumnya dari `-20` (prioritas tinggi) sampai `19` (prioritas rendah)
- Nilai nice lebih besar = proses “lebih baik hati” (ngalah)

### 7.1 Jalankan proses dengan nice tertentu

```bash
# Jalankan dengan prioritas lebih rendah
nice -n 10 sha256sum /dev/zero > /dev/null
```

### 7.2 Ubah nice proses yang sudah berjalan

```bash
# Lihat NI (nice) dan PR (priority)
ps -o pid,ni,pri,comm -p <PID>

# Turunkan prioritas (lebih besar nice)
sudo renice 10 -p <PID>
```

Catatan: menaikkan prioritas (nice lebih kecil) biasanya butuh root.

---

## 8. DAEMON, ORPHAN, DAN ZOMBIE

### 8.1 Daemon

**Daemon** adalah proses background yang biasanya berjalan lama untuk memberi layanan (contoh: `sshd`, `cron`, `nginx`).

Ciri umum:
- Tidak terikat terminal (no controlling TTY)
- Biasanya start saat boot via `systemd`

### 8.2 Orphan process

Orphan adalah proses yang parent-nya mati duluan. Orphan akan diadopsi oleh PID 1.

### 8.3 Zombie process

Zombie terjadi ketika:
1. Child exit
2. Parent belum memanggil `wait()`/`waitpid()` untuk mengambil exit status

Zombie tidak memakai CPU, tapi masih memegang entry di process table. Jika banyak zombie, itu indikasi bug pada parent process.

Cara melihat zombie:

```bash
ps -eo pid,ppid,stat,comm | awk '$3 ~ /Z/ {print}'
```

---

## 9. MONITORING DENGAN `ps`, `top`, DAN `htop`

### 9.1 `ps`

`ps` adalah snapshot.

```bash
# Tampilkan proses lengkap
ps aux | head

# Urut berdasarkan penggunaan CPU
ps aux --sort=-%cpu | head -10

# Urut berdasarkan penggunaan memory
ps aux --sort=-%mem | head -10

# Pilih kolom tertentu
ps -eo pid,ppid,user,stat,%cpu,%mem,etime,cmd | head
```

### 9.2 `top`

`top` adalah realtime.

```bash
top
```

Shortcut penting di `top`:
- `P` sort CPU
- `M` sort memory
- `k` kill (kirim signal)
- `1` tampilkan per-core
- `q` keluar

### 9.3 `htop`

`htop` lebih interaktif.

```bash
sudo apt install htop
htop
```

---

## 10. MEMBACA INFORMASI PROSES LEWAT `/proc`

Linux menyediakan detail proses di `/proc/<PID>/`.

Contoh:

```bash
PID=$$

# Informasi status
cat /proc/$PID/status | head -30

# Command line (dipisah NUL)
tr '\0' ' ' < /proc/$PID/cmdline

# Environment variables
tr '\0' '\n' < /proc/$PID/environ | head

# File descriptor
ls -la /proc/$PID/fd | head

# Memory map
cat /proc/$PID/maps | head
```

---

## TUTORIAL PRAKTIS

### Tutorial 1: Kenalan dengan Identitas Proses

```bash
echo "My shell PID: $$"
ps -o pid,ppid,tty,stat,etime,cmd -p $$
```

### Tutorial 2: Melihat Tree Proses dan Parent/Child

```bash
pstree -p $$

# Buat child sederhana
(sleep 60) &
CHILD_PID=$!

ps -o pid,ppid,stat,cmd -p $CHILD_PID
pstree -p $$ | head -50

kill $CHILD_PID
```

### Tutorial 3: Job Control (bg/fg) + Stop/Continue

```bash
sleep 300

# Tekan Ctrl+Z
jobs
bg
jobs
fg

# Tekan Ctrl+C untuk mengakhiri
```

### Tutorial 4: Signals dengan Aman

```bash
sleep 1000 &
PID=$!

# Minta berhenti halus
kill -TERM $PID

# Verifikasi apakah masih hidup
ps -p $PID -o pid,stat,cmd
```

### Tutorial 5: Prioritas Proses

```bash
# Jalankan proses CPU-bound dengan nice tinggi (lebih "ngalah")
( nice -n 15 yes > /dev/null ) &
PID=$!

ps -o pid,ni,pri,stat,cmd -p $PID

# Ubah nice
sudo renice 5 -p $PID
ps -o pid,ni,pri,stat,cmd -p $PID

# Stop
kill -KILL $PID
```

### Tutorial 6: Membaca Detail dari /proc

```bash
PID=1
cat /proc/$PID/status | head -25
cat /proc/$PID/cmdline | tr '\0' ' '
ls -la /proc/$PID/fd | head
```

---

## PRAKTIKUM

### Praktikum 1: Investigasi Proses “Berat”

**Durasi:** 45 menit

**Tugas:**
1. Jalankan `top` dan identifikasi proses yang paling banyak memakai CPU dan memory
2. Ambil PID proses tersebut
3. Tampilkan detailnya dengan `ps` dan `/proc`
4. Jelaskan:
   - User pemilik proses
   - State proses
   - Berapa lama proses berjalan (etime)
   - File descriptor apa saja yang terbuka (cukup 5 pertama)

**Commands yang digunakan:**

```bash
top
ps -o pid,ppid,user,stat,%cpu,%mem,etime,cmd -p <PID>
cat /proc/<PID>/status
ls -la /proc/<PID>/fd | head
```

### Praktikum 2: Simulasi Zombie (C)

**Durasi:** 60 menit

**Tujuan:** Melihat zombie terjadi ketika parent tidak melakukan `wait()`.

Buat file `zombie_demo.c`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        // Child exit cepat
        printf("Child exiting. PID=%d\n", getpid());
        _exit(0);
    }

    // Parent: sengaja tidur tanpa wait
    printf("Parent sleeping. PID=%d, child PID=%d\n", getpid(), pid);
    sleep(30);

    return 0;
}
```

Compile dan jalankan:

```bash
gcc -o zombie_demo zombie_demo.c
./zombie_demo &
PARENT_PID=$!

# Cari child zombie
ps -eo pid,ppid,stat,comm | awk '$2=='"$PARENT_PID"' {print}'

# Lihat state parent juga
ps -o pid,ppid,stat,cmd -p $PARENT_PID
```

**Pertanyaan:**
1. Apakah Anda melihat `Z` di kolom `STAT`?
2. Kenapa zombie hilang setelah parent selesai?

### Praktikum 3: “Graceful shutdown” vs “force kill”

**Durasi:** 45 menit

**Tugas:**
1. Jalankan satu proses `sleep 1000` sebagai simulasi service
2. Hentikan dengan SIGTERM
3. Jalankan lagi, hentikan dengan SIGKILL
4. Jelaskan perbedaan “graceful” vs “force”

```bash
sleep 1000 &
PID=$!
kill -TERM $PID
ps -p $PID

sleep 1000 &
PID=$!
kill -KILL $PID
ps -p $PID
```

---

## STUDI KASUS

### Studi Kasus 1: Proses Tidak Mau Mati

**Skenario:** Anda menjalankan `kill -TERM <PID>` tapi proses tidak berhenti.

**Checklist investigasi:**
1. Apakah proses sedang di state `D` (uninterruptible I/O)?
2. Apakah PID yang Anda kill benar?
3. Apakah proses auto-restart (misalnya dimonitor systemd/supervisor)?

Command:

```bash
ps -o pid,ppid,stat,wchan,cmd -p <PID>

# Cek parent (apakah ini service?)
ps -o pid,ppid,cmd -p <PPID>

# Jika systemd, nanti di Bab 5 akan dibahas:
# systemctl status <service>
```

**Catatan:** Jika state `D`, SIGKILL pun sering tidak langsung “menghapus” proses sampai I/O selesai.

### Studi Kasus 2: CPU 100% tapi “Tidak ada apa-apa”

**Skenario:** Server lambat, tapi Anda tidak melihat proses besar secara jelas.

**Pendekatan:**
1. Gunakan `top` dan sort CPU
2. Cek load average
3. Cek apakah sistem banyak context switch atau I/O wait

```bash
top
cat /proc/loadavg
vmstat 1 5
```

---

## TANTANGAN

### Tantangan 1: Process Hunter

**Level: Pemula**

1. Temukan PID dari proses `sshd` (atau service lain yang ada di mesin Anda)
2. Tampilkan PPID-nya
3. Tampilkan tree prosesnya
4. Ambil 10 baris pertama dari `/proc/<PID>/status`

### Tantangan 2: “Nice Guy” Benchmark

**Level: Menengah**

1. Jalankan dua proses CPU-bound (misal `yes > /dev/null`) bersamaan
2. Beri salah satu `nice -n 15`
3. Amati di `top` pembagian CPU-nya

### Tantangan 3: Cleanup Zombie Detector

**Level: Lanjut**

Buat one-liner yang:
1. Menampilkan semua zombie
2. Mengelompokkan berdasarkan PPID
3. Menampilkan berapa zombie per parent

Hint (silakan modifikasi):

```bash
ps -eo ppid,stat | awk '$2 ~ /Z/ {count[$1]++} END {for (p in count) print p, count[p]}'
```

---

## SOAL LATIHAN

### Pilihan Ganda

**1.** Perbedaan program dan process adalah...
- [ ] A. Program berjalan di RAM, process berjalan di disk
- [x] B. Program adalah file di disk, process adalah program yang sedang dieksekusi
- [ ] C. Program hanya untuk Linux, process hanya untuk Windows
- [ ] D. Tidak ada perbedaan

**2.** State `Z` pada proses berarti...
- [ ] A. Proses menggunakan CPU 100%
- [x] B. Proses sudah exit tapi belum di-wait oleh parent
- [ ] C. Proses sedang tidur
- [ ] D. Proses sedang menunggu input keyboard

**3.** Signal yang umumnya dipakai untuk menghentikan proses dengan “baik” adalah...
- [x] A. SIGTERM
- [ ] B. SIGKILL
- [ ] C. SIGSTOP
- [ ] D. SIGHUP

**4.** Nilai nice yang lebih besar biasanya berarti...
- [ ] A. Prioritas lebih tinggi
- [x] B. Prioritas lebih rendah
- [ ] C. Proses menjadi zombie
- [ ] D. Proses tidak bisa di-kill

**5.** `kill` adalah...
- [ ] A. Selalu membunuh proses
- [x] B. Mengirim signal ke proses
- [ ] C. Menghapus file executable
- [ ] D. Mematikan kernel

### Essay

**1.** Jelaskan apa itu PID dan PPID. Berikan contoh bagaimana proses membentuk hierarki. (15 poin)

**2.** Jelaskan perbedaan SIGTERM dan SIGKILL, serta kapan sebaiknya menggunakan masing-masing. (20 poin)

**3.** Kenapa zombie bisa muncul? Apakah zombie selalu berbahaya? Jelaskan. (20 poin)

**4.** Jelaskan job control di shell: `Ctrl+Z`, `bg`, `fg`, dan `jobs`. (20 poin)

### Praktik

**1.** Pilih satu proses di sistem Anda (selain PID 1). Buat laporan singkat berisi:
- PID/PPID
- State
- Command lengkap
- 5 file descriptor pertama
- 10 baris pertama dari `/proc/<PID>/status`

(25 poin)

---

## RANGKUMAN

- Process adalah program yang sedang berjalan dan punya identitas (PID) serta resource (memory, FD, scheduler info).
- Linux melacak hierarki proses lewat PPID; `pstree` membantu melihat struktur.
- State proses (R/S/D/T/Z) penting untuk troubleshooting.
- Job control mengatur foreground/background pada shell.
- Signals adalah cara standar mengontrol proses; mulai dari SIGTERM sebelum SIGKILL.
- `nice`/`renice` mengatur “kebaikan hati” proses dalam berebut CPU.
- `/proc/<PID>/` menyediakan detail proses yang sangat berguna untuk diagnosis.

---

## REFERENSI

- `man ps`
- `man top`
- `man htop`
- `man kill`
- `man nice`
- `man renice`
- `man proc`
- `man 7 signal`

---

## GLOSARIUM

- **PID**: Process ID, identitas unik proses.
- **PPID**: Parent Process ID.
- **Signal**: Notifikasi/asynchronous event untuk proses.
- **Daemon**: Proses background jangka panjang penyedia layanan.
- **Zombie**: Proses yang sudah exit tapi statusnya belum diambil parent.
- **Orphan**: Proses yang parent-nya mati dan diadopsi PID 1.
- **Nice**: Nilai prioritas “user-space” untuk scheduler (lebih besar = prioritas lebih rendah).
- **Job Control**: Mekanisme shell untuk mengelola proses fg/bg.
