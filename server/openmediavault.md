## **Dokumentasi Proyek NAS dengan OpenMediaVault**
**Nama Proyek:** Implementasi NAS Lokal untuk Nusa Putra University  
**Dibuat oleh:** Enggar Irawan  
**Tanggal Selesai:** 25 March 2025

---

### **1. Latar Belakang**
Implementasi Network Attached Storage (NAS) berbasis OpenMediaVault di lingkungan virtual menggunakan Proxmox, untuk menyimpan dan membagikan data secara terpusat dalam jaringan lokal Universitas Nusa Putra.

---

### **2. Infrastruktur**
- **Hypervisor:** Proxmox VE
- **VM NAS:**
  - **Nama VM:** OpenMediaVault
  - **CPU:** 2 Core
  - **RAM:** 4096
  - **Storage System Disk:** 16 GB (/dev/sda)
  - **Storage Data Disk:** 1 TB (/dev/sdb)
- **Network:** Jaringan lokal (LAN) Universitas Nusa Putra

---

### **3. Instalasi dan Konfigurasi OpenMediaVault**
#### a. **Instalasi VM**
1. Membuat VM baru di Proxmox dengan ISO OpenMediaVault.
2. Menambahkan dua disk:
   - Disk 1: 16 GB untuk sistem
   - Disk 2: 1 TB untuk penyimpanan data

#### b. **Format dan Mount Disk 1TB**
1. Buka **Storage > Disks**, pilih `/dev/sdb` (1TB).
2. Wipe disk menggunakan opsi "Wipe".
3. Buka **Storage > File System**, klik "Create":
   - Select `/dev/sdb`
   - Filesystem: EXT4
   - Mount filesystem setelah selesai
4. Mount filesystem ke sistem.

#### c. **Pembuatan Shared Folder**
1. Buka **Access Rights Management > Shared Folders**
2. Klik "Add":
   - Nama: `NusaPutra`
   - Path: `/NusaPutra`
   - Volume: pilih disk 1TB
3. Klik Save.

#### d. **Konfigurasi SMB/CIFS**
1. Buka **Services > SMB/CIFS**
2. Enable layanan
3. Di tab Shares:
   - Add share
   - Shared Folder: `NusaPutra`
   - Public: *No* (agar memerlukan autentikasi)
   - Klik Save dan Apply

---

### **4. Manajemen Pengguna**
1. Buka **Access Rights Management > Users**
2. Add user:
   - Username: NusaPutra
   - Password: ********
3. Assign ke grup `users`
4. Berikan permission ke shared folder di tab "Privileges"

---

### **5. Akses NAS**
- Dari Windows:
  - Buka `\\10.10.10.13\`
  - Masukkan username & password
- Dari Linux:
  - `smb://10.10.10.13` via File Manager atau terminal

---

### **6. Catatan Tambahan**
- Port WebUI OMV: 80
- Backup konfigurasi secara berkala
- Bisa ditambahkan fitur remote (VPN/SFTP) jika diperlukan

---
