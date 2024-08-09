# simple-script-mikrotik
Script Mikrotik Sederhana Jenis RB750Gr3

### Analisis Script MikroTik

**Umum**
Script ini digunakan untuk mengonfigurasi router MikroTik model RB750Gr3 dengan RouterOS versi 6.49.11. Tujuan utama dari script ini adalah mengelola koneksi internet, memberikan akses jaringan lokal, serta mengatur pembatasan bandwidth berdasarkan MAC address yang terdaftar.

**Rincian Konfigurasi**

1. **Bridge dan Interface Ethernet:**
   - **Bridge Creation:** Sebuah bridge bernama `bridge1` dibuat untuk menghubungkan beberapa interface Ethernet ke jaringan lokal.
   - **Penamaan Interface:** Interface Ethernet diberi nama sebagai berikut:
     - `ether1` menjadi `ether1_INTERNET` (koneksi ke internet).
     - `ether2`, `ether3`, `ether4`, dan `ether5` dinamai `ether2_LOCAL1`, `ether3_LOCAL2`, `ether4_LOCAL3`, dan `ether5_LOCAL4` (untuk jaringan lokal).
   - **Bridge Port:** Interface lokal (`ether2_LOCAL1` hingga `ether5_LOCAL4`) dihubungkan ke `bridge1`.

2. **DHCP Server:**
   - **DHCP Pool:** Pool DHCP bernama `dhcp_pool0` dengan rentang IP `192.168.88.10-192.168.88.254` dibuat untuk mendistribusikan alamat IP kepada perangkat di jaringan lokal.
   - **DHCP Server:** DHCP server diaktifkan pada `bridge1` menggunakan pool `dhcp_pool0`.
   - **Alamat IP Bridge:** `bridge1` diberikan alamat IP `192.168.88.1/24`, yang juga berfungsi sebagai gateway bagi jaringan lokal.
   - **DHCP Network:** Jaringan DHCP dikonfigurasi dengan gateway `192.168.88.1` dan DNS server `8.8.8.8` serta `8.8.4.4`.

3. **DHCP Client untuk Internet:**
   - Interface `ether1_INTERNET` dikonfigurasi sebagai DHCP client untuk menerima alamat IP dari modem atau router yang terhubung ke internet.

4. **Firewall Mangle:**
   - **Menandai Koneksi:** Koneksi yang datang dari interface `bridge1` (jaringan lokal) ditandai berdasarkan MAC address:
     - **`unregistered`:** Koneksi dari perangkat yang tidak terdaftar dengan MAC address tertentu akan diberi tanda `unregistered`.
     - **`registered`:** Koneksi dari perangkat yang MAC address-nya terdaftar akan diberi tanda `registered`.
   - **Menandai Paket:** Berdasarkan tanda koneksi yang telah dibuat, paket juga ditandai dengan label `unregistered` atau `registered`.

5. **Queue Simple:**
   - **`unregistered_users`:** Queue simple ini membatasi bandwidth menjadi `1M/1M` untuk perangkat yang tidak terdaftar.
   - **`registered_users`:** Queue simple ini memberikan bandwidth yang lebih besar, yaitu `10M/10M` untuk perangkat yang terdaftar.

6. **Firewall NAT:**
   - **Masquerade:** Aturan NAT `masquerade` digunakan untuk meneruskan semua traffic dari jaringan lokal ke internet melalui `ether1_INTERNET`.

7. **DNS Server:**
   - **Pengaturan DNS:** DNS server dikonfigurasi untuk menggunakan Google DNS (`8.8.8.8` dan `8.8.4.4`), memastikan bahwa perangkat di jaringan lokal dapat melakukan resolusi DNS.

8. **System Identity:**
   - **Nama Sistem:** Nama sistem diatur menjadi `RB750Gr3-Configured`.

9. **Wireless Interface:**
   - **Wireless Disabled:** Jika ada interface wireless (meskipun model RB750Gr3 tidak memiliki interface wireless), fungsi ini akan dinonaktifkan.

### Kesimpulan

- **Jaringan Lokal:** Semua port LAN (`ether2` hingga `ether5`) terhubung ke `bridge1`, memungkinkan perangkat yang terhubung ke port tersebut untuk mendapatkan IP dari DHCP server dan terhubung ke jaringan.
- **Akses Internet:** DHCP client pada `ether1_INTERNET` memastikan bahwa router mendapatkan IP dari modem, dan aturan NAT `masquerade` memungkinkan perangkat di jaringan lokal mengakses internet.
- **Pembatasan Bandwidth:** Bandwidth untuk perangkat diatur berdasarkan MAC address; perangkat yang tidak terdaftar diberikan bandwidth rendah (`1M/1M`), sementara perangkat yang terdaftar diberikan bandwidth lebih tinggi (`10M/10M`).
- **DNS dan Gateway:** Jaringan lokal menggunakan DNS server Google dan gateway `192.168.88.1` untuk mengakses internet.

Script ini sangat cocok untuk lingkungan jaringan kecil di mana kontrol akses dan pembatasan bandwidth diperlukan. Semua perangkat di jaringan lokal akan memiliki akses ke internet dengan konfigurasi ini, dan kontrol tambahan diberikan berdasarkan MAC address.
