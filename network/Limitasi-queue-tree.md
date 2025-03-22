# Dokumentasi Implementasi Queue Tree Menggunakan Mangle di MikroTik

## 1. Pendahuluan
Queue Tree digunakan untuk melakukan manajemen bandwidth dengan cara mengalokasikan dan membatasi penggunaan bandwidth berdasarkan aturan tertentu. Dalam implementasi ini, kita menggunakan **Mangle** untuk menandai paket (packet marking) sebelum menerapkannya ke **Queue Tree**.

## 2. Konfigurasi Mangle
Mangle digunakan untuk menandai paket berdasarkan sumber, tujuan, atau jenis trafik. Berikut adalah langkah-langkah implementasi Mangle:

### a. Menandai Paket untuk Koneksi Zoom
1. Buka WinBox dan navigasi ke **IP > Firewall > Mangle**.
2. Tambahkan aturan baru untuk menandai koneksi Zoom dengan detail berikut:
   - **Chain**: prerouting
   - **Protocol**: TCP & UDP
   - **Dst. Port**: 3478, 3479, 3480 (port Zoom)
   - **Action**: mark connection
   - **New Connection Mark**: `Zoom-Conn`
   - **Passthrough**: Yes
3. Tambahkan aturan baru untuk menandai paket Zoom:
   - **Chain**: prerouting
   - **Connection Mark**: `Zoom-Conn`
   - **Action**: mark packet
   - **New Packet Mark**: `Zoom-Paket`
   - **Passthrough**: No

### b. Menandai Paket untuk VLAN Mahasiswa
1. Tambahkan aturan baru untuk menandai koneksi VLAN Mahasiswa:
   - **Chain**: forward
   - **Src. Address**: `172.16.0.0/19`
   - **Action**: mark connection
   - **New Connection Mark**: `Queue-Mahasiswa`
   - **Passthrough**: Yes
2. Tambahkan aturan baru untuk menandai paket VLAN Mahasiswa:
   - **Chain**: forward
   - **Connection Mark**: `Queue-Mahasiswa`
   - **Action**: mark packet
   - **New Packet Mark**: `queue-mhs`
   - **Passthrough**: No

### c. Menandai Paket untuk VLAN Dosen
1. Tambahkan aturan baru untuk menandai koneksi VLAN Dosen:
   - **Chain**: forward
   - **Src. Address**: `132.156.0.0/23`
   - **Action**: mark connection
   - **New Connection Mark**: `VLAN-Dosen-Conn`
   - **Passthrough**: Yes
2. Tambahkan aturan baru untuk menandai paket VLAN Dosen:
   - **Chain**: forward
   - **Connection Mark**: `VLAN-Dosen-Conn`
   - **Action**: mark packet
   - **New Packet Mark**: `VLAN-Dosen-Pkt`
   - **Passthrough**: No

### d. Menandai Paket untuk VLAN Gd-A
1. Tambahkan aturan baru untuk menandai koneksi VLAN Gd-A:
   - **Chain**: forward
   - **Src. Address**: `192.168.0.0/22`
   - **Action**: mark connection
   - **New Connection Mark**: `VLAN-Gd-A-Conn`
   - **Passthrough**: Yes
2. Tambahkan aturan baru untuk menandai paket VLAN Gd-A:
   - **Chain**: forward
   - **Connection Mark**: `VLAN-Gd-A-Conn`
   - **Action**: mark packet
   - **New Packet Mark**: `VLAN-Gd-A-Pkt`
   - **Passthrough**: No

## 3. Konfigurasi Queue Tree
Setelah paket ditandai menggunakan **Mangle**, langkah selanjutnya adalah membuat **Queue Tree** untuk membatasi bandwidth.

1. Buka **Queues > Queue Tree**
2. Tambahkan aturan **Priority Zoom**:
   - **Name**: Zoom-Download
   - **Parent**: Global-Download
   - **Packet Mark**: `Zoom-Paket`
   - **Limit At**: `500M`
   - **Max Limit**: Sesuai kebutuhan

3. Tambahkan aturan **Limitation VLAN Mahasiswa**:
   - **Name**: Queue-mahasiswa
   - **Parent**: global
   - **Packet Mark**: `queue-mhs`
   - **Limit At**: `15M`
   - **Max Limit**: `20M`

4. Tambahkan aturan **Limitation VLAN Dosen**:
   - **Name**: VLAN-Dosen-Limit
   - **Parent**: global
   - **Packet Mark**: `VLAN-Dosen-Pkt`
   - **Limit At**: `20M`
   - **Max Limit**: `30M`

5. Tambahkan aturan **Limitation VLAN Gd-A**:
   - **Name**: VLAN-Gd-A
   - **Parent**: global
   - **Packet Mark**: `VLAN-Gd-A-Pkt`
   - **Limit At**: `15M`
   - **Max Limit**: `20M`

## 4. Kesimpulan
Implementasi **Queue Tree menggunakan Mangle** memungkinkan pengaturan bandwidth yang lebih spesifik berdasarkan jenis trafik dan sumber trafik. Dengan metode ini, kita dapat mengoptimalkan penggunaan bandwidth, memastikan prioritas trafik penting, serta membatasi penggunaan bandwidth untuk kelompok tertentu agar jaringan tetap stabil.

## 5. Lampiran
Berikut adalah tangkapan layar konfigurasi:
- **Konfigurasi Mangle** 
<img src="img/network/Mangle.png" alt="Tampilan Mangle" width="600">

---

- **Konfigurasi Queue Tree** 
<img src="img/network/queue-tree.png" alt="Tampilan Queue Tree" width="600">

---

Dokumentasi ini dapat digunakan sebagai referensi untuk mengimplementasikan **QoS (Quality of Service) di MikroTik** sesuai kebutuhan jaringan kampus atau perusahaan.

