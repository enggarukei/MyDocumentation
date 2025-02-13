Berikut adalah dokumentasi lengkap untuk **Setup Zabbix untuk Pemantauan Jaringan** dengan **Server di Proxmox**, **IP Zabbix 10.10.10.252**, **VM Debian Server**, dan **domain unsp.my.id**.

---

# **📡 Setup Zabbix untuk Pemantauan Jaringan**

## **1️⃣ Pendahuluan**

Dokumentasi ini menjelaskan langkah-langkah dalam menginstal dan mengonfigurasi **Zabbix Server** di **VM Debian Server** yang berjalan di **Proxmox** dengan IP **10.10.10.252**, serta menghubungkan domain **unsp.my.id** untuk akses berbasis web.

---

## **2️⃣ Persiapan Sistem**

### **🔹 Spesifikasi Minimal Server Zabbix**

- **CPU**: 2 vCPU
- **RAM**: 2GB+
- **Storage**: 20GB+
- **OS**: Debian 11 atau 12
- **Database**: MariaDB
- **Web Server**: Nginx atau Apache

---

## **3️⃣ Instalasi Zabbix di Debian Server (10.10.10.252)**

### **🔹 3.1 Update Sistem**

```bash
sudo apt update && sudo apt upgrade -y
```

### **🔹 3.2 Tambahkan Repository Zabbix**

```bash
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-1+debian$(lsb_release -sr)_all.deb
sudo dpkg -i zabbix-release_7.0-1+debian$(lsb_release -sr)_all.deb
sudo apt update
```

### **🔹 3.3 Instal Paket Zabbix Server, Frontend, dan Agen**

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent -y
```

---

## **4️⃣ Setup Database untuk Zabbix**

### **🔹 4.1 Instal MariaDB**

```bash
sudo apt install mariadb-server -y
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

### **🔹 4.2 Konfigurasi Database Zabbix**

Masuk ke MariaDB:

```bash
sudo mysql -u root -p
```

Buat database dan user Zabbix:

```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'passwordku123';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Import skema database:

```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -u zabbix -p zabbix
```

---

## **5️⃣ Konfigurasi Zabbix Server & Nginx**

### **🔹 5.1 Edit Konfigurasi Zabbix Server**

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

Ubah baris berikut:

```ini
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=passwordku123
```

Simpan (`CTRL + X`, `Y`, `ENTER`).

### **🔹 5.2 Konfigurasi Nginx**

Edit file konfigurasi Nginx:

```bash
sudo nano /etc/zabbix/nginx.conf
```

Ubah baris:

```ini
listen 80;
server_name unsp.my.id;
```

Simpan lalu restart Nginx:

```bash
sudo systemctl restart nginx
```

---

## **6️⃣ Menghubungkan Zabbix dengan Domain (unsp.my.id)**

### **🔹 6.1 Arahkan Domain ke IP Zabbix**

Buka **DNS Management** dan tambahkan **A Record**:

```
Type: A
Name: unsp
Value: 10.10.10.252
```

### **🔹 6.2 Instal & Konfigurasi SSL (Opsional, Jika Domain Bisa Diakses Publik)**

Instal **Certbot**:

```bash
sudo apt install certbot python3-certbot-nginx -y
```

Dapatkan SSL gratis dari Let’s Encrypt:

```bash
sudo certbot --nginx -d unsp.my.id
```

Verifikasi bahwa SSL sudah aktif dengan:

```bash
sudo certbot renew --dry-run
```

---

## **7️⃣ Jalankan & Cek Zabbix Server**

### **🔹 7.1 Jalankan Zabbix Server & Agen**

```bash
sudo systemctl restart zabbix-server zabbix-agent nginx php7.4-fpm
sudo systemctl enable zabbix-server zabbix-agent nginx php7.4-fpm
```

### **🔹 7.2 Akses Zabbix Web Interface**

Buka browser dan akses:

```
http://unsp.my.id
```

**Login dengan:**

- **Username**: `Admin`
- **Password**: `zabbix`

---

## **8️⃣ Menambahkan Host untuk Monitoring**

1. **Login ke Zabbix Web Interface**
2. **Masuk ke "Configuration" → "Hosts"**
3. **Klik "Create Host"**, lalu:
    - **Hostname**: `Server Debian`
    - **Visible Name**: `Server Debian`
    - **Groups**: `Linux Servers`
    - **Agent Interface**: `10.10.10.252`
4. **Klik "Templates" → Pilih Template "Linux by Zabbix Agent"**
5. **Klik "Add"**

Setelah beberapa menit, Zabbix akan mulai mengumpulkan data!

---

## **9️⃣ Troubleshooting (Jika Ada Masalah)**

### **🔹 Cek Log Zabbix Server**

```bash
tail -f /var/log/zabbix/zabbix_server.log
```

### **🔹 Pastikan Zabbix Berjalan dengan Benar**

```bash
sudo systemctl status zabbix-server zabbix-agent nginx php7.4-fpm
```

### **🔹 Jika Tidak Bisa Akses Web Zabbix**

Pastikan firewall tidak memblokir akses:

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 10051/tcp
sudo ufw reload
```

---

## **🎯 Kesimpulan**

✅ **Zabbix Server** telah berhasil diinstal di **Proxmox** dengan **VM Debian Server (10.10.10.252)**  
✅ **Akses Web Zabbix** tersedia di **[http://unsp.my.id](http://unsp.my.id/)**  
✅ **Pemantauan Host** sudah bisa dilakukan

---

Semoga dokumentasi ini membantu! 🚀 Jika ada pertanyaan atau kendala, silakan tanyakan. 😊
