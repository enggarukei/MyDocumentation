# Dokumentasi Instalasi dan Konfigurasi TrueNAS

## 1. Pendahuluan
TrueNAS adalah sistem operasi berbasis FreeBSD yang dirancang untuk menyajikan solusi penyimpanan berbasis ZFS yang handal dan mudah digunakan. TrueNAS sering digunakan untuk NAS (Network Attached Storage) dalam lingkungan rumah maupun perusahaan karena fiturnya yang kaya.

### Mengapa Memilih TrueNAS?
- Menggunakan ZFS untuk integritas data tinggi
- Dukungan untuk berbagai protokol seperti SMB, NFS, dan AFP
- Fitur snapshot dan backup yang kuat
- Antarmuka berbasis web yang mudah digunakan

## 2. Persiapan Instalasi

### Spesifikasi Minimum:
- CPU: 64-bit
- RAM: Minimum 8GB (direkomendasikan 16GB+ untuk ZFS)
- Storage: SSD/HDD dengan kapasitas sesuai kebutuhan
- Network: Minimal 1 Gbps Ethernet

### Download ISO TrueNAS
1. Kunjungi situs resmi TrueNAS: [https://www.truenas.com/download/](https://www.truenas.com/download/)
2. Unduh versi TrueNAS CORE terbaru.

### Persiapan Media Bootable
1. Gunakan **Rufus** atau **balenaEtcher** untuk membuat bootable USB.
2. Pilih ISO TrueNAS dan buat bootable pada USB minimal 8GB.

## 3. Proses Instalasi

### Booting dari USB
1. Masukkan USB bootable ke server atau VM (jika menggunakan Proxmox/VirtualBox/ESXi).
2. Boot dari USB dan pilih **Install/Upgrade TrueNAS**.

### Instalasi di Proxmox
Jika menggunakan Proxmox:
- Buat VM baru dengan minimal 16GB RAM.
- Gunakan **SATA** sebagai storage controller.
- Tambahkan virtual disk dengan ukuran sesuai kebutuhan.

### Konfigurasi Awal
1. Pilih disk tujuan instalasi.
2. Masukkan password root.
3. Tunggu hingga proses instalasi selesai.
4. Reboot sistem dan lepaskan USB installer.

## 4. Konfigurasi Storage

### Membuat ZFS Pool
1. Akses **TrueNAS WebUI** melalui `http://IP-TrueNAS`.
2. Masuk ke **Storage > Pools > Add**.
3. Pilih disk yang tersedia dan buat konfigurasi RAID (RAIDZ1, RAIDZ2, atau Mirror).
4. Klik **Create**.

### Konfigurasi Dataset dan Permission
1. Masuk ke **Storage > Pools**.
2. Tambahkan dataset untuk berbagai kebutuhan (misalnya: `Backup`, `Media`, dll.).
3. Atur permission sesuai kebutuhan pengguna.

## 5. Konfigurasi Jaringan

### IP Static & VLAN
1. Masuk ke **Network > Interfaces**.
2. Edit antarmuka jaringan dan atur **Static IP**.
3. Tambahkan VLAN jika diperlukan.

### Konfigurasi SMB/NFS/AFP
1. Masuk ke **Sharing** dan pilih protokol yang ingin digunakan.
2. Buat share baru dan arahkan ke dataset yang dibuat.
3. Atur akses kontrol sesuai kebutuhan.

## 6. Pengaturan User & Permissions

### Membuat User dan Grup
1. Masuk ke **Accounts > Users**.
2. Tambahkan user baru dengan hak akses sesuai kebutuhan.
3. Masuk ke **Accounts > Groups** untuk mengatur grup pengguna.

### Konfigurasi Akses Berbasis Permission
1. Masuk ke **Storage > Pools > Dataset**.
2. Pilih dataset dan klik **Edit Permissions**.
3. Atur akses berbasis user atau grup.

## 7. Testing & Troubleshooting

### Mengakses Share dari Windows/Linux
- Windows: Gunakan `\IP-TrueNAS\Share` pada File Explorer.
- Linux: Gunakan `mount -t cifs //IP-TrueNAS/Share /mnt/share`.

### Troubleshooting Umum
- **Tidak bisa mengakses share?**
  - Pastikan user memiliki permission yang benar.
  - Cek firewall dan izin layanan SMB/NFS.
- **Performance lambat?**
  - Gunakan disk SSD untuk cache ZIL/L2ARC.
  - Tambahkan lebih banyak RAM untuk performa ZFS yang lebih baik.

---
## Tentang Cara Penggunaan TrueNAS di Universitas Nusa Putra
<iframe src="https://drive.google.com/file/d/1-aitWKslHMD3fKAd-WbHR7w9d5aWUP0Q/view?usp=sharing" width="100%" height="600px"></iframe>

--- 

**Dokumentasi ini dibuat oleh : Enggarukei & Udin Kebab**

