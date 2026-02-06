---
title: Mengenal LXC dan Cara Membuat Container di Proxmox VE
date: 2026-02-05 15:30:00 +0700
categories: [DevOps]
tags: [proxmox]
---

Halo semuanya! Sebagai seseorang yang baru terjun ke dunia virtualisasi, saya sering bertanya-tanya "Kenapa setiap membuat server baru saya harus install OS secara utuh dan resource yang dipakai juga lebih besar?"

Nah, di artikel kali ini, saya ingin berbagi progres belajar saya tentang Proxmox VE, khususnya mengenai fitur yang sangat powerfull namun sering bikin pemula bingung yakni, LXC (Linux Containers).

## Apa itu LXC?

Sebelum masuk ke tutorial penting bagi kita untuk memahami terlebih dahulu mengenai LXC dan VM, serta kapan kita harus memilih dari kedua fitur ini sesuai dengan kebutuhan.

### Analogi Rumah vs Apartemen

- VM (Virtual Machine) itu seperti rumah. Kamu punya atap, pondasi, pipa air sendiri. Jadinya, sangat aman dan privat, tapi mahal dan memerlukan banyak lahan (CPU/RAM).
- LXC (Linux Containers) itu seperti apartemen. Kamu punya ruang sendiri, tapi kamu berbagi pondasi dan pipa air yang sama dengan penghuni lain (berbagi kernel linux milik Host Proxmox). Kelebihannya ini jauh lebih hemat dan efisien.

### Kenapa Pilih LXC?

- Sangat efisien: Container tidak menjalankan kernel sendiri. Jika VM memerlukan setidaknya ~512MB agar bisa berjalan normal, container setidaknya hanya perlu ~50MB.
- Boot Instan: LXC hanya memerlukan waktu sekitar 2-5 detik, sedangkan VM memerlukan waktu yang lebih lama sekitar 30-60 detik.
- Storage ringan: Karena LXC ringan dia hanya memerlukan alokasi storage sekitar 200MB-2GB berbeda dengan VM yang membutuhkan 8GB-20GB.
- Performa jaringan: Karena LXC berbagi resource dengan host maka mendapatkan kecepatan sama persis (native speed), sedangkan VM hanya sekitar 5-10% dari bandwith aslinya.

### Trade-off LXC

Dari sekian banyaknya keuntungan yang diberikan LXC pasti ada harga yang harus dibayar, yaitu:

- LXC hanya mendukung distro linux, jadi windows harus menggunakan VM.
- Fitur low-level seperti kernel dibatasi.
- Keamanan hanya pada process-level (semi-isolasi) sedangkan VM hingga hardware-level.

### Kapan pakai LXC?

- Aplikasi berbasis web. Misalnya Nginx, Apache, Node.js.
- Database server seperti MySQL, PostgreSQL, MongoDB.
- Service jaringan, contohnya DNS, DHCP, proxy server.
- Selain itu, LXC cocok untuk keperluan development yang memerlukan pembuatan environment cepat dan ringan untuk build/test yang sering dihapus.

## Langkah-Langkah Membuat Container di Proxmox

1. Masuk ke dashboard Proxmox melalui browser.
2. Pilih menu **storage local** dan klik **CT Templates**
   ![Dashboard Proxmox](/assets/img/20260205_membuat_container/20260205_dashboard_proxmox.png)
3. Kemudian klik tombol **Templates** dan cari jenis linux yang ingin diinstall. Kali ini saya ingin membuat server dengan Ubuntu versi 24.04. Karena belum punya maka kita download terlebih dahulu.
   ![Pilih template linux](/assets/img/20260205_membuat_container/20260205_templates.png)
4. Kita tunggu proses download hingga selesai.
   ![Proses download template](/assets/img/20260205_membuat_container/20260205_templates_download.png)
5. Pada tab status proses download sudah selesai dengan statusnya adalah **stopped:OK**.
   ![Download Complete](/assets/img/20260205_membuat_container/20260205_templates_download_complete.png)
6. Selanjutnya, kita membuat container baru dengan klik pada tombol **Create CT**.
   ![Buat Container](/assets/img/20260205_membuat_container/20260205_btn_create.png)
7. Lalu pada tab general isi parameter berikut.
   - Node : server fisik (host Proxmox)
   - CT ID : Nomor unik dari server kita nantinya
   - Hostname : Isikan nama untuk server
   - Password & Confirm Password : Password root server
     ![General Config](/assets/img/20260205_membuat_container/20260205_general.png)
8. Kemudian pada tab template pilih jenis server yang mau kita install. Disini saya pilih ubuntu 24.04.
   ![Select container template](/assets/img/20260205_membuat_container/20260205_select_templates.png)
9. Selanjutnya pada tab Disks alokasikan ukuran disk sesuai kebutuhan. Namun, kali ini saya biarkan default saja.
   ![Alokasi disks size](/assets/img/20260205_membuat_container/20260205_disks.png)
10. Pada tab CPU alokasikan jumlah core CPU sesuaikan saja dengan beban komputasi server kalian nantinya.
    ![Alokasi CPU](/assets/img/20260205_membuat_container/20260205_cpu.png)
11. Seperti sebelumnya pada alokasi **Memory**/RAM sesuaikan dengan kebutuhan. Karena LXC ringan saya cukup alokasikan 512MB.
    ![Alokasi Memory](/assets/img/20260205_membuat_container/20260205_memory.png)
12. Nah, pada tab **Network** kita tentukan alamat IP dari server sesuai topologi jaringan yang ada. Disini saya menggunakan IP statis 192.24.5.105 dengan subnet mask 255.255.255.0 yang mengarah ke gateway 192.24.5.1.
    ![Konfigurasi network](/assets/img/20260205_membuat_container/20260205_networks.png)
13. Lalu pada tab **DNS** bisa kalian isi dengan DNS google ataupun lainnya. Namun jika dikosongkan DNS akan mengambil dari host-nya yakni server proxmox.
    ![Konfigurasi DNS](/assets/img/20260205_membuat_container/20260205_dns.png)
14. Pada tab **Confirm** kita pastikan konfigurasinya sudah sesuai kebutuhan. Selanjutnya klik finish.
    ![Konfirmasi konfigurasi](/assets/img/20260205_membuat_container/20260205_confirm.png)
15. Klik **start** pada server yang barusan kita buat. Tunggu sebentar sekitar 5 detik dan server berhasil kita buat 🎉.
    ![Start server](/assets/img/20260205_membuat_container/20260205_start_server.png)

## Referensi

- [Linux Container](https://pve.proxmox.com/wiki/Linux_Container)
- [VM vs LXC in Proxmox: Complete Comparison Guide](https://www.propelrc.com/vm-vs-lxc-in-proxmox/)
