# Dokumentasi Instalasi dan Konfigurasi SSL untuk Website Monitoring

## Informasi Umum
- **Domain:** monitoring.kaldyta.com
- **Server:** Debian dengan Apache2 sebagai Reverse Proxy
- **Aplikasi:** Grafana (port default 3000)
- **SSL Provider:** Let's Encrypt (Certbot)

## 1. Persiapan
Sebelum menginstal SSL, pastikan:
- Apache2 telah dikonfigurasi sebagai Reverse Proxy untuk Grafana.
- Domain sudah mengarah ke IP server.
- Port 80 dan 443 telah dibuka di firewall maupun di MikroTik.

## 2. Instalasi Certbot
Jalankan perintah berikut untuk menginstal Certbot:
```bash
sudo apt update && sudo apt install certbot python3-certbot-apache -y
```

## 3. Mengaktifkan SSL dengan Certbot
Jalankan perintah berikut untuk mendapatkan dan memasang sertifikat SSL:
```bash
sudo certbot --apache -d monitoring.kaldyta.com
```
Ikuti petunjuk yang diberikan Certbot.

## 4. Verifikasi SSL
Setelah pemasangan berhasil, verifikasi dengan perintah:
```bash
sudo certbot certificates
```
Pastikan domain terdaftar dengan status aktif.

## 5. Konfigurasi Virtual Host di Apache
Edit konfigurasi Virtual Host Apache untuk menggunakan SSL:
```bash
sudo nano /etc/apache2/sites-available/grafana.conf
```
Tambahkan atau modifikasi konfigurasi berikut:
```apache
<VirtualHost *:80>
    ServerName monitoring.kaldyta.com
    Redirect permanent / https://monitoring.kaldyta.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName monitoring.kaldyta.com

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/monitoring.kaldyta.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/monitoring.kaldyta.com/privkey.pem

    ProxyPass / http://localhost:3000/
    ProxyPassReverse / http://localhost:3000/
</VirtualHost>
```

Simpan dan keluar, lalu restart Apache:
```bash
sudo systemctl restart apache2
```

## 6. Pengujian HTTPS
Buka browser dan akses:
```
https://monitoring.kaldyta.com
```
Gunakan `curl` untuk mengecek respons server:
```bash
curl -I https://monitoring.kaldyta.com
```

## 7. Perpanjangan Otomatis SSL
Let's Encrypt memiliki masa berlaku 90 hari, pastikan perpanjangan otomatis dengan:
```bash
sudo certbot renew --dry-run
```
Jika tidak ada error, SSL akan diperbarui otomatis.

## 8. Kesimpulan
SSL telah berhasil dipasang pada monitoring.kaldyta.com dengan Apache2 sebagai Reverse Proxy. Website sekarang aman dengan HTTPS.

---

### Dokumentasi ini akan diunggah ke GitHub sebagai bagian dari portofolio.

