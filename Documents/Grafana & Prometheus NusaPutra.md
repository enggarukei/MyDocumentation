Baik! Saya akan membantu Anda membuat **Network Monitoring untuk MikroTik** menggunakan **Debian Server 12**, **Grafana**, dan **Prometheus**. Kita juga akan menggunakan **SNMP Exporter** agar **Prometheus** bisa mengambil data dari MikroTik.

---

## **🛠️ Langkah 1: Persiapan Server Debian**

1. **Update paket terlebih dahulu:**
    
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
    
2. **Install paket dasar:**
    
    ```bash
    sudo apt install -y curl wget nano unzip tar
    ```
    

---

## **📌 Langkah 2: Install Prometheus**

1. **Buat user Prometheus:**
    
    ```bash
    sudo useradd --no-create-home --shell /bin/false prometheus
    ```
    
2. **Buat direktori konfigurasi dan data Prometheus:**
    
    ```bash
    sudo mkdir /etc/prometheus /var/lib/prometheus
    ```
    
3. **Download Prometheus:**
    
    ```bash
    cd /tmp
    curl -LO https://github.com/prometheus/prometheus/releases/latest/download/prometheus-*-linux-amd64.tar.gz
    tar xvf prometheus-*-linux-amd64.tar.gz
    cd prometheus-*-linux-amd64
    ```
    
4. **Pindahkan file yang diperlukan:**
    
    ```bash
    sudo mv prometheus /usr/local/bin/
    sudo mv promtool /usr/local/bin/
    sudo mv consoles console_libraries /etc/prometheus/
    sudo mv prometheus.yml /etc/prometheus/
    ```
    
5. **Atur kepemilikan direktori:**
    
    ```bash
    sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
    ```
    

---

## **📌 Langkah 3: Konfigurasi Prometheus untuk MikroTik**

6. **Edit file konfigurasi Prometheus:**
    
    ```bash
    sudo nano /etc/prometheus/prometheus.yml
    ```
    
7. **Tambahkan konfigurasi berikut:**
    
    ```yaml
    global:
      scrape_interval: 15s
    
    scrape_configs:
      - job_name: 'mikrotik'
        static_configs:
          - targets: ['192.168.1.1:9436']  # Ganti dengan IP MikroTik dan port SNMP Exporter jika digunakan
    ```
    
8. **Simpan dengan** `Ctrl + X → Y → Enter`.

---

## **📌 Langkah 4: Buat Service untuk Prometheus**

9. **Buat file service Prometheus:**
    
    ```bash
    sudo nano /etc/systemd/system/prometheus.service
    ```
    
10. **Tambahkan konfigurasi berikut:**
    
    ```ini
    [Unit]
    Description=Prometheus Monitoring System
    Wants=network-online.target
    After=network-online.target
    
    [Service]
    User=prometheus
    Group=prometheus
    Type=simple
    ExecStart=/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus
    
    [Install]
    WantedBy=multi-user.target
    ```
    
11. **Simpan dan aktifkan service:**
    
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable --now prometheus
    ```
    

---

## **📌 Langkah 5: Install dan Konfigurasi SNMP Exporter untuk MikroTik**

12. **Unduh SNMP Exporter:**
    
    ```bash
    cd /tmp
    curl -LO https://github.com/prometheus/snmp_exporter/releases/latest/download/snmp_exporter-*-linux-amd64.tar.gz
    tar xvf snmp_exporter-*-linux-amd64.tar.gz
    cd snmp_exporter-*-linux-amd64
    ```
    
13. **Pindahkan file ke direktori sistem:**
    
    ```bash
    sudo mv snmp_exporter /usr/local/bin/
    ```
    
14. **Buat service SNMP Exporter:**
    
    ```bash
    sudo nano /etc/systemd/system/snmp_exporter.service
    ```
    
15. **Tambahkan konfigurasi berikut:**
    
    ```ini
    [Unit]
    Description=SNMP Exporter for Prometheus
    Wants=network-online.target
    After=network-online.target
    
    [Service]
    User=root
    Group=root
    Type=simple
    ExecStart=/usr/local/bin/snmp_exporter --config.file=/etc/prometheus/snmp.yml
    
    [Install]
    WantedBy=multi-user.target
    ```
    
16. **Aktifkan dan jalankan SNMP Exporter:**
    
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable --now snmp_exporter
    ```
    

---

## **📌 Langkah 6: Konfigurasi MikroTik untuk SNMP**

17. **Aktifkan SNMP di MikroTik:**
    
    ```shell
    /snmp set enabled=yes contact="Admin" location="Data Center"
    ```
    
18. **Pastikan MikroTik bisa dijangkau dengan SNMP dari server Debian:**
    
    ```bash
    snmpwalk -v2c -c public 192.168.1.1
    ```
    

---

## **📌 Langkah 7: Install dan Konfigurasi Grafana**

19. **Tambahkan repository Grafana:**
    
    ```bash
    sudo apt install -y software-properties-common
    sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
    ```
    
20. **Install Grafana:**
    
    ```bash
    sudo apt update
    sudo apt install -y grafana
    ```
    
21. **Aktifkan dan jalankan Grafana:**
    
    ```bash
    sudo systemctl enable --now grafana-server
    ```
    
22. **Cek status service:**
    
    ```bash
    sudo systemctl status grafana-server
    ```
    

---

## **📌 Langkah 8: Akses Prometheus dan Grafana**

23. **Buka browser dan akses Prometheus:**
    
    ```
    http://10.10.10.11:9090
    ```
    
24. **Buka browser dan akses Grafana:**
    
    ```
    http://10.10.10.11:3000
    ```
    
    - **Username:** `admin`
    - **Password:** `admin` (akan diminta untuk diubah setelah login pertama kali)

---

## **📌 Langkah 9: Tambahkan Prometheus sebagai Data Source di Grafana**

25. Masuk ke **Grafana** → **Settings** → **Data Sources**.
26. Pilih **Prometheus**.
27. Masukkan URL:
    
    ```
    http://10.10.10.11:9090
    ```
    
28. Klik **Save & Test**.

---

## **📌 Langkah 10: Tambahkan Dashboard untuk Monitoring MikroTik**

29. Masuk ke Grafana.
30. Pilih **Import Dashboard**.
31. Gunakan **Grafana Dashboard ID:** `12216` (MikroTik Traffic Overview).
32. Klik **Load** → Pilih **Prometheus sebagai Data Source** → Klik **Import**.

---

### **🎯 Kesimpulan**

Anda sekarang memiliki **Network Monitoring** untuk **MikroTik** menggunakan **Prometheus, Grafana, dan SNMP Exporter** di **Debian Server 12**! 🎉

Jika ada pertanyaan atau kendala, silakan tanyakan! 🚀


![Alt Text](img/Pasted\ image\ 20250216002849.png)
