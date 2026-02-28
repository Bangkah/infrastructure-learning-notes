# BAB 4: PERMISSION & SECURITY DASAR

**Penulis:** Muhammad Dhiyaul Atha  
**GitHub:** [Bangkah](https://github.com/Bangkah)

---

## Daftar Isi

1. [Pendahuluan](#pendahuluan)
2. [Konsep Dasar: User, Group, dan Ownership](#1-konsep-dasar-user-group-dan-ownership)
3. [rwx dan Makna Permission pada File vs Direktori](#2-rwx-dan-makna-permission-pada-file-vs-direktori)
4. [Mode Simbolik dan Numeric (755, 644)](#3-mode-simbolik-dan-numeric-755-644)
5. [`chmod`: Mengubah Permission](#4-chmod-mengubah-permission)
6. [`chown` dan `chgrp`: Mengubah Owner/Group](#5-chown-dan-chgrp-mengubah-ownergroup)
7. [`sudo`: Superuser Do](#6-sudo-superuser-do)
8. [Least Privilege (Prinsip Keamanan Dasar)](#7-least-privilege-prinsip-keamanan-dasar)
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

Permission adalah “pagar” keamanan utama di Linux. Hampir semua problem operasional yang sering ditemui sysadmin (service gagal start, aplikasi tidak bisa baca config, user tidak bisa akses folder, dsb.) ujung-ujungnya berhubungan dengan **ownership** dan **permission**.

Bab ini fokus ke hal-hal yang paling sering Anda pakai sehari-hari:
- memahami **rwx**
- memahami angka **755/644**
- mengubah permission dengan `chmod`
- mengubah owner/group dengan `chown`
- memakai `sudo` dengan aman
- menerapkan prinsip **least privilege**

### Tujuan Pembelajaran

Setelah menyelesaikan bab ini, Anda akan mampu:
1. Menjelaskan konsep user, group, owner, dan group owner
2. Menjelaskan arti rwx untuk file dan direktori
3. Mengonversi permission symbolic ↔ numeric (mis. 755, 644)
4. Menggunakan `chmod` (symbolic dan numeric)
5. Menggunakan `chown`/`chgrp` untuk memperbaiki ownership
6. Menggunakan `sudo` dengan benar dan aman
7. Menerapkan least privilege saat mengatur akses

### Prasyarat

- Menyelesaikan Bab 1–3
- Pemahaman dasar command line

---

## 1. KONSEP DASAR: USER, GROUP, DAN OWNERSHIP

Di Linux setiap file/direktori punya:
- **Owner (user)**
- **Group**
- **Permission untuk**: owner, group, dan others

Cek identitas Anda:

```bash
id
whoami
groups
```

Cek owner dan group suatu file:

```bash
ls -l /etc/passwd
# -rw-r--r-- 1 root root ... /etc/passwd
#   ^^^^^^^^   ^^^^ ^^^^
#   perms      owner group
```

---

## 2. RWX DAN MAKNA PERMISSION PADA FILE VS DIREKTORI

Permission ditulis seperti ini:

```text
-rwxr-xr--
 ^^^ ^^^ ^^^
  |   |   |
  |   |   └── others
  |   └────── group
  └────────── owner
```

### 2.1 Makna `r`, `w`, `x` untuk FILE

| Bit | Nama | Arti pada file |
|-----|------|----------------|
| `r` | read | boleh membaca isi file |
| `w` | write | boleh mengubah isi file |
| `x` | execute | boleh menjalankan file sebagai program |

### 2.2 Makna `r`, `w`, `x` untuk DIREKTORI

Untuk direktori, artinya sedikit berbeda:

| Bit | Arti pada direktori |
|-----|----------------------|
| `r` | boleh melihat daftar isi (list) |
| `w` | boleh membuat/menghapus/rename entry di direktori |
| `x` | boleh “masuk” (traverse) dan mengakses file di dalamnya jika tahu nama file |

Contoh penting:
- Direktori tanpa `x`: Anda tidak bisa `cd` ke sana, meskipun `r` ada.
- Direktori tanpa `r` tapi ada `x`: Anda bisa akses file jika tahu namanya, tapi tidak bisa `ls` daftar isi.

---

## 3. MODE SIMBOLIK DAN NUMERIC (755, 644)

### 3.1 Permission matrix

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

### 3.2 Contoh interpretasi

- `644` pada file: owner bisa read+write, yang lain read saja
- `755` pada direktori: owner full, group/others bisa masuk dan membaca daftar

Cek permission numeric:

```bash
stat -c '%a %n' /etc/passwd
```

---

## 4. `CHMOD`: MENGUBAH PERMISSION

### 4.1 Numeric mode

```bash
# File umum: 644
chmod 644 file.txt

# Script/executable: 755
chmod 755 script.sh
```

### 4.2 Symbolic mode

Format: `chmod [u|g|o|a][+|-|=][r|w|x] <target>`

```bash
# Tambah execute untuk owner
chmod u+x script.sh

# Hapus write untuk others
chmod o-w file.txt

# Set permission tepat
chmod u=rw,g=r,o= file.txt
```

### 4.3 Rekursif (hati-hati)

```bash
# Ubah permission semua file/folder di bawahnya
chmod -R 755 mydir
```

Catatan: `chmod -R` sering berbahaya jika Anda tidak yakin struktur isinya.

---

## 5. `CHOWN` DAN `CHGRP`: MENGUBAH OWNER/GROUP

### 5.1 `chown`

```bash
# Ubah owner
sudo chown atha file.txt

# Ubah owner dan group
sudo chown atha:atha file.txt

# Rekursif
sudo chown -R atha:atha mydir/
```

### 5.2 `chgrp`

```bash
# Ubah group saja
sudo chgrp developers file.txt
```

---

## 6. `SUDO`: SUPERUSER DO

### 6.1 Kapan pakai sudo?

Gunakan `sudo` saat memang butuh akses admin (misalnya mengubah file di `/etc`, install package, start/stop service).

Contoh aman:

```bash
# Lihat file yang butuh root
sudo ls -la /root

# Edit config (pilih editor Anda)
sudoedit /etc/hosts
```

### 6.2 Cek apa yang boleh Anda lakukan

```bash
sudo -l
```

### 6.3 Jangan kebiasaan kerja sebagai root

- Jangan login sebagai root untuk pekerjaan harian
- Hindari `sudo su` / `sudo -i` kecuali benar-benar perlu

---

## 7. LEAST PRIVILEGE (PRINSIP KEAMANAN DASAR)

**Least privilege** berarti: berikan akses **paling minimal** yang diperlukan untuk menyelesaikan tugas.

Contoh penerapan:
- Aplikasi web cukup baca file config tertentu, tidak perlu akses write ke seluruh `/etc`
- User biasa tidak perlu akses write ke `/var/log`
- Script backup butuh akses baca ke data target, tapi akses write cukup ke direktori backup

Checklist praktis:
1. Mulai dari permission ketat
2. Longgarkan hanya yang perlu
3. Dokumentasikan alasan perubahan

---

## TUTORIAL PRAKTIS

### Tutorial 1: Membaca Permission dan Ownership

```bash
mkdir -p perm-lab
cd perm-lab

echo "hello" > a.txt
ls -l a.txt
stat a.txt
```

### Tutorial 2: Eksperimen Permission File

```bash
# Buat script
cat > runme.sh << 'EOF'
#!/bin/bash
echo "I can run!"
EOF

ls -l runme.sh

# Coba jalankan (biasanya gagal karena belum executable)
./runme.sh

# Tambah execute
chmod u+x runme.sh
./runme.sh
```

### Tutorial 3: Eksperimen Permission Direktori

```bash
mkdir secret
chmod 700 secret

# Lihat apakah bisa list/masuk
ls -ld secret
cd secret
cd ..

# Longgarkan untuk group/others
chmod 755 secret
ls -ld secret
```

### Tutorial 4: Ownership dengan chown (gunakan file aman)

```bash
# Buat file milik Anda
echo "test" > owned.txt
ls -l owned.txt

# Contoh command (butuh sudo):
# sudo chown root:root owned.txt
# ls -l owned.txt
# sudo chown $USER:$USER owned.txt
```

---

## PRAKTIKUM

### Praktikum 1: Permission Audit Mini

**Durasi:** 45 menit

**Tugas:**
1. Buat folder lab `perm-audit/` berisi:
   - 2 file biasa
   - 1 script
   - 1 subdirektori
2. Set permission yang masuk akal:
   - file biasa `644`
   - script `755`
   - direktori `755`
3. Buat laporan output `ls -la` dan `stat -c '%a %n'` untuk semuanya

```bash
mkdir -p perm-audit/sub
cd perm-audit

echo a > f1.txt
echo b > f2.txt
cat > s1.sh << 'EOF'
#!/bin/bash
echo "ok"
EOF

chmod 644 f1.txt f2.txt
chmod 755 s1.sh
chmod 755 sub

ls -la
find . -maxdepth 2 \( -type f -o -type d \) | while read -r p; do stat -c '%a %n' "$p"; done
```

### Praktikum 2: Perbaiki Akses yang Salah

**Durasi:** 60 menit

**Skenario:** Anda punya direktori `project/` tapi tidak bisa menjalankan script di dalamnya.

**Tugas:**
1. Buat `project/run.sh`
2. Sengaja bikin permission salah (tanpa execute)
3. Diagnosa dengan `ls -l`
4. Perbaiki dengan `chmod`

```bash
mkdir -p project
cat > project/run.sh << 'EOF'
#!/bin/bash
echo "running"
EOF

chmod 644 project/run.sh
./project/run.sh

ls -l project/run.sh
chmod u+x project/run.sh
./project/run.sh
```

---

## STUDI KASUS

### Studi Kasus 1: “Permission denied” saat menjalankan script

**Gejala:** `./script.sh: Permission denied`

**Checklist:**
1. Apakah file punya bit execute? (`ls -l`)
2. Apakah filesystem di-mount `noexec`? (cek `mount`)
3. Apakah interpreter benar ada? (shebang `#!/bin/bash`)

```bash
ls -l script.sh
mount | grep -E ' on / | on /home '
head -1 script.sh
```

### Studi Kasus 2: Service tidak bisa baca config

**Gejala:** service gagal start karena tidak bisa baca file config.

**Checklist:**
1. Owner/group file config benar?
2. Permission file config terlalu ketat?
3. Permission direktori parent mengizinkan traverse (`x`) untuk user service?

```bash
ls -l /etc/<app>/<app>.conf
ls -ld /etc/<app>
```

---

## TANTANGAN

### Tantangan 1: Permission Translator

**Level: Pemula**

Jawab tanpa Google:
1. `640` itu symbolic-nya apa?
2. `rwxr-x---` itu numeric-nya berapa?
3. Permission yang cocok untuk file private yang hanya boleh dibaca owner?

### Tantangan 2: Folder Sharing Aman

**Level: Menengah**

Buat folder `shared/` yang:
- owner bisa read/write
- group bisa read
- others tidak bisa akses

Tuliskan command yang Anda pakai dan tampilkan output `ls -ld shared`.

---

## SOAL LATIHAN

### Pilihan Ganda

**1.** Pada direktori, bit `x` berarti...
- [ ] A. Boleh menghapus direktori
- [x] B. Boleh traverse/masuk direktori
- [ ] C. Boleh melihat daftar isi direktori
- [ ] D. Direktori menjadi executable file

**2.** Permission `644` berarti...
- [x] A. Owner rw, group r, others r
- [ ] B. Owner rwx, group r, others r
- [ ] C. Owner r, group r, others r
- [ ] D. Owner rw, group rw, others r

**3.** Perintah untuk mengubah owner file adalah...
- [ ] A. chmod
- [x] B. chown
- [ ] C. chgrp
- [ ] D. sudo

**4.** Prinsip least privilege berarti...
- [ ] A. Semua user diberi akses admin
- [x] B. Akses minimal yang diperlukan untuk tugas
- [ ] C. Semua file harus 777
- [ ] D. Selalu kerja sebagai root

### Essay

**1.** Jelaskan perbedaan makna `rwx` pada file dan pada direktori. (20 poin)

**2.** Jelaskan langkah diagnosis untuk error “Permission denied” saat menjalankan script. (20 poin)

**3.** Berikan contoh penerapan least privilege di server Linux. (20 poin)

---

## RANGKUMAN

- Linux menggunakan model permission `owner/group/others` dengan bit `rwx`.
- `rwx` pada direktori berbeda maknanya dengan file; bit `x` (traverse) sering jadi penyebab utama “kok tidak bisa akses”.
- Numeric mode (mis. 755/644) adalah penjumlahan 4/2/1.
- `chmod` mengubah permission; `chown` mengubah owner (dan bisa sekaligus group).
- `sudo` dipakai saat perlu admin, tapi jangan dijadikan kebiasaan kerja harian.
- Least privilege adalah fondasi keamanan: beri akses minimal yang diperlukan.

---

## REFERENSI

- `man chmod`
- `man chown`
- `man sudo`
- `man stat`
- `man ls`

---

## GLOSARIUM

- **Owner**: user pemilik file/direktori.
- **Group**: grup pemilik file/direktori.
- **Others**: semua user selain owner dan group.
- **Permission**: aturan akses untuk read/write/execute.
- **Least privilege**: prinsip pemberian hak akses minimal.
