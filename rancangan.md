# Rancangan Simulasi Jaringan Pabrik Elektronik

Dokumen ini berisi rancangan topologi dan infrastruktur jaringan untuk simulasi pabrik elektronik menggunakan Cisco Packet Tracer. Jaringan dibagi menjadi 5 zona utama berdasarkan fungsinya.

## 1. Zona Produksi (Lantai Pabrik) 🏭
*   **PC / Laptop**: Digunakan untuk komputer operator pengendali mesin cetak atau mesin potong.
*   **Generic IP End Device (IoT)**: Digunakan sebagai pengganti sensor suhu atau sensor mesin otomatis di Packet Tracer.
*   **Switch (2960)**: Berfungsi untuk menghubungkan semua komputer operator dan perangkat IoT di area pabrik.

## 2. Zona Gudang (Logistik) 📦
*   **Wireless Access Point (AP-PT)**: Memancarkan sinyal Wi-Fi di area gudang yang luas untuk menjangkau perangkat portabel.
*   **Smart Device / Tablet**: Digunakan sebagai pengganti alat pemindai kode batang (*barcode scanner*) nirkabel.
*   **PC**: Digunakan oleh admin gudang guna mencatat stok barang masuk dan keluar.

## 3. Zona Manajemen & Desain 💻
*   **PC / Laptop Spesifikasi Tinggi**: Digunakan oleh tim desainer produk dan tim keuangan.
*   **Network Printer**: Printer bersama yang bisa diakses oleh seluruh staf kantor melalui jaringan.
*   **Switch (2960)**: Menghubungkan seluruh komputer staf dan printer kantor di area ini.

## 4. Zona Server (Ruang Kontrol) 🗄️
*   **Server (Generic)**: Bertindak sebagai:
    *   **Server Data**: Menyimpan desain produk dan database operasional pabrik.
    *   **Server DHCP**: Bertugas sebagai pembagi alamat IP otomatis untuk perangkat *client* di berbagai zona.
*   **Switch Layer 3 (3560)**: Bertindak sebagai sakelar utama (*Core Switch*) untuk menghubungkan Switch dari zona-zona lain, serta mengatur komunikasi (*routing*) antar zona.

## 5. Zona Eksternal (Edge Network / WAN) 🌐
*   **Edge Router (Misal Cisco 1941/4331)**: Bertindak sebagai gerbang (Gateway) yang menghubungkan seluruh jaringan lokal pabrik ke dunia luar (Internet/Cloud). Bertugas mengatur *Network Address Translation* (NAT) dan memberikan akses internet secara aman.
*   **Cloud (PT-Cloud)**: Representasi dari *Internet Service Provider* (ISP) untuk simulasi koneksi jaringan publik.

---

## Saran & Rekomendasi Tambahan 💡

Untuk membuat simulasi jaringan ini lebih realistis, berstandar industri, aman, dan efisien, berikut adalah beberapa rekomendasi yang bisa Anda terapkan pada topologi:

1.  **Penerapan VLAN (Virtual LAN)**
    Pisahkan keempat zona tersebut ke dalam VLAN yang berbeda pada Switch (misalnya: VLAN 10 untuk Produksi, VLAN 20 untuk Gudang, VLAN 30 untuk Manajemen, VLAN 40 untuk Server). Hal ini sangat penting untuk mencegah terjadinya *broadcast storm*, meningkatkan keamanan, dan membatasi akses antar zona (misal: pekerja gudang tidak bisa mengakses PC Desain).
2.  **Konfigurasi Inter-VLAN Routing**
    Karena VLAN memisahkan jaringan secara logis, Anda perlu mengaktifkan fitur *Inter-VLAN Routing* pada **Switch Layer 3 (3560)** agar perangkat antar VLAN (misal: PC Manajemen) tetap bisa mengakses Server Data di VLAN lain, namun dengan aturan tertentu.
3.  **Manajemen IP Addressing (Statis vs Dinamis)**
    Meskipun Server akan dikonfigurasi sebagai DHCP, **jangan memberikan IP dinamis (DHCP) ke perangkat infrastruktur dan server**. Pastikan **Server**, **Network Printer**, **Access Point**, dan antarmuka manajemen **Switch** menggunakan **IP Address Statis**. IP Dinamis hanya diberikan kepada PC, Laptop, dan Smart Device. Pastikan juga membuat *DHCP Pool* yang berbeda-beda untuk tiap VLAN.
4.  **IoT Registration Server / Gateway Terpusat**
    Untuk mengelola Generic IP End Device (IoT) di Zona Produksi secara terpusat, aktifkan layanan *IoT Service* pada Server Generic. Dengan begitu, simulasi sensor mesin dan suhu bisa dipantau langsung layaknya sistem SCADA melalui *web browser* dari PC Ruang Kontrol atau PC Manajemen.
5.  **Keamanan Wireless (WLAN Security)**
    Pada Wireless Access Point (AP-PT) di area Gudang, jangan biarkan *open authentication*. Terapkan keamanan jaringan nirkabel standar seperti enkripsi **WPA2-PSK** untuk mencegah pihak tidak sah (penyusup) terhubung ke dalam sistem logistik internal pabrik.
6.  **Konfigurasi NAT pada Edge Router**
    Karena Router Tepi sudah ditambahkan ke dalam rancangan utama, pastikan Anda mengonfigurasi NAT (*Network Address Translation*) pada router tersebut agar semua perangkat di dalam VLAN pabrik (yang ber-IP privat) bisa mengakses Internet menggunakan alamat IP publik.
7.  **Redundansi Jaringan Sederhana**
    Mengingat ini adalah lingkungan pabrik yang tidak boleh putus jaringannya, Anda bisa menambahkan jalur kabel ganda (*redundant links*) dari Switch Distribusi (2960) ke Switch Core (3560) dengan mengaktifkan *EtherChannel* atau membiarkan *Spanning Tree Protocol (STP)* bekerja. Hal ini memastikan pabrik tetap beroperasi meski salah satu kabel fisik terputus.
