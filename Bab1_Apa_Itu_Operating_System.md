# BAB 1: APA ITU OPERATING SYSTEM

---

```
╔══════════════════════════════════════════════════════════════════════════╗
║                                                                          ║
║    ██████╗ ██╗  ██╗██╗██╗   ██╗ █████╗ ██╗   ██╗██╗                      ║
║    ██╔══██╗██║  ██║██║╚██╗ ██╔╝██╔══██╗██║   ██║██║                      ║
║    ██║  ██║███████║██║ ╚████╔╝ ███████║██║   ██║██║                      ║
║    ██║  ██║██╔══██║██║  ╚██╔╝  ██╔══██║██║   ██║██║                      ║
║    ██████╔╝██║  ██║██║   ██║   ██║  ██║╚██████╔╝███████╗                 ║
║    ╚═════╝ ╚═╝  ╚═╝╚═╝   ╚═╝   ╚═╝  ╚═╝ ╚═════╝ ╚══════╝                 ║
║                                                                          ║
║    📚 Learning Notes by Muhammad Dhiyaul Atha                            ║
║    📖 Bab 1: Apa Itu Operating System                                    ║
║                                                                          ║
╚══════════════════════════════════════════════════════════════════════════╝
```

---

## 📑 DAFTAR ISI

1. [Pendahuluan](#pendahuluan)
2. [Definisi Operating System](#1-definisi-operating-system)
3. [Kernel vs User Space](#2-kernel-vs-user-space)
4. [Cara Kerja Program Dijalankan](#3-cara-kerja-program-dijalankan)
5. [System Call](#4-system-call)
6. [Tutorial Praktis](#tutorial-praktis)
7. [Praktikum](#praktikum)
8. [Studi Kasus](#studi-kasus)
9. [Tantangan](#tantangan)
10. [Soal Latihan](#soal-latihan)
11. [Rangkuman](#rangkuman)
12. [Referensi](#referensi)
13. [Glosarium](#glosarium)

---

## PENDAHULUAN

Selamat datang di Bab 1 dari perjalanan belajar Operating System! Bab ini adalah fondasi yang akan membentuk pemahaman Anda tentang bagaimana komputer bekerja di level sistem. Sebelum Anda bisa menjadi sysadmin, DevOps engineer, atau backend developer yang handal, pemahaman mendalam tentang Operating System adalah **WAJIB**.

### 🎯 Tujuan Pembelajaran

Setelah menyelesaikan bab ini, Anda akan mampu:
1. Menjelaskan definisi dan fungsi utama Operating System
2. Membedakan antara Kernel Space dan User Space
3. Memahami siklus hidup program dari disk hingga eksekusi
4. Menjelaskan konsep System Call dan implementasinya
5. Melakukan eksperimen praktis untuk membuktikan konsep-konsep teori

### 📋 Prasyarat

- Komputer dengan OS Linux (Ubuntu/Debian direkomendasikan)
- Pemahaman dasar command line
- Rasa ingin tahu yang tinggi!

---

## 1. DEFINISI OPERATING SYSTEM

### 1.1 Pengertian Dasar

**Operating System (OS)** adalah perangkat lunak sistem yang bertindak sebagai **perantara** antara pengguna komputer dan perangkat keras komputer. OS mengelola semua sumber daya komputer dan menyediakan layanan umum untuk program aplikasi.

```
┌─────────────────────────────────────────────────────────────┐
│                         USER                                │
│              (Anda yang menggunakan komputer)               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      APLIKASI                               │
│        (Browser, Text Editor, Game, Terminal, dll)          │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   OPERATING SYSTEM                          │
│    ┌─────────────────────────────────────────────────────┐  │
│    │           Shell & System Libraries                  │  │
│    └─────────────────────────────────────────────────────┘  │
│    ┌─────────────────────────────────────────────────────┐  │
│    │                    KERNEL                           │  │
│    │  (Process Management, Memory, I/O, File System)     │  │
│    └─────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      HARDWARE                               │
│         (CPU, RAM, Disk, Network Card, GPU, dll)            │
└─────────────────────────────────────────────────────────────┘
```

### 1.2 Analogi Sederhana

Bayangkan **Operating System sebagai manajer hotel**:

| Komponen Hotel | Komponen Komputer |
|----------------|-------------------|
| Manajer Hotel | Operating System |
| Kamar Hotel | Memory (RAM) |
| Tamu | Program/Proses |
| Kunci Kamar | Permissions |
| Resepsionis | Shell/Interface |
| Fasilitas (lift, AC, air) | Hardware Resources |
| Buku Tamu | Log Files |
| Peraturan Hotel | Kernel Policies |

Manajer hotel (OS) memastikan:
- Setiap tamu (proses) mendapat kamar (memory) yang tepat
- Tidak ada tamu yang masuk kamar orang lain (memory protection)
- Fasilitas digunakan secara adil (resource scheduling)
- Keamanan terjaga (security)

### 1.3 Fungsi Utama Operating System

#### 1.3.1 Process Management (Manajemen Proses)

OS bertanggung jawab untuk:
- **Membuat dan menghapus proses** (user & system)
- **Suspend dan resume proses**
- **Menyediakan mekanisme sinkronisasi proses**
- **Menyediakan mekanisme komunikasi antar proses (IPC)**
- **Menyediakan mekanisme penanganan deadlock**

```
       ┌──────────┐
       │   NEW    │ ──────────────────────────────────────┐
       └────┬─────┘                                       │
            │ admit                                       │
            ▼                                             │
       ┌──────────┐         ┌───────────────┐             │
       │  READY   │◄────────│   WAITING     │             │
       └────┬─────┘ I/O or  └───────────────┘             │
            │ dispatch    event wait ▲                    │
            ▼              │         │                    │
       ┌──────────┐ ───────┘         │                    │
       │ RUNNING  │──────────────────┘                    │
       └────┬─────┘   interrupt                           │
            │ exit                                        │
            ▼                                             │
       ┌──────────┐                                       │
       │TERMINATED│◄──────────────────────────────────────┘
       └──────────┘
```

#### 1.3.2 Memory Management (Manajemen Memori)

- **Melacak bagian memori yang sedang digunakan** dan oleh siapa
- **Memutuskan proses mana yang akan di-load** ke memori
- **Mengalokasikan dan mendealokasi** ruang memori sesuai kebutuhan
- **Virtual memory management**

```
┌─────────────────────────────────────────────────────────────┐
│                    VIRTUAL MEMORY                           │
├─────────────────────────────────────────────────────────────┤
│  Process A   │  Process B   │  Process C   │   Kernel      │
│  0x0000      │  0x0000      │  0x0000      │               │
│    to        │    to        │    to        │               │
│  0xFFFF      │  0xFFFF      │  0xFFFF      │               │
└──────┬───────┴──────┬───────┴──────┬───────┴───────────────┘
       │              │              │
       │    MMU (Memory Management Unit)
       │              │              │
       ▼              ▼              ▼
┌─────────────────────────────────────────────────────────────┐
│                   PHYSICAL MEMORY (RAM)                     │
│  ┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐   │
│  │ A1 │ B3 │ C1 │ K  │ A2 │ B1 │ C2 │ A3 │ B2 │ K  │ C3 │   │
│  └────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘   │
└─────────────────────────────────────────────────────────────┘
```

#### 1.3.3 File System Management

- **Membuat dan menghapus file/direktori**
- **Menyediakan primitif untuk manipulasi file**
- **Mapping file ke secondary storage**
- **Backup file ke media penyimpanan stabil**

```
                          /
                          │
         ┌────────────────┼────────────────┐
         │                │                │
        bin              etc             home
         │                │                │
    ┌────┼────┐     ┌─────┼─────┐    ┌─────┼─────┐
   bash  ls  cat  passwd hosts   user1      user2
                  shadow              │
                                ┌─────┼─────┐
                          Documents  Pictures  .bashrc
```

#### 1.3.4 I/O System Management

- **Menyembunyikan kekhasan hardware** dari user
- **Memory management untuk I/O** (buffering, caching, spooling)
- **Driver interface**

#### 1.3.5 Security & Protection

- **Mengontrol akses** ke sumber daya sistem
- **Autentikasi pengguna**
- **Menjaga integritas data**

### 1.4 Jenis-Jenis Operating System

#### 1.4.1 Berdasarkan Jumlah User

| Tipe | Deskripsi | Contoh |
|------|-----------|--------|
| **Single-user** | Satu user pada satu waktu | MS-DOS, Windows 95 |
| **Multi-user** | Banyak user bersamaan | Linux, Unix, Windows Server |

#### 1.4.2 Berdasarkan Jumlah Task

| Tipe | Deskripsi | Contoh |
|------|-----------|--------|
| **Single-tasking** | Satu program pada satu waktu | MS-DOS |
| **Multitasking** | Banyak program bersamaan | Linux, Windows, macOS |

#### 1.4.3 Berdasarkan Tujuan

| Tipe | Karakteristik | Contoh |
|------|---------------|--------|
| **General Purpose** | Fleksibel untuk berbagai tugas | Ubuntu, Windows 10 |
| **Real-time (RTOS)** | Response time terjamin | FreeRTOS, VxWorks |
| **Embedded** | Untuk perangkat khusus | Android, Tizen |
| **Network OS** | Fokus pada layanan jaringan | Cisco IOS |
| **Distributed** | Mengelola multiple machines | Amoeba |

### 1.5 Sejarah Singkat Operating System

```
Timeline Operating System:

1950s ─── Batch Systems
          │ Jobs diproses satu per satu tanpa interaksi
          │
1960s ─── Time-Sharing Systems
          │ MULTICS, Unix lahir
          │
1970s ─── Unix berkembang (AT&T Bell Labs)
          │ C language untuk menulis OS
          │
1980s ─── Personal Computer Era
          │ MS-DOS, Macintosh
          │
1991  ─── Linux lahir (Linus Torvalds)
          │
2000s ─── Mobile OS Revolution
          │ Android, iOS
          │
2010s ─── Container & Cloud Era
          │ Docker, Kubernetes
          │
2020s ─── Edge Computing, IoT OS
```

### 1.6 Komponen Utama OS Modern

```
┌─────────────────────────────────────────────────────────────────┐
│                    MODERN OPERATING SYSTEM                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌───────────┐  │
│  │   Process   │ │   Memory    │ │    File     │ │    I/O    │  │
│  │  Scheduler  │ │   Manager   │ │   System    │ │  Manager  │  │
│  └─────────────┘ └─────────────┘ └─────────────┘ └───────────┘  │
│                                                                 │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌───────────┐  │
│  │  Security   │ │  Network    │ │   Device    │ │   Power   │  │
│  │   Module    │ │   Stack     │ │   Drivers   │ │Management │  │
│  └─────────────┘ └─────────────┘ └─────────────┘ └───────────┘  │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │                        KERNEL                               ││
│  └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. KERNEL VS USER SPACE

### 2.1 Pengertian Kernel

**Kernel** adalah inti dari Operating System. Kernel adalah bagian pertama yang di-load saat boot dan tetap berada di memori selama komputer berjalan. Kernel memiliki akses penuh ke seluruh hardware dan menjalankannya dalam mode istimewa (**privileged mode** atau **kernel mode**).

### 2.2 Ring Protection Model

CPU modern mengimplementasikan **ring protection** untuk memisahkan level privilege:

```
                    ┌───────────────────┐
                    │                   │
                    │      Ring 3       │ ◄─── User Applications
                    │   (User Mode)     │      Least Privileged
                    │                   │
               ┌────┴───────────────────┴────┐
               │                             │
               │          Ring 2             │ ◄─── Device Drivers (some OS)
               │                             │
          ┌────┴─────────────────────────────┴────┐
          │                                       │
          │              Ring 1                   │ ◄─── Device Drivers
          │                                       │
     ┌────┴───────────────────────────────────────┴────┐
     │                                                 │
     │                    Ring 0                       │ ◄─── Kernel
     │               (Kernel Mode)                     │      Most Privileged
     │                                                 │
     └─────────────────────────────────────────────────┘
```

Pada praktiknya, kebanyakan OS modern hanya menggunakan **2 ring**:
- **Ring 0** = Kernel Mode
- **Ring 3** = User Mode

### 2.3 Perbedaan Kernel Space vs User Space

| Aspek | Kernel Space | User Space |
|-------|--------------|------------|
| **Privilege Level** | Ring 0 (tertinggi) | Ring 3 (terendah) |
| **Akses Hardware** | Langsung | Melalui System Call |
| **Akses Memory** | Seluruh RAM | Terbatas pada area yang dialokasikan |
| **Crash Impact** | System crash (kernel panic) | Hanya aplikasi crash |
| **Contoh** | Kernel, Device Drivers | Browser, Editor, Games |
| **Alamat Memory** | Biasanya di address tinggi | Biasanya di address rendah |

### 2.4 Visualisasi Memory Layout

```
┌─────────────────────────────────────────────────────────────┐
│                    VIRTUAL ADDRESS SPACE                     │
├─────────────────────────────────────────────────────────────┤
│  0xFFFFFFFF ┌──────────────────────────────────────────┐    │
│             │                                          │    │
│             │            KERNEL SPACE                  │    │
│             │       (Hanya bisa diakses saat           │    │
│             │        CPU dalam kernel mode)            │    │
│             │                                          │    │
│  0xC0000000 ├──────────────────────────────────────────┤    │
│             │            K E R N E L                   │    │
│             │            B O U N D A R Y               │    │
│  0xBFFFFFFF ├──────────────────────────────────────────┤    │
│             │                                          │    │
│             │                                          │    │
│             │            USER SPACE                    │    │
│             │                                          │    │
│             │    ┌────────────────────────────────┐    │    │
│             │    │           Stack                │    │    │
│             │    │            ↓                   │    │    │
│             │    ├────────────────────────────────┤    │    │
│             │    │         (unused)               │    │    │
│             │    ├────────────────────────────────┤    │    │
│             │    │            ↑                   │    │    │
│             │    │           Heap                 │    │    │
│             │    ├────────────────────────────────┤    │    │
│             │    │           BSS                  │    │    │
│             │    ├────────────────────────────────┤    │    │
│             │    │           Data                 │    │    │
│             │    ├────────────────────────────────┤    │    │
│             │    │           Text (Code)          │    │    │
│             │    └────────────────────────────────┘    │    │
│             │                                          │    │
│  0x00000000 └──────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

### 2.5 Mengapa Pemisahan Ini Penting?

#### 2.5.1 Keamanan (Security)

```
❌ TANPA PEMISAHAN:
   ┌─────────────────────────────────────┐
   │  Program Jahat                      │
   │  bisa langsung akses:               │
   │  - Membaca password dari memory     │
   │  - Memodifikasi kernel              │
   │  - Mengambil alih hardware          │
   └─────────────────────────────────────┘

✓ DENGAN PEMISAHAN:
   ┌─────────────────────────────────────┐
   │  Program Jahat                      │
   │  ───────────────────────────────    │
   │      │ Mencoba akses langsung       │
   │      ▼                              │
   │  ╔═══════════════════════════════╗  │
   │  ║   PROTECTION BOUNDARY         ║  │
   │  ║   "ACCESS DENIED"             ║  │
   │  ║   → Segmentation Fault        ║  │
   │  ╚═══════════════════════════════╝  │
   └─────────────────────────────────────┘
```

#### 2.5.2 Stabilitas (Stability)

```
Skenario: Bug dalam program user

TANPA PEMISAHAN:
┌─────────────────────────────────────────┐
│  User Program Bug ──► System Crash!     │
│  💥 KERNEL PANIC 💥                     │
└─────────────────────────────────────────┘

DENGAN PEMISAHAN:
┌─────────────────────────────────────────┐
│  User Program Bug ──► Program terminated│
│  ✓ Kernel tetap berjalan                │
│  ✓ Program lain tidak terpengaruh       │
└─────────────────────────────────────────┘
```

#### 2.5.3 Isolasi Proses

Setiap proses memiliki virtual address space sendiri:

```
Process A                    Process B
┌────────────────┐          ┌────────────────┐
│ Address 0x1000 │          │ Address 0x1000 │
│    "Hello"     │          │    "World"     │
└────────┬───────┘          └────────┬───────┘
         │                           │
         ▼                           ▼
┌─────────────────────────────────────────────┐
│            MMU Translation                   │
└─────────────────────────────────────────────┘
         │                           │
         ▼                           ▼
┌────────────────┐          ┌────────────────┐
│ Physical: 0xABC│          │ Physical: 0XYZ│
│    "Hello"     │          │    "World"     │
└────────────────┘          └────────────────┘
```

### 2.6 Tipe-Tipe Kernel Architecture

#### 2.6.1 Monolithic Kernel

Semua layanan OS berjalan di kernel space.

```
┌─────────────────────────────────────────────────────────────┐
│                        USER SPACE                           │
│    ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│    │  App 1   │ │  App 2   │ │  App 3   │ │  App 4   │      │
│    └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
├─────────────────────────────────────────────────────────────┤
│                       KERNEL SPACE                          │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                   MONOLITHIC KERNEL                   │  │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐      │  │
│  │  │ Process │ │ Memory  │ │  File   │ │  I/O &  │      │  │
│  │  │ Manager │ │ Manager │ │ System  │ │ Devices │      │  │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘      │  │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐                  │  │
│  │  │ Network │ │ Security│ │  IPC    │                  │  │
│  │  │  Stack  │ │         │ │         │                  │  │
│  │  └─────────┘ └─────────┘ └─────────┘                  │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘

Contoh: Linux, Unix, MS-DOS

✓ Kelebihan:
  - Performa tinggi (semua di satu memory space)
  - Komunikasi antar komponen cepat

✗ Kekurangan:
  - Bug di satu komponen bisa crash keseluruhan
  - Sulit di-maintain (codebase besar)
```

#### 2.6.2 Microkernel

Kernel minimal, layanan lain di user space.

```
┌─────────────────────────────────────────────────────────────┐
│                        USER SPACE                           │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐        │
│  │  App 1   │ │  App 2   │ │  File    │ │ Network  │        │
│  │          │ │          │ │  Server  │ │  Server  │        │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                     │
│  │ Device   │ │ Memory   │ │ Process  │                     │
│  │ Driver   │ │ Server   │ │ Server   │                     │
│  └──────────┘ └──────────┘ └──────────┘                     │
├─────────────────────────────────────────────────────────────┤
│                       KERNEL SPACE                          │
│         ┌─────────────────────────────────────────┐         │
│         │            MICROKERNEL                  │         │
│         │  ┌───────┐ ┌───────┐ ┌───────┐          │         │
│         │  │ Basic │ │  IPC  │ │ Basic │          │         │
│         │  │ Sched │ │       │ │Memory │          │         │
│         │  └───────┘ └───────┘ └───────┘          │         │
│         └─────────────────────────────────────────┘         │
└─────────────────────────────────────────────────────────────┘

Contoh: Minix, QNX, seL4

✓ Kelebihan:
  - Lebih stabil (crash di server tidak crash kernel)
  - Lebih mudah di-maintain
  - Lebih secure

✗ Kekurangan:
  - Overhead dari IPC (message passing)
  - Performa lebih rendah
```

#### 2.6.3 Hybrid Kernel

Kombinasi kedua pendekatan.

```
┌─────────────────────────────────────────────────────────────┐
│                        USER SPACE                           │
│    ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│    │  App 1   │ │  App 2   │ │ Some     │ │ User     │      │
│    │          │ │          │ │ Drivers  │ │ Services │      │
│    └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
├─────────────────────────────────────────────────────────────┤
│                       KERNEL SPACE                          │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                   HYBRID KERNEL                       │  │
│  │  ┌─────────────────────────────────────────────────┐  │  │
│  │  │ Microkernel Core (Scheduling, IPC, Memory)      │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐      │  │
│  │  │  File   │ │ Network │ │ Some    │ │ Device  │      │  │
│  │  │ System  │ │  Stack  │ │ Drivers │ │ Manager │      │  │
│  │  └─────────┘ └─────────┘ └─────────┘ └─────────┘      │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘

Contoh: Windows NT, macOS (XNU)
```

### 2.7 Linux: Modular Monolithic Kernel

Linux menggunakan pendekatan **modular monolithic**:

```
┌─────────────────────────────────────────────────────────────┐
│                     LINUX KERNEL                            │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────┐    │
│  │              CORE KERNEL (vmlinuz)                  │    │
│  │  Always loaded in memory                            │    │
│  └─────────────────────────────────────────────────────┘    │
│                            │                                │
│                            ▼                                │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐        │
│  │ Module 1 │ │ Module 2 │ │ Module 3 │ │ Module N │        │
│  │ (ext4.ko)│ │(nvidia.ko)││(wifi.ko) │ │ (.ko)    │        │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘        │
│  Loadable Kernel Modules - dapat di-load/unload runtime     │
└─────────────────────────────────────────────────────────────┘
```

**Keuntungan pendekatan ini:**
- Module bisa di-load saat dibutuhkan (hemat memory)
- Module bisa di-update tanpa reboot
- Tetap mendapat performa monolithic kernel

---

## 3. CARA KERJA PROGRAM DIJALANKAN

### 3.1 Dari Source Code ke Eksekusi

```
┌────────────────────────────────────────────────────────────────────┐
│                    THE JOURNEY OF A PROGRAM                         │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. SOURCE CODE                                                     │
│     hello.c                                                         │
│     ┌──────────────────────────────┐                                │
│     │ #include <stdio.h>           │                                │
│     │ int main() {                 │                                │
│     │   printf("Hello\n");         │                                │
│     │   return 0;                  │                                │
│     │ }                            │                                │
│     └──────────────────────────────┘                                │
│                    │                                                │
│                    ▼ Preprocessor (cpp)                             │
│  2. PREPROCESSED                                                    │
│     hello.i (expanded macros, includes)                             │
│                    │                                                │
│                    ▼ Compiler (cc1)                                 │
│  3. ASSEMBLY                                                        │
│     hello.s                                                         │
│     ┌──────────────────────────────┐                                │
│     │ .text                        │                                │
│     │ main:                        │                                │
│     │   pushq %rbp                 │                                │
│     │   ...                        │                                │
│     └──────────────────────────────┘                                │
│                    │                                                │
│                    ▼ Assembler (as)                                 │
│  4. OBJECT FILE                                                     │
│     hello.o (machine code, belum lengkap)                           │
│                    │                                                │
│                    ▼ Linker (ld)                                    │
│  5. EXECUTABLE                                                      │
│     hello (ELF format, lengkap dengan libraries)                    │
│                    │                                                │
│                    ▼ Loader (OS)                                    │
│  6. RUNNING PROCESS                                                 │
│     PID: 12345                                                      │
│                                                                     │
└────────────────────────────────────────────────────────────────────┘
```

### 3.2 Format File Executable: ELF

Di Linux, format executable adalah **ELF (Executable and Linkable Format)**:

```
┌─────────────────────────────────────────────────────────────┐
│                    ELF FILE STRUCTURE                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                 ELF HEADER                          │    │
│  │  - Magic number (0x7F 'E' 'L' 'F')                  │    │
│  │  - Architecture (32/64 bit)                         │    │
│  │  - Entry point address                              │    │
│  │  - Program header table offset                      │    │
│  │  - Section header table offset                      │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              PROGRAM HEADER TABLE                   │    │
│  │  (untuk eksekusi - bagaimana load ke memory)        │    │
│  │  - PT_LOAD: segments to load                        │    │
│  │  - PT_INTERP: dynamic linker path                   │    │
│  │  - PT_DYNAMIC: dynamic linking info                 │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                  SECTIONS                           │    │
│  │  .text    - machine code (executable)               │    │
│  │  .data    - initialized global variables            │    │
│  │  .bss     - uninitialized global variables          │    │
│  │  .rodata  - read-only data (string literals)        │    │
│  │  .symtab  - symbol table                            │    │
│  │  .strtab  - string table                            │    │
│  │  .rel*    - relocation tables                       │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              SECTION HEADER TABLE                   │    │
│  │  (metadata tentang sections)                        │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 3.3 Proses Loading Program

Ketika Anda menjalankan `./hello`:

```
┌─────────────────────────────────────────────────────────────────────┐
│                      PROGRAM LOADING PROCESS                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  STEP 1: Shell menerima command                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  $ ./hello                                                  │     │
│  │  Shell parse command dan memanggil fork() + execve()        │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                              │                                       │
│                              ▼                                       │
│  STEP 2: Kernel execve() system call                                 │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  1. Buka file ./hello                                       │     │
│  │  2. Baca ELF header                                         │     │
│  │  3. Verifikasi magic number, architecture                   │     │
│  │  4. Alokasikan memory structures                            │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                              │                                       │
│                              ▼                                       │
│  STEP 3: Memory Mapping                                              │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  1. Parse program headers                                   │     │
│  │  2. Map .text segment (Read + Execute)                      │     │
│  │  3. Map .data segment (Read + Write)                        │     │
│  │  4. Allocate .bss (zeroed)                                  │     │
│  │  5. Setup stack                                             │     │
│  │  6. Setup heap                                              │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                              │                                       │
│                              ▼                                       │
│  STEP 4: Dynamic Linking (jika ada)                                  │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  1. Load dynamic linker (/lib64/ld-linux-x86-64.so.2)       │     │
│  │  2. Load shared libraries (libc.so.6, etc)                  │     │
│  │  3. Resolve symbols                                         │     │
│  │  4. Apply relocations                                       │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                              │                                       │
│                              ▼                                       │
│  STEP 5: Start Execution                                             │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  1. Set instruction pointer ke entry point                  │     │
│  │  2. Setup argc, argv, envp di stack                         │     │
│  │  3. Jump ke _start (dari CRT)                               │     │
│  │  4. _start memanggil main()                                 │     │
│  │  5. Program berjalan!                                       │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.4 Process Memory Layout saat Running

```
┌─────────────────────────────────────────────────────────────┐
│              PROCESS VIRTUAL ADDRESS SPACE                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  High Address (0xFFFFFFFF...)                               │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                   KERNEL SPACE                      │    │
│  │           (mapped tapi tidak accessible)            │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                     STACK                           │    │
│  │         (grows downward ↓)                          │    │
│  │  - Local variables                                  │    │
│  │  - Function parameters                              │    │
│  │  - Return addresses                                 │    │
│  │  - argc, argv, envp (at bottom)                     │    │
│  └─────────────────────────────────────────────────────┘    │
│                        ↓                                    │
│  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐     │
│                   UNUSED SPACE                              │
│  └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘     │
│                        ↑                                    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                     HEAP                            │    │
│  │         (grows upward ↑)                            │    │
│  │  - malloc() allocations                             │    │
│  │  - Dynamic data                                     │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │          MEMORY MAPPED REGION                       │    │
│  │  - Shared libraries (.so files)                     │    │
│  │  - Memory-mapped files                              │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                     BSS                             │    │
│  │  - Uninitialized globals (zeroed)                   │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                     DATA                            │    │
│  │  - Initialized global variables                     │    │
│  │  - Static variables                                 │    │
│  └─────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                     TEXT                            │    │
│  │  - Machine code (read-only + execute)               │    │
│  │  - String literals (rodata)                         │    │
│  └─────────────────────────────────────────────────────┘    │
│  Low Address (0x00000000...)                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 3.5 Fork dan Exec

Dua system call penting dalam menjalankan program:

#### fork() - Membuat proses baru

```
SEBELUM fork():
┌──────────────────────────┐
│     Parent Process       │
│     PID: 100             │
│     Code + Data + Stack  │
└──────────────────────────┘

SETELAH fork():
┌──────────────────────────┐     ┌──────────────────────────┐
│     Parent Process       │     │     Child Process        │
│     PID: 100             │     │     PID: 101             │
│     fork() returns 101   │     │     fork() returns 0     │
│     Code + Data + Stack  │     │     Code + Data + Stack  │
└──────────────────────────┘     └──────────────────────────┘
         (Original)                      (Copy)
```

#### exec() - Mengganti program

```
SEBELUM exec("ls"):
┌──────────────────────────┐
│     Process              │
│     Running: bash        │
│     Code: bash code      │
│     Data: bash data      │
└──────────────────────────┘

SETELAH exec("ls"):
┌──────────────────────────┐
│     Process (same PID)   │
│     Running: ls          │
│     Code: ls code        │ ← Diganti!
│     Data: ls data        │ ← Diganti!
└──────────────────────────┘
```

#### Kombinasi fork() + exec()

```
Shell (PID 100)
     │
     │ fork()
     ▼
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Parent (PID 100)              Child (PID 101)          │
│  wait() untuk child            exec("/bin/ls")          │
│         │                             │                 │
│         │                             ▼                 │
│         │                      ┌─────────────┐          │
│         │                      │   ls runs   │          │
│         │                      │   prints    │          │
│         │                      │   output    │          │
│         │                      └─────────────┘          │
│         │                             │                 │
│         │◄────────────────────────────┘                 │
│         │           child exits                         │
│         ▼                                               │
│  continue execution                                     │
│  (show prompt again)                                    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 4. SYSTEM CALL

### 4.1 Pengertian System Call

**System Call** adalah mekanisme yang digunakan program user space untuk meminta layanan dari kernel. Ini adalah **satu-satunya cara legal** bagi program untuk berinteraksi dengan hardware dan resources sistem.

```
┌─────────────────────────────────────────────────────────────────────┐
│                         SYSTEM CALL                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   USER SPACE                                                         │
│   ┌──────────────────────────────────────────────────────────────┐   │
│   │  Application Program                                         │   │
│   │                                                              │   │
│   │  printf("Hello");  ─────────────────────────────────┐        │   │
│   │                                                      │        │   │
│   └──────────────────────────────────────────────────────┼────────┘   │
│                                                          │            │
│   ┌──────────────────────────────────────────────────────┼────────┐   │
│   │  C Library (glibc)                                   │        │   │
│   │                                                      ▼        │   │
│   │  printf() ──► write() wrapper ──► syscall(1, ...)            │   │
│   │                                                      │        │   │
│   └──────────────────────────────────────────────────────┼────────┘   │
│ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─   │
│   KERNEL SPACE                                           │            │
│   ┌──────────────────────────────────────────────────────┼────────┐   │
│   │  System Call Interface                               │        │   │
│   │                                                      ▼        │   │
│   │  syscall_table[1] ──► sys_write()                            │   │
│   │                              │                               │   │
│   └──────────────────────────────┼───────────────────────────────┘   │
│                                  │                                    │
│   ┌──────────────────────────────┼───────────────────────────────┐   │
│   │  Kernel Subsystems           │                               │   │
│   │                              ▼                               │   │
│   │  VFS ──► File System ──► Device Driver ──► Hardware          │   │
│   │                                                              │   │
│   └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 Mengapa Perlu System Call?

```
TANPA SYSTEM CALL:
┌────────────────────────────────────────────────────────┐
│  User Program                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │                                                  │  │
│  │  // Mau menulis ke disk                          │  │
│  │  // Harus akses hardware langsung:               │  │
│  │                                                  │  │
│  │  outb(0x1F7, 0x20);  // Command register         │  │
│  │  outb(0x1F6, 0xE0);  // Drive select             │  │
│  │  outb(0x1F5, 0x00);  // Cylinder high            │  │
│  │  ... banyak lagi ...                             │  │
│  │                                                  │  │
│  │  MASALAH:                                        │  │
│  │  ❌ Setiap program harus tahu detail hardware    │  │
│  │  ❌ Program bisa corrupt disk orang lain         │  │
│  │  ❌ No abstraction                               │  │
│  │  ❌ No protection                                │  │
│  │                                                  │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘

DENGAN SYSTEM CALL:
┌────────────────────────────────────────────────────────┐
│  User Program                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │                                                  │  │
│  │  // Mau menulis ke disk                          │  │
│  │  write(fd, buffer, size);                        │  │
│  │                                                  │  │
│  │  KEUNTUNGAN:                                     │  │
│  │  ✓ Simple API                                    │  │
│  │  ✓ Kernel handle detail hardware                 │  │
│  │  ✓ Kernel enforce permissions                    │  │
│  │  ✓ Portable across different hardware            │  │
│  │  ✓ Protected access                              │  │
│  │                                                  │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

### 4.3 Kategori System Call

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KATEGORI SYSTEM CALL                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  1. PROCESS CONTROL                                         │     │
│  │     fork()    - Buat proses baru                            │     │
│  │     exec()    - Load program baru                           │     │
│  │     exit()    - Terminasi proses                            │     │
│  │     wait()    - Tunggu child process                        │     │
│  │     kill()    - Kirim signal ke proses                      │     │
│  │     getpid()  - Dapatkan process ID                         │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  2. FILE MANAGEMENT                                         │     │
│  │     open()    - Buka file                                   │     │
│  │     close()   - Tutup file                                  │     │
│  │     read()    - Baca dari file                              │     │
│  │     write()   - Tulis ke file                               │     │
│  │     lseek()   - Pindah posisi dalam file                    │     │
│  │     stat()    - Dapatkan info file                          │     │
│  │     unlink()  - Hapus file                                  │     │
│  │     mkdir()   - Buat direktori                              │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  3. DEVICE MANAGEMENT                                       │     │
│  │     ioctl()   - Control device                              │     │
│  │     read()    - Baca dari device                            │     │
│  │     write()   - Tulis ke device                             │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  4. INFORMATION MAINTENANCE                                 │     │
│  │     time()    - Waktu sistem                                │     │
│  │     uname()   - Info sistem                                 │     │
│  │     getuid()  - Dapatkan user ID                            │     │
│  │     gethostname() - Nama host                               │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  5. COMMUNICATION                                           │     │
│  │     pipe()    - Buat pipe                                   │     │
│  │     socket()  - Buat socket                                 │     │
│  │     connect() - Connect ke server                           │     │
│  │     send()    - Kirim data                                  │     │
│  │     recv()    - Terima data                                 │     │
│  │     mmap()    - Memory mapping                              │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐     │
│  │  6. MEMORY MANAGEMENT                                       │     │
│  │     brk()     - Ubah data segment size                      │     │
│  │     mmap()    - Map file ke memory                          │     │
│  │     munmap()  - Unmap memory                                │     │
│  │     mprotect()- Ubah memory protection                      │     │
│  └─────────────────────────────────────────────────────────────┘     │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.4 Mekanisme System Call (x86-64 Linux)

```
┌─────────────────────────────────────────────────────────────────────┐
│             SYSTEM CALL MECHANISM (x86-64 Linux)                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  USER SPACE                                                          │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │                                                                │  │
│  │  write(1, "Hello", 5);                                         │  │
│  │         │                                                      │  │
│  │         ▼                                                      │  │
│  │  /* glibc wrapper */                                           │  │
│  │  mov rax, 1        // syscall number (write = 1)               │  │
│  │  mov rdi, 1        // arg1: fd (stdout)                        │  │
│  │  mov rsi, "Hello"  // arg2: buffer                             │  │
│  │  mov rdx, 5        // arg3: count                              │  │
│  │  syscall           // MASUK KE KERNEL! ─────────────┐          │  │
│  │                                                      │          │  │
│  └──────────────────────────────────────────────────────┼──────────┘  │
│ ═══════════════════════════════════════════════════════════════════  │
│  KERNEL SPACE                                           │            │
│  ┌──────────────────────────────────────────────────────┼──────────┐  │
│  │                                                      ▼          │  │
│  │  1. CPU switch ke Ring 0 (kernel mode)                         │  │
│  │  2. Save user registers                                        │  │
│  │  3. Lookup syscall table: sys_call_table[rax]                  │  │
│  │  4. Call sys_write(rdi, rsi, rdx)                              │  │
│  │                     │                                          │  │
│  │                     ▼                                          │  │
│  │  ┌───────────────────────────────────────────────────────┐     │  │
│  │  │  sys_write()                                          │     │  │
│  │  │  {                                                    │     │  │
│  │  │    - Validasi fd (apakah valid?)                      │     │  │
│  │  │    - Check permission                                 │     │  │
│  │  │    - Copy data dari user space                        │     │  │
│  │  │    - Write ke file/device                             │     │  │
│  │  │    - Return jumlah bytes written                      │     │  │
│  │  │  }                                                    │     │  │
│  │  └───────────────────────────────────────────────────────┘     │  │
│  │                     │                                          │  │
│  │                     ▼                                          │  │
│  │  5. Set return value di rax                                    │  │
│  │  6. Restore user registers                                     │  │
│  │  7. sysret instruction (kembali ke Ring 3)                     │  │
│  │                     │                                          │  │
│  └─────────────────────┼──────────────────────────────────────────┘  │
│ ═══════════════════════════════════════════════════════════════════  │
│  USER SPACE            │                                             │
│  ┌─────────────────────┼──────────────────────────────────────────┐  │
│  │                     ▼                                          │  │
│  │  // Return dari syscall                                        │  │
│  │  // rax berisi return value (5 jika sukses)                    │  │
│  │                                                                │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.5 Tabel System Call Linux (x86-64)

Beberapa syscall penting dan nomornya:

| No | Nama | Deskripsi |
|----|------|-----------|
| 0 | read | Baca dari file descriptor |
| 1 | write | Tulis ke file descriptor |
| 2 | open | Buka file |
| 3 | close | Tutup file descriptor |
| 4 | stat | Dapatkan file status |
| 9 | mmap | Map memory |
| 10 | mprotect | Set memory protection |
| 12 | brk | Ubah data segment |
| 39 | getpid | Dapatkan process ID |
| 57 | fork | Buat child process |
| 59 | execve | Execute program |
| 60 | exit | Terminasi proses |
| 61 | wait4 | Wait for process |
| 62 | kill | Send signal |

### 4.6 Library Function vs System Call

```
┌─────────────────────────────────────────────────────────────────────┐
│           LIBRARY FUNCTION vs SYSTEM CALL                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  LIBRARY FUNCTION (contoh: printf)                                   │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │                                                                │  │
│  │  printf("Number: %d\n", 42);                                   │  │
│  │                                                                │  │
│  │  Apa yang terjadi:                                             │  │
│  │  1. printf() di glibc                                          │  │
│  │  2. Format string processing                                   │  │
│  │  3. Buffer hasil: "Number: 42\n"                               │  │
│  │  4. write() system call ◄── Hanya 1 syscall!                   │  │
│  │                                                                │  │
│  │  Karakteristik:                                                │  │
│  │  - Berjalan di user space                                      │  │
│  │  - Lebih banyak fitur (formatting, buffering)                  │  │
│  │  - May atau may not memanggil syscall                          │  │
│  │  - Bisa di-port ke OS berbeda                                  │  │
│  │                                                                │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  SYSTEM CALL (contoh: write)                                         │
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │                                                                │  │
│  │  write(1, "Hello", 5);                                         │  │
│  │                                                                │  │
│  │  Apa yang terjadi:                                             │  │
│  │  1. Langsung masuk ke kernel                                   │  │
│  │  2. Kernel proses request                                      │  │
│  │  3. Kembali ke user space                                      │  │
│  │                                                                │  │
│  │  Karakteristik:                                                │  │
│  │  - Interface langsung ke kernel                                │  │
│  │  - Minimal overhead untuk fitur                                │  │
│  │  - OS-specific                                                 │  │
│  │  - Selalu melibatkan mode switch                               │  │
│  │                                                                │  │
│  └────────────────────────────────────────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.7 Overhead System Call

System call memiliki overhead karena:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SYSTEM CALL OVERHEAD                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Regular Function Call: ~1-5 clock cycles                           │
│  ┌─────┐      ┌─────┐                                               │
│  │Call │ ───► │ Ret │                                               │
│  └─────┘      └─────┘                                               │
│                                                                      │
│  System Call: ~100-1000+ clock cycles                               │
│  ┌─────┐                                                            │
│  │User │                                                            │
│  │Mode │                                                            │
│  └──┬──┘                                                            │
│     │ 1. Save all registers                                         │
│     │ 2. Switch to kernel stack                                     │
│     │ 3. Switch to kernel page tables                               │
│     │ 4. Mode switch (Ring 3 → Ring 0)                              │
│     ▼                                                               │
│  ┌─────┐                                                            │
│  │Kernel                                                            │
│  │Mode │ Process request                                            │
│  └──┬──┘                                                            │
│     │ 5. Switch back to user page tables                            │
│     │ 6. Switch to user stack                                       │
│     │ 7. Mode switch (Ring 0 → Ring 3)                              │
│     │ 8. Restore all registers                                      │
│     ▼                                                               │
│  ┌─────┐                                                            │
│  │User │                                                            │
│  │Mode │                                                            │
│  └─────┘                                                            │
│                                                                      │
│  IMPLIKASI:                                                          │
│  - Minimize syscall frequency                                        │
│  - Batch operations when possible                                    │
│  - Use buffered I/O                                                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## TUTORIAL PRAKTIS

### Tutorial 1: Melihat System Calls dengan strace

**Tujuan:** Memahami syscalls yang dipanggil oleh program sederhana.

**Langkah-langkah:**

```bash
# 1. Install strace jika belum ada
sudo apt install strace

# 2. Buat program sederhana
cat > hello.c << 'EOF'
#include <stdio.h>
int main() {
    printf("Hello, OS!\n");
    return 0;
}
EOF

# 3. Compile
gcc -o hello hello.c

# 4. Jalankan dengan strace
strace ./hello

# 5. Lihat hanya syscalls tertentu
strace -e trace=write,open,close ./hello

# 6. Hitung jumlah syscalls
strace -c ./hello
```

**Output yang diharapkan:**

```
execve("./hello", ["./hello"], 0x7ffc...) = 0
brk(NULL)                               = 0x55a...
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0..."..., 832) = 832
...
write(1, "Hello, OS!\n", 11)            = 11
exit_group(0)                           = ?
```

### Tutorial 2: Melihat Process Memory Map

**Tujuan:** Memahami layout memory dari proses berjalan.

```bash
# 1. Jalankan program di background
sleep 1000 &
PID=$!

# 2. Lihat memory map
cat /proc/$PID/maps

# 3. Interpretasi output:
# address           perms offset  dev   inode   pathname
# 561a8c200000-561a8c202000 r--p 00000000 08:02 1234567 /usr/bin/sleep
# ↑               ↑    ↑      ↑     ↑       ↑
# |               |    |      |     |       |
# |               |    |      |     |       └── Path executable
# |               |    |      |     └── Inode number
# |               |    |      └── Device (major:minor)
# |               |    └── Offset dalam file
# |               └── Permissions: r=read, w=write, x=execute, p=private, s=shared
# └── Address range

# 4. Hentikan process
kill $PID
```

### Tutorial 3: Membuat System Call Langsung (Assembly)

**Tujuan:** Memahami cara kerja syscall di level rendah.

```bash
# Buat file assembly
cat > syscall_demo.s << 'EOF'
.section .data
    msg:    .ascii "Hello from syscall!\n"
    len = . - msg

.section .text
    .global _start

_start:
    # write(1, msg, len)
    mov $1, %rax        # syscall number: write = 1
    mov $1, %rdi        # fd: stdout = 1
    lea msg(%rip), %rsi # buffer address
    mov $len, %rdx      # length
    syscall             # invoke kernel

    # exit(0)
    mov $60, %rax       # syscall number: exit = 60
    xor %rdi, %rdi      # exit code: 0
    syscall
EOF

# Assemble dan link
as -o syscall_demo.o syscall_demo.s
ld -o syscall_demo syscall_demo.o

# Jalankan
./syscall_demo

# Verifikasi dengan strace
strace ./syscall_demo
```

### Tutorial 4: Melihat Kernel Version dan Info

```bash
# 1. Lihat versi kernel
uname -r

# 2. Informasi lengkap sistem
uname -a

# 3. Lihat kernel config (jika tersedia)
cat /boot/config-$(uname -r) | head -50

# 4. Lihat kernel messages
dmesg | head -30

# 5. Lihat modul kernel yang loaded
lsmod

# 6. Info tentang modul tertentu
modinfo ext4
```

---

## PRAKTIKUM

### Praktikum 1: Eksplorasi Proses

**Durasi:** 45 menit

**Tugas:**

1. **Buka terminal dan jalankan:**
   ```bash
   # Catat PID shell Anda
   echo $$
   
   # Lihat informasi proses
   ps aux | grep $$
   ```

2. **Eksplorasi /proc:**
   ```bash
   # Masuk ke direktori proses Anda
   cd /proc/$$
   
   # Lihat file-file di sini
   ls -la
   
   # Baca berbagai informasi:
   cat status      # Status proses
   cat cmdline     # Command line
   cat environ     # Environment variables
   cat limits      # Resource limits
   cat fd          # File descriptors (direktori)
   ```

3. **Buat laporan berisi:**
   - Screenshot setiap perintah dan outputnya
   - Penjelasan setiap field di `/proc/[PID]/status`
   - Daftar file descriptor yang terbuka

### Praktikum 2: Tracing System Calls

**Durasi:** 60 menit

**Tugas:**

1. **Trace program berbeda dan bandingkan:**
   ```bash
   # Program 1: ls
   strace -c ls /tmp
   
   # Program 2: cat
   strace -c cat /etc/passwd
   
   # Program 3: Python
   strace -c python3 -c "print('hello')"
   ```

2. **Analisis hasil:**
   - Program mana yang paling banyak syscalls?
   - Syscall apa yang paling sering dipanggil?
   - Mengapa ada perbedaan?

3. **Eksperimen buffering:**
   ```bash
   # Buat file besar
   dd if=/dev/urandom of=/tmp/bigfile bs=1M count=10
   
   # Compare unbuffered vs buffered read
   strace -e trace=read -c cat /tmp/bigfile > /dev/null
   strace -e trace=read -c dd if=/tmp/bigfile of=/dev/null bs=4096
   ```

### Praktikum 3: Kernel Modules

**Durasi:** 45 menit

**Tugas:**

1. **Lihat modul saat ini:**
   ```bash
   lsmod | head -20
   ```

2. **Cari modul untuk hardware tertentu:**
   ```bash
   # Network
   lsmod | grep -E "e1000|rtl|iwl|ath"
   
   # Sound
   lsmod | grep snd
   
   # USB
   lsmod | grep usb
   ```

3. **Lihat dependencies:**
   ```bash
   # Pilih satu modul
   modinfo <nama_modul>
   
   # Lihat dependensi
   cat /lib/modules/$(uname -r)/modules.dep | grep <nama_modul>
   ```

### Praktikum 4: Process Creation

**Durasi:** 60 menit

**Tugas:**

Buat program C yang mendemonstrasikan fork():

```c
// fork_demo.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    printf("Parent PID: %d\n", getpid());
    
    pid_t pid = fork();
    
    if (pid < 0) {
        // Error
        perror("fork failed");
        exit(1);
    } else if (pid == 0) {
        // Child process
        printf("Child: My PID is %d, Parent PID is %d\n", 
               getpid(), getppid());
        sleep(2);
        printf("Child: Exiting\n");
        exit(42);  // Exit dengan code 42
    } else {
        // Parent process
        printf("Parent: Created child with PID %d\n", pid);
        
        int status;
        waitpid(pid, &status, 0);
        
        if (WIFEXITED(status)) {
            printf("Parent: Child exited with code %d\n", 
                   WEXITSTATUS(status));
        }
    }
    
    return 0;
}
```

**Compile dan jalankan:**
```bash
gcc -o fork_demo fork_demo.c
./fork_demo
strace -f ./fork_demo  # -f untuk follow forks
```

---

## STUDI KASUS

### Studi Kasus 1: Mengapa Program Crash?

**Skenario:**
Seorang developer melaporkan programnya crash dengan error "Segmentation Fault".

**Investigasi:**

```c
// buggy.c - Program dengan bug
#include <stdio.h>
#include <string.h>

int main() {
    char *ptr = NULL;
    strcpy(ptr, "Hello");  // CRASH! Dereferencing NULL pointer
    return 0;
}
```

**Analisis:**

```bash
# Compile dengan debug symbols
gcc -g -o buggy buggy.c

# Jalankan
./buggy
# Output: Segmentation fault (core dumped)

# Debug dengan gdb
gdb ./buggy
(gdb) run
# Program received signal SIGSEGV, Segmentation fault.
(gdb) bt
# Backtrace menunjukkan di mana crash
```

**Penjelasan:**
```
┌────────────────────────────────────────────────────────────────┐
│                    SEGMENTATION FAULT                          │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  Program mencoba:                                              │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  Write to address 0x0 (NULL)                            │   │
│  └─────────────────────────────────────────────────────────┘   │
│                     │                                          │
│                     ▼                                          │
│  MMU Translation:                                              │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  Virtual Address 0x0 → PAGE FAULT!                      │   │
│  │  (Halaman ini tidak mapped atau tidak writable)         │   │
│  └─────────────────────────────────────────────────────────┘   │
│                     │                                          │
│                     ▼                                          │
│  Kernel Response:                                              │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  1. Cek apakah valid page fault                         │   │
│  │  2. Address 0x0 tidak valid                             │   │
│  │  3. Send SIGSEGV signal ke process                      │   │
│  │  4. Default handler: terminate process                  │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                │
│  KESIMPULAN:                                                   │
│  Protection mechanism bekerja! Program berbahaya               │
│  dihentikan sebelum merusak data lain.                        │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### Studi Kasus 2: Kenapa Server Lambat?

**Skenario:**
Web server Apache tiba-tiba lambat. Perlu investigasi.

**Investigasi:**

```bash
# 1. Cek load system
uptime
# Output: load average: 15.23, 14.56, 12.89  (TINGGI!)

# 2. Cek proses yang menggunakan CPU
top -o %CPU
# atau
htop

# 3. Cek memory usage
free -h
# Output mungkin menunjukkan swap tinggi

# 4. Cek I/O
iostat -x 1
# atau
iotop

# 5. Cek open files
lsof | wc -l
# Terlalu banyak? Mungkin file descriptor leak

# 6. Cek network
netstat -tuln | wc -l
ss -s
```

**Diagnosis:**

```
┌────────────────────────────────────────────────────────────────┐
│                    DIAGNOSIS HASIL                             │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  TEMUAN:                                                       │
│  1. Load average sangat tinggi (>15)                          │
│  2. Memory hampir habis, banyak swap                          │
│  3. Apache menggunakan terlalu banyak proses                  │
│                                                                │
│  ANALISIS:                                                     │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Apache prefork MPM dengan MaxRequestWorkers=256         │  │
│  │  Setiap worker = ~50MB RAM                               │  │
│  │  256 × 50MB = 12.8GB RAM dibutuhkan                      │  │
│  │  Server hanya punya 8GB RAM                              │  │
│  │  → Swapping terjadi → System lambat                      │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                │
│  SOLUSI:                                                       │
│  1. Kurangi MaxRequestWorkers                                 │
│  2. Switch ke worker atau event MPM                           │
│  3. Tambah RAM                                                │
│  4. Implementasi caching                                      │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

### Studi Kasus 3: Security - Privilege Escalation Attempt

**Skenario:**
Admin mendeteksi upaya mencurigakan untuk mendapatkan root access.

**Log Analysis:**

```bash
# Cek auth log
sudo grep -i "failed\|error\|denied" /var/log/auth.log

# Cek sudo attempts
sudo grep "sudo" /var/log/auth.log

# Cek unusual processes
ps aux --sort=-%cpu | head -20
```

**Temuan:**

```
┌────────────────────────────────────────────────────────────────┐
│                    SECURITY INCIDENT                           │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  LOG ENTRY:                                                    │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Feb 28 14:23:15 server sudo: user 'bob': authentication │  │
│  │  failure; TTY=pts/0 ; PWD=/tmp ; USER=root ;             │  │
│  │  COMMAND=/bin/bash                                       │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                │
│  ANALISIS:                                                     │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  1. User 'bob' mencoba sudo ke root                      │  │
│  │  2. Authentication gagal (password salah atau tidak      │  │
│  │     terdaftar di sudoers)                                │  │
│  │  3. Directory /tmp (mencurigakan)                        │  │
│  │  4. Mencoba spawn bash shell sebagai root                │  │
│  └──────────────────────────────────────────────────────────┘  │
│                                                                │
│  KERNEL PROTECTION YANG BEKERJA:                               │
│  1. User/kernel mode separation → bob tidak bisa bypass sudo  │
│  2. Permission system → bob tidak punya akses                 │
│  3. Logging → semua tercatat untuk forensik                   │
│                                                                │
│  TINDAKAN:                                                     │
│  1. Investigasi user bob                                      │
│  2. Audit /tmp untuk file mencurigakan                        │
│  3. Cek bash history bob                                      │
│  4. Pertimbangkan temporary lockout                           │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

---

## TANTANGAN

### Tantangan 1: Detective Mode 🔍

**Level: Pemula**

Gunakan perintah-perintah yang telah dipelajari untuk menjawab pertanyaan berikut tentang sistem Anda:

1. Kernel apa yang sedang berjalan? (versi exact)
2. Berapa total RAM dan berapa yang terpakai?
3. Berapa banyak proses yang berjalan saat ini?
4. Berapa lama sistem sudah berjalan (uptime)?
5. Arsitektur CPU apa yang digunakan?
6. Filesystem apa yang digunakan di root partition?

**Petunjuk:** Gunakan `uname`, `free`, `ps`, `uptime`, `lscpu`, `df`

### Tantangan 2: Syscall Hunter 🎯

**Level: Menengah**

1. Buat script bash sederhana yang mencetak "Hello World"
2. Gunakan `strace` untuk mencatat SEMUA syscalls
3. Identifikasi:
   - Syscall pertama yang dipanggil
   - Syscall terakhir sebelum exit
   - Syscall yang membuka/membaca file
   - Total jumlah syscall unik

**Bonus:** Bandingkan jumlah syscalls antara script bash vs program C equivalent.

### Tantangan 3: Memory Explorer 🧠

**Level: Menengah**

1. Tulis program C yang:
   - Mengalokasi array 100MB
   - Menulis data ke array tersebut
   - Sleep selama 60 detik
   - Cleanup dan exit

2. Sementara program berjalan:
   - Gunakan `/proc/[PID]/maps` untuk melihat memory layout
   - Gunakan `/proc/[PID]/status` untuk melihat memory usage
   - Screenshot dan jelaskan setiap region memory

### Tantangan 4: Fork Bomb Defense 💣

**Level: Lanjut**

**PERINGATAN: Jangan jalankan di sistem production!**

```bash
# INI ADALAH FORK BOMB - JANGAN JALANKAN!
# :(){ :|:& };:
```

**Tugas:**
1. Jelaskan bagaimana fork bomb bekerja (JANGAN JALANKAN!)
2. Apa mekanisme kernel yang bisa mencegahnya?
3. Bagaimana cara set limit untuk mencegah fork bomb?
4. Implementasikan pencegahan dengan `ulimit`

### Tantangan 5: Build Your Own 🏗️

**Level: Expert**

Buat program C yang mengimplementasikan mini-shell dengan fitur:
1. Prompt dengan username dan current directory
2. Execute commands (fork + exec)
3. Handle piping sederhana (command1 | command2)
4. Built-in commands: `cd`, `exit`, `pwd`

---

## SOAL LATIHAN

### Pilihan Ganda

**1.** Fungsi utama Operating System adalah...
- [ ] A. Hanya menjalankan aplikasi
- [ ] B. Hanya menyediakan GUI
- [x] C. Sebagai perantara antara user dan hardware
- [ ] D. Hanya mengatur memory

**2.** Kernel mode (Ring 0) memiliki privilege lebih tinggi dari user mode karena...
- [ ] A. Berjalan lebih cepat
- [x] B. Memiliki akses penuh ke hardware dan memory
- [ ] C. Menggunakan lebih banyak RAM
- [ ] D. Hanya bisa diakses oleh root user

**3.** System call adalah...
- [ ] A. Pemanggilan fungsi biasa di C
- [x] B. Mekanisme untuk meminta layanan dari kernel
- [ ] C. Cara untuk call fungsi dari library
- [ ] D. Metode komunikasi antar proses

**4.** Pada Linux x86-64, instruksi yang digunakan untuk melakukan system call adalah...
- [ ] A. `int 0x80`
- [x] B. `syscall`
- [ ] C. `call kernel`
- [ ] D. `sysenter`

**5.** Apa yang terjadi jika program user space mencoba mengakses memory kernel secara langsung?
- [ ] A. Program mendapat akses ke kernel
- [ ] B. Kernel mengizinkan akses
- [x] C. Program menerima Segmentation Fault
- [ ] D. System melakukan reboot

### Essay

**1.** Jelaskan perbedaan antara Monolithic Kernel dan Microkernel. Berikan contoh OS untuk masing-masing dan diskusikan kelebihan/kekurangannya. (20 poin)

**2.** Gambarkan dan jelaskan proses yang terjadi saat Anda mengetik `./program` di terminal hingga program tersebut berjalan. Sertakan system calls yang terlibat. (25 poin)

**3.** Mengapa pemisahan Kernel Space dan User Space penting untuk keamanan sistem? Berikan contoh skenario serangan yang bisa dicegah dengan pemisahan ini. (20 poin)

**4.** Jelaskan konsep Virtual Memory dan bagaimana OS menggunakannya untuk memberikan ilusi bahwa setiap proses memiliki address space sendiri. (20 poin)

**5.** Analisis mengapa system call memiliki overhead yang lebih besar dibanding function call biasa. Apa implikasinya terhadap performa program? (15 poin)

### Praktik

**1.** Buat program C yang mendemonstrasikan perbedaan antara library function (`printf`) dan system call (`write`). Trace keduanya dengan `strace` dan analisis perbedaannya. (30 poin)

**2.** Implementasikan program yang membuat child process dengan `fork()`, di mana child menjalankan program berbeda menggunakan `execvp()`. Parent harus menunggu child selesai dan mencetak exit status. (35 poin)

**3.** Tulis script yang mengumpulkan informasi sistem berikut dan menyimpannya ke file:
- Versi kernel
- Total dan free memory
- Daftar 10 proses dengan CPU tertinggi
- Daftar semua loaded kernel modules
- Mount points dan filesystem types
(35 poin)

---

## RANGKUMAN

### Poin-Poin Kunci

```
┌─────────────────────────────────────────────────────────────────────┐
│                        RANGKUMAN BAB 1                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  📌 OPERATING SYSTEM ADALAH:                                        │
│  • Perantara antara user dan hardware                              │
│  • Pengelola resources (CPU, memory, I/O, storage)                 │
│  • Penyedia abstraksi dan layanan umum                             │
│                                                                     │
│  📌 KERNEL VS USER SPACE:                                           │
│  • Kernel Space (Ring 0): Akses penuh, bisa akses hardware         │
│  • User Space (Ring 3): Terbatas, harus via system call            │
│  • Pemisahan untuk keamanan dan stabilitas                         │
│                                                                     │
│  📌 CARA PROGRAM DIJALANKAN:                                        │
│  • Source → Compile → Object → Link → Executable                   │
│  • Executable di-load ke memory oleh loader                        │
│  • Fork() untuk membuat proses baru                                │
│  • Exec() untuk mengganti program                                  │
│                                                                     │
│  📌 SYSTEM CALL:                                                    │
│  • Satu-satunya cara legal mengakses kernel services               │
│  • Melibatkan mode switch (overhead)                               │
│  • Kategori: Process, File, Device, Info, Communication, Memory    │
│  • Contoh: read(), write(), fork(), exec(), exit()                 │
│                                                                     │
│  📌 TOOLS PENTING:                                                  │
│  • strace - trace system calls                                     │
│  • /proc - virtual filesystem untuk info proses                    │
│  • ps, top, htop - monitor proses                                  │
│  • lsmod, modinfo - info kernel modules                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Mindmap

```
                              ┌─────────────┐
                              │ OPERATING   │
                              │   SYSTEM    │
                              └──────┬──────┘
                                     │
         ┌───────────────────────────┼───────────────────────────┐
         │                           │                           │
         ▼                           ▼                           ▼
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│    DEFINISI     │       │  KERNEL vs USER │       │  CARA PROGRAM   │
│                 │       │     SPACE       │       │   DIJALANKAN    │
├─────────────────┤       ├─────────────────┤       ├─────────────────┤
│• Perantara      │       │• Ring 0 vs 3    │       │• Compile        │
│• Resource Mgr   │       │• Protection     │       │• Link           │
│• Abstraction    │       │• Isolation      │       │• Load           │
│• Services       │       │• Stability      │       │• Fork + Exec    │
└─────────────────┘       └─────────────────┘       └─────────────────┘
                                     │
                                     │
                          ┌──────────┴──────────┐
                          │                     │
                          ▼                     ▼
               ┌─────────────────┐   ┌─────────────────┐
               │  SYSTEM CALL    │   │ KERNEL TYPES    │
               ├─────────────────┤   ├─────────────────┤
               │• Interface only │   │• Monolithic     │
               │• Mode switch    │   │• Microkernel    │
               │• Categories:    │   │• Hybrid         │
               │  - Process      │   │• Modular        │
               │  - File         │   │                 │
               │  - Device       │   │                 │
               │  - Memory       │   │                 │
               │  - Network      │   │                 │
               └─────────────────┘   └─────────────────┘
```

---

## REFERENSI

### Buku

1. **Silberschatz, A., Galvin, P. B., & Gagne, G.** (2018). *Operating System Concepts* (10th ed.). Wiley.
   - Bab 1-2: Introduction, Operating System Structures

2. **Tanenbaum, A. S., & Bos, H.** (2014). *Modern Operating Systems* (4th ed.). Pearson.
   - Bab 1: Introduction

3. **Love, R.** (2010). *Linux Kernel Development* (3rd ed.). Addison-Wesley.
   - Bab 1-5: Introduction to Linux Kernel

4. **Kerrisk, M.** (2010). *The Linux Programming Interface*. No Starch Press.
   - Bab 1-3: History and Standards, Fundamental Concepts, System Programming Concepts

5. **Bovet, D. P., & Cesati, M.** (2005). *Understanding the Linux Kernel* (3rd ed.). O'Reilly.

### Online Resources

1. **Linux Kernel Documentation**
   - https://www.kernel.org/doc/html/latest/

2. **The Linux Documentation Project**
   - https://tldp.org/

3. **Linux man pages**
   - `man 2 syscalls` - Daftar system calls
   - `man 7 signal` - Signals
   - `man 5 proc` - /proc filesystem

4. **OSDev Wiki**
   - https://wiki.osdev.org/

5. **Computer Systems: A Programmer's Perspective (CS:APP)**
   - http://csapp.cs.cmu.edu/

### Video Tutorials

1. **MIT OpenCourseWare - Operating System Engineering**
   - https://ocw.mit.edu/courses/6-828-operating-system-engineering/

2. **UC Berkeley CS162 - Operating Systems**
   - Tersedia di YouTube

### Tools Documentation

1. **strace manual**: `man strace`
2. **GDB documentation**: https://www.gnu.org/software/gdb/documentation/
3. **Proc filesystem**: `man 5 proc`

---

## GLOSARIUM

| Istilah | Definisi |
|---------|----------|
| **Kernel** | Inti dari Operating System yang memiliki akses penuh ke hardware |
| **User Space** | Area memory dan mode eksekusi untuk program aplikasi |
| **Kernel Space** | Area memory yang hanya bisa diakses oleh kernel |
| **System Call** | Interface untuk meminta layanan dari kernel |
| **Process** | Instance dari program yang sedang berjalan |
| **Thread** | Unit eksekusi terkecil dalam sebuah proses |
| **Virtual Memory** | Abstraksi memory yang memberikan ilusi continuous address space |
| **MMU** | Memory Management Unit - hardware yang menerjemahkan virtual ke physical address |
| **ELF** | Executable and Linkable Format - format file executable di Linux |
| **Fork** | System call untuk membuat proses baru dengan duplikasi |
| **Exec** | System call untuk mengganti image program dalam proses |
| **Ring** | Level privilege di CPU, Ring 0 tertinggi |
| **Interrupt** | Sinyal ke CPU untuk meminta perhatian |
| **Context Switch** | Proses menyimpan state satu proses dan load state proses lain |
| **Scheduler** | Komponen OS yang memutuskan proses mana yang berjalan |
| **Daemon** | Proses background yang berjalan tanpa interaksi langsung user |
| **Signal** | Notifikasi asinkron ke proses |
| **IPC** | Inter-Process Communication - mekanisme komunikasi antar proses |
| **Syscall Table** | Array pointer ke fungsi handler system call di kernel |
| **Segmentation Fault** | Error saat program mengakses memory yang tidak diizinkan |

---

## NEXT CHAPTER PREVIEW

**Bab 2: Struktur Sistem Linux**

Di bab selanjutnya, kita akan mempelajari:
- Struktur direktori Linux secara mendalam (/, /bin, /etc, /home, dll)
- Filosofi "Everything is a file"
- Filesystem Hierarchy Standard (FHS)
- Studi Kasus: Kenapa config ada di /etc?

---

*Selamat! Anda telah menyelesaikan Bab 1. Sekarang Anda memiliki fondasi yang kuat tentang Operating System. Lanjutkan ke praktikum untuk memperdalam pemahaman Anda!*

---

## 👤 Author

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Muhammad Dhiyaul Atha                                     │
│   ─────────────────────                                     │
│                                                             │
│   📧 Connect with me:                                       │
│   🔗 GitHub: github.com/dhiyaulatha                         │
│                                                             │
│   "The only way to learn is by doing."                      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

<div align="center">

**📘 Learning Notes Collection**

Made with ❤️ by **Muhammad Dhiyaul Atha**

**Versi:** 1.0 | **Tanggal:** Maret 2026 | **Lisensi:** CC BY-SA 4.0

</div>
