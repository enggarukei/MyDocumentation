# Dokumentasi DNS Resolver untuk Nusa Putra

## Pendahuluan
DNS Resolver adalah layanan yang menerjemahkan nama domain menjadi alamat IP agar komputer dapat berkomunikasi melalui jaringan. Dalam proyek ini, kita akan membuat **DNS Resolver untuk Nusa Putra** menggunakan **Unbound** di **VM Proxmox**, dengan **IP 10.10.10.12** dan melakukan **port forwarding ke IP publik 103.182.195.180** di **MikroTik**.

## Persiapan
### Perangkat yang Digunakan:
- **Proxmox** sebagai virtualisasi
- **VM Debian** untuk menjalankan Unbound
- **MikroTik** untuk melakukan port forwarding
- **IP Publik 103.182.xxx.xxx (Mengalokasikan 1 buah IP Public)**

### Perangkat Lunak yang Dibutuhkan:
- **Unbound** sebagai DNS Resolver
- **MikroTik RouterOS** untuk konfigurasi jaringan
- **Linux Debian** sebagai OS di VM Proxmox

## Instalasi & Konfigurasi
### 1. Instalasi Unbound di Debian
```bash
apt update && apt install unbound -y
```

### 2. Konfigurasi Unbound
Edit file konfigurasi utama:
```bash
nano /etc/unbound/unbound.conf
```
Tambahkan konfigurasi berikut:
```conf
server:
    interface: 0.0.0.0
    access-control: 0.0.0.0/0 allow
    verbosity: 1
    root-hints: "/etc/unbound/root.hints"
    forward-zone:
        name: "."
        forward-addr: 8.8.8.8
        forward-addr: 1.1.1.1
```
Simpan dan restart Unbound:
```bash
systemctl restart unbound
systemctl enable unbound
```

### 3. Port Forwarding di MikroTik
Tambahkan rule NAT agar DNS dapat diakses dari internet:
```bash
/ip firewall nat add chain=dstnat protocol=udp dst-port=53 action=dst-nat to-addresses=10.10.10.12 to-ports=53
/ip firewall nat add chain=dstnat protocol=tcp dst-port=53 action=dst-nat to-addresses=10.10.10.12 to-ports=53
```

### 4. Testing DNS Resolver
Jalankan perintah berikut untuk memastikan resolver berfungsi:
```bash
dig google.com @10.10.10.12
```
Jika berhasil, akan muncul hasil seperti berikut:
```
;; Query time: 12 msec
;; SERVER: 10.10.10.12#53(10.10.10.12)
```

## Pemecahan Masalah
- **DNS tidak merespons?**
  - Cek status Unbound: `systemctl status unbound`
  - Cek firewall di MikroTik: `ip firewall filter print`

- **Lambat dalam resolve?**
  - Pastikan **forward-addr** mengarah ke server DNS yang cepat seperti **8.8.8.8** dan **1.1.1.1**.

## Kesimpulan
Dengan konfigurasi ini, kita berhasil membangun DNS Resolver untuk Nusa Putra yang memungkinkan query DNS diteruskan melalui **Unbound** di **Proxmox** dengan **port forwarding di MikroTik**. Ini membantu meningkatkan kecepatan dan kontrol atas layanan DNS di jaringan kampus.

---
_Dokumentasi oleh: Enggarukei

