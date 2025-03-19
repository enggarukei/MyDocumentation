### **Dokumentasi Instalasi Monitoring Jaringan di Nusa Putra**

**Tanggal & Waktu**: 12:42 AM  
**Lokasi**: Nusa Putra (Installing Remote)
**IP Publik Digunakan**: Satu IP publik yang tidak terpakai  
**Tools yang Digunakan**: Prometheus & Grafana

---

### **1. Latar Belakang**

Sebagai langkah dalam meningkatkan pengawasan dan optimasi jaringan di Nusa Putra, sistem monitoring telah diimplementasikan menggunakan **Prometheus** sebagai sistem pengumpulan metrik dan **Grafana** untuk visualisasi data. Instalasi ini dilakukan pada **Proxmox Container** dengan IP internal **10.xx.xx.xx**.

---

### **2. Infrastruktur & Arsitektur**

- **Proxmox Server** (IP: xx.xx.xx.xx)
- **Container Debian** (IP: xx.xx.xx.xx)
- **MikroTik Router** sebagai sumber data trafik
- **IP Publik yang tidak digunakan** untuk akses eksternal

---

### **3. Instalasi & Konfigurasi**

#### **a. Instalasi Prometheus**

1. Update sistem:
    
    ```bash
    apt update && apt upgrade -y
    ```
    
2. Unduh dan ekstrak Prometheus:
    
    ```bash
    wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-linux-amd64.tar.gz
    tar -xvzf prometheus-linux-amd64.tar.gz
    cd prometheus-linux-amd64
    ```
    
3. Jalankan Prometheus:
    
    ```bash
    ./prometheus --config.file=prometheus.yml
    ```
    

#### **b. Instalasi Grafana**

4. Tambahkan repository Grafana:
    
    ```bash
    wget -q -O - https://packages.grafana.com/gpg.key | apt-key add -
    echo "deb https://packages.grafana.com/oss/deb stable main" | tee -a /etc/apt/sources.list.d/grafana.list
    apt update
    ```
    
5. Instal Grafana:
    
    ```bash
    apt install grafana -y
    ```
    
6. Jalankan dan enable service:
    
    ```bash
    systemctl start grafana-server
    systemctl enable grafana-server
    ```
    

#### **c. Konfigurasi Prometheus untuk MikroTik**

7. Tambahkan **MikroTik** sebagai target di `prometheus.yml`:
    
    ```yaml
    scrape_configs:
      - job_name: 'mikrotik'
        static_configs:
          - targets: ['192.168.1.1:9100'] # Diganti dengan IP MikroTik
    ```
    
8. Restart Prometheus:
    
    ```bash
    systemctl restart prometheus
    ```
    

#### **d. Konfigurasi Grafana untuk Visualisasi Data**

9. Akses **Grafana** melalui browser:
    
    ```
    http://<IP_Public>:3000
    ```
    
10. Login dengan default:
    
    ```
    Username: admin  
    Password: admin  
    ```
    
11. Tambahkan **Prometheus** sebagai data source.
12. Buat dashboard baru untuk memonitor **trafik jaringan MikroTik**.

---

### **4. Hasil Implementasi**

✅ **Prometheus berhasil mengumpulkan data jaringan dari MikroTik**  
✅ **Grafana berhasil menampilkan grafik dan metrik real-time**  
✅ **Sistem dapat diakses melalui IP publik untuk pemantauan jarak jauh**

---

![Realtime](/img/Grafana.png)

atau bisa diakses pada link:
https://monitoring.kaldyta.com

--- 

### **5. Kesimpulan & Langkah Selanjutnya**

- **Sistem monitoring berjalan dengan baik**, memungkinkan pemantauan trafik secara **real-time** dan **historis**.
- **Langkah selanjutnya** adalah mengoptimalkan alerting menggunakan **Grafana Alerts** atau **Prometheus Alertmanager** agar tim IT dapat menerima notifikasi jika ada anomali jaringan.

---

📌 **Dokumentasi ini dibuat untuk keperluan monitoring jaringan di Nusa Putra**. Jika ada pertanyaan atau perlu pembaruan, silakan hubungi tim IT. 🚀