# BAB 6: FILE SYSTEM & DISK

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)

---

## Daftar Isi

1. [Pendahuluan](#pendahuluan)
2. [Konsep: Disk, Partition, Filesystem](#1-konsep-disk-partition-filesystem)
3. [Block Device dan Identifikasi Disk](#2-block-device-dan-identifikasi-disk)
4. [Mount dan Mount Point](#3-mount-dan-mount-point)
5. [`df` dan `du`: Analisis Penggunaan Disk](#4-df-dan-du-analisis-penggunaan-disk)
6. [ext4 Sekilas](#5-ext4-sekilas)
7. [Swap dan Virtual Memory](#6-swap-dan-virtual-memory)
8. [Partitioning: Konsep dan Observasi](#7-partitioning-konsep-dan-observasi)
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

Filesystem adalah cara Linux menata data di atas disk. Memahami disk, partisi, mount, dan analisis kapasitas adalah skill inti sysadmin—terutama saat menghadapi kasus klasik: **disk penuh**, **mount gagal**, atau **server lambat karena swap**.

Bab ini fokus ke topik yang ada di roadmap:
- Mount (mounting filesystems)
- Partition (disk partitioning)
- `df`, `du` (disk usage)
- ext4
- swap

### Tujuan Pembelajaran

Setelah menyelesaikan bab ini, Anda akan mampu:
1. Menjelaskan beda disk, partisi, dan filesystem
2. Mengidentifikasi block device dengan `lsblk`
3. Memahami konsep mount point dan membaca output `mount`
4. Menganalisis disk usage dengan `df` dan `du`
5. Memahami peran ext4 dan swap secara praktis

### Prasyarat

- Menyelesaikan Bab 1–5
- Akses Linux (lebih nyaman jika punya hak `sudo` untuk lab opsional)

---

## 1. KONSEP: DISK, PARTITION, FILESYSTEM

Urutan sederhananya:

- **Disk**: perangkat penyimpanan (mis. `/dev/sda`, `/dev/nvme0n1`).
- **Partition**: pembagian logis dari disk (mis. `/dev/sda1`, `/dev/nvme0n1p2`).
- **Filesystem**: format penyimpanan data di partisi (mis. ext4, xfs).
- **Mount point**: lokasi di tree direktori Linux tempat filesystem “ditampilkan” (mis. `/`, `/home`).

---

## 2. BLOCK DEVICE DAN IDENTIFIKASI DISK

### 2.1 `lsblk`: daftar perangkat block

```bash
lsblk
lsblk -f
```

Field yang sering dipakai:
- `NAME`: nama device
- `TYPE`: disk/part/loop
- `FSTYPE`: jenis filesystem
- `MOUNTPOINT`: di-mount ke mana
- `UUID`: identitas unik filesystem

### 2.2 `fdisk -l`: informasi partisi

```bash
sudo fdisk -l
```

Catatan: gunakan untuk observasi. Jangan menulis perubahan partisi kecuali Anda benar-benar paham risikonya.

---

## 3. MOUNT DAN MOUNT POINT

### 3.1 Apa itu mount?

Mount adalah proses “menempelkan” filesystem ke sebuah direktori.

Contoh: filesystem root dipasang di `/`.

### 3.2 Melihat filesystem yang sudah di-mount

```bash
mount | head

# Lebih ringkas untuk melihat disk layout
lsblk -f
```

### 3.3 Kesalahan umum

- Lupa buat direktori mount point
- Salah device/UUID
- Tidak punya permission (butuh `sudo`)

---

## 4. `DF` DAN `DU`: ANALISIS PENGGUNAAN DISK

### 4.1 `df`: melihat kapasitas per filesystem

```bash
df -h
```

Ini jawab pertanyaan: “filesystem mana yang penuh?”.

### 4.2 `du`: melihat pemakaian per direktori

```bash
# Total ukuran per item pada direktori sekarang
du -sh *

# Cari direktori terbesar (contoh root)
sudo du -sh /* 2>/dev/null | sort -h | tail -10
```

Ini jawab pertanyaan: “folder mana yang makan space?”.

---

## 5. EXT4 SEKILAS

ext4 adalah filesystem yang sangat umum di Linux.

Hal praktis yang perlu Anda tahu:
- mendukung journaling (lebih aman setelah crash)
- punya UUID yang bisa dipakai di `/etc/fstab`

Lihat tipe filesystem:

```bash
lsblk -f
```

---

## 6. SWAP DAN VIRTUAL MEMORY

Swap adalah area disk yang dipakai kernel saat RAM tidak cukup.

### 6.1 Cek swap

```bash
swapon --show
free -h
```

### 6.2 Kapan swap jadi masalah?

Jika sistem sering swap berat, server bisa terasa sangat lambat (disk jauh lebih lambat dari RAM).

---

## 7. PARTITIONING: KONSEP DAN OBSERVASI

Partitioning adalah proses membagi disk menjadi partisi.

Untuk bab ini, fokusnya adalah **membaca** informasi partisi dan memahami konsep. Jika Anda ingin latihan membuat partisi, lakukan di mesin virtual atau menggunakan lab loopback (opsional) agar tidak merusak disk asli.

Perintah observasi:

```bash
lsblk
lsblk -f
sudo fdisk -l
```

---

## TUTORIAL PRAKTIS

### Tutorial 1: Audit Disk dan Filesystem

```bash
lsblk -f

df -h

mount | head -30
```

### Tutorial 2: Temukan “Top Space Eater”

```bash
# Di home user
cd ~
du -sh * 2>/dev/null | sort -h | tail -10

# Di root (butuh sudo)
sudo du -sh /* 2>/dev/null | sort -h | tail -10
```

### Tutorial 3: Cek Swap dan Dampaknya

```bash
free -h
swapon --show
```

### Tutorial 4 (Opsional, Aman): Lab Filesystem via Loopback

Tujuan: membuat “disk virtual” berupa file, lalu memformat dan mount untuk latihan.

> Peringatan: tetap butuh `sudo` dan akan melakukan `mkfs` ke loop device (bukan disk asli). Pastikan Anda paham command yang dijalankan.

```bash
# Buat file 200MB
fallocate -l 200M /tmp/disk-demo.img

# Format file sebagai ext4
sudo mkfs.ext4 /tmp/disk-demo.img

# Buat mount point
sudo mkdir -p /mnt/disk-demo

# Mount sebagai loop
sudo mount -o loop /tmp/disk-demo.img /mnt/disk-demo

# Tes tulis data
sudo sh -c 'echo hello > /mnt/disk-demo/hello.txt'
ls -la /mnt/disk-demo

# Unmount
sudo umount /mnt/disk-demo

# Bersihkan
sudo rmdir /mnt/disk-demo
rm -f /tmp/disk-demo.img
```

---

## PRAKTIKUM

### Praktikum 1: Laporan Disk Layout

**Durasi:** 45 menit

**Tugas:**
1. Jalankan `lsblk -f` dan `df -h`
2. Catat:
   - filesystem mana yang di-mount ke `/`
   - filesystem mana yang di-mount ke `/home` (jika ada)
   - tipe filesystem yang digunakan (ext4/xfs/dll)
3. Tulis ringkasan 5–10 baris

```bash
lsblk -f
df -h
```

### Praktikum 2: Disk Full Drill (Simulasi Analisis)

**Durasi:** 60 menit

**Tujuan:** membiasakan alur analisis saat “disk penuh”.

**Tugas:**
1. Tentukan satu filesystem target (mis. `/` atau `/home`) dari `df -h`
2. Gunakan `du` untuk menemukan direktori yang paling besar
3. Buat daftar 5 direktori terbesar

```bash
df -h

# pilih salah satu mount point, contoh /
sudo du -sh /* 2>/dev/null | sort -h | tail -10
```

### Praktikum 3: Swap Observation

**Durasi:** 30 menit

**Tugas:**
1. Catat total RAM dan swap
2. Catat nilai swap yang terpakai
3. Jika swap terpakai besar, tulis dugaan kenapa

```bash
free -h
swapon --show
```

---

## STUDI KASUS

### Studi Kasus 1: Disk 100% Full

**Skenario:** Alert: “Disk usage 100%”.

**Alur cepat:**
1. `df -h` untuk tahu filesystem mana yang penuh
2. `du` untuk cari direktori terbesar
3. Fokus area yang sering bengkak: `/var/log`, `/var/lib`, `/home`, `/tmp`

```bash
df -h
sudo du -sh /* 2>/dev/null | sort -h | tail -10
sudo du -sh /var/* 2>/dev/null | sort -h | tail -10
```

### Studi Kasus 2: Mount Gagal saat Boot

**Skenario:** server masuk emergency mode karena entri `/etc/fstab` salah.

**Checklist:**
1. Cek apakah UUID/DEVICE benar
2. Cek filesystem type
3. Cek apakah mount point ada

```bash
# Observasi UUID
auto="$(lsblk -f | head -20)"; echo "$auto"

# Cek fstab
cat /etc/fstab
```

Catatan: editing `fstab` butuh hati-hati; salah satu karakter bisa bikin boot gagal.

### Studi Kasus 3: Server Lambat karena Swap

**Skenario:** RAM penuh, swap naik, sistem terasa “berat”.

**Checklist:**
1. Cek `free -h` dan `swapon --show`
2. Cari proses pemakan memory (Bab 3)

```bash
free -h
swapon --show
ps aux --sort=-%mem | head -10
```

---

## TANTANGAN

### Tantangan 1: Mount Detective

**Level: Pemula**

1. Dari `lsblk -f`, tentukan filesystem untuk `/`
2. Tuliskan:
   - device
   - fstype
   - UUID (jika ada)

### Tantangan 2: Du Master

**Level: Menengah**

Buat command yang menampilkan **10 direktori terbesar** di home Anda, urut dari kecil ke besar, ukuran human-readable.

---

## SOAL LATIHAN

### Pilihan Ganda

**1.** `df -h` paling tepat digunakan untuk...
- [x] A. Melihat kapasitas free/used per filesystem
- [ ] B. Melihat ukuran file satu per satu
- [ ] C. Membuat filesystem
- [ ] D. Mengubah permission

**2.** `du -sh *` paling tepat digunakan untuk...
- [ ] A. Menampilkan mount points
- [x] B. Mengukur ukuran direktori/file
- [ ] C. Membuat partisi
- [ ] D. Mengaktifkan swap

**3.** Mount point adalah...
- [ ] A. Nama partisi
- [x] B. Direktori tempat filesystem ditampilkan
- [ ] C. File konfigurasi network
- [ ] D. Tipe filesystem

### Essay

**1.** Jelaskan perbedaan disk, partition, filesystem, dan mount point dengan contoh. (25 poin)

**2.** Jelaskan alur troubleshooting ketika disk penuh. Tools apa yang dipakai dan urutannya? (25 poin)

---

## RANGKUMAN

- Disk → partisi → filesystem → dimount ke mount point.
- `lsblk` membantu memahami layout disk/partisi/filesystem.
- `mount` menunjukkan filesystem yang sedang ter-mount.
- `df` melihat kapasitas per filesystem; `du` mencari folder/file pemakan space.
- ext4 adalah filesystem umum dan stabil.
- swap membantu saat RAM habis, tapi swap berat bisa membuat sistem sangat lambat.

---

## REFERENSI

- `man df`
- `man du`
- `man lsblk`
- `man mount`
- `man fdisk`
- `man swapon`
- `man fstab`

---

## GLOSARIUM

- **Block device**: perangkat penyimpanan berbasis blok (disk/partisi).
- **Partition**: pembagian logis pada disk.
- **Filesystem**: format penyimpanan data (ext4, xfs, dll).
- **Mount**: menempelkan filesystem ke direktori.
- **Mount point**: direktori tujuan mount.
- **Swap**: area disk untuk memperluas RAM secara virtual.
