# iPaymu Payment Gateway untuk PHPNuxBill

Plugin gateway mandiri untuk menerima pembayaran melalui halaman pembayaran iPaymu. Paket ini tidak mengganti file core PHPNuxBill.

## Fitur

- Redirect Payment API v2 iPaymu.
- Mode Sandbox dan Production.
- Signature API SHA-256/HMAC-SHA256.
- Verifikasi `X-Signature` callback iPaymu.
- Validasi nominal pembayaran sebelum aktivasi paket.
- Proses callback idempotent agar callback berulang tidak mengaktifkan paket dua kali.
- Pengecekan status manual dari halaman transaksi PHPNuxBill.
- Pengaturan penanggung biaya transaksi dan masa berlaku pembayaran.

## Persyaratan

- PHPNuxBill yang mendukung folder `system/paymentgateway` dan `Billing::finalizeGateway()`.
- PHP dengan ekstensi cURL, JSON, dan hash.
- Website HTTPS dengan URL callback yang dapat diakses internet.
- Akun merchant iPaymu beserta nomor VA dan API Key.

## Instalasi

1. Ekstrak ZIP ke direktori utama PHPNuxBill. Struktur folder di ZIP sudah sesuai.
2. Pastikan kedua file berikut tersedia:
   - `system/paymentgateway/ipaymu.php`
   - `system/paymentgateway/ui/ipaymu.tpl`
3. Masuk ke Admin PHPNuxBill, buka **Payment Gateway > iPaymu**.
4. Pilih **Sandbox**, lalu isi nomor VA dan API Key sandbox.
5. Simpan pengaturan. URL callback ditampilkan pada halaman konfigurasi dan otomatis dikirim untuk setiap transaksi.
6. Lakukan satu transaksi pengujian sampai paket benar-benar aktif.
7. Setelah pengujian berhasil, ganti ke **Production** dan masukkan kredensial production.

Tidak diperlukan perubahan database atau cron tambahan.

## Mendaftar iPaymu

Gunakan tombol **Daftar iPaymu** pada halaman konfigurasi, atau buka:

https://my.ipaymu.com/register/ref/ardian08221

## Keamanan

- Jangan membagikan VA dan API Key di forum, tangkapan layar, log, atau ZIP.
- Callback hanya memproses pembayaran dengan signature valid dan nominal yang sama dengan transaksi lokal.
- Aktivasi memakai `Billing::finalizeGateway()` agar transaksi callback yang dikirim ulang tidak menggandakan aktivasi.
- Mulai dari Sandbox dan buat cadangan database sebelum memasang plugin di server produksi.

## Alur Pembayaran

1. Pelanggan memilih iPaymu pada checkout PHPNuxBill.
2. Plugin membuat transaksi Redirect Payment API v2.
3. Pelanggan diarahkan ke halaman pembayaran iPaymu.
4. iPaymu mengirim callback ke `/callback/ipaymu`.
5. Plugin memverifikasi signature, referensi, status, dan nominal.
6. Setelah valid, paket pelanggan diaktifkan satu kali.

## Pemecahan Masalah

- **iPaymu tidak muncul:** pastikan nama file tepat `system/paymentgateway/ipaymu.php`.
- **Gagal membuat transaksi:** periksa Mode, VA, API Key, ekstensi cURL, waktu server, dan log PHPNuxBill.
- **Pembayaran sukses tetapi paket belum aktif:** pastikan URL `/callback/ipaymu` bisa diakses dari internet, HTTPS valid, dan tidak diblokir firewall/Cloudflare.
- **Signature callback tidak valid:** jangan mencampur kredensial Sandbox dan Production; pastikan VA yang tersimpan sesuai environment.
- **Tombol cek status gagal:** callback tetap menjadi jalur utama. Periksa ID transaksi dan respons API pada data transaksi gateway.

## Dokumentasi Resmi

- Dokumentasi iPaymu: https://docs.ipaymu.com/id/docs
- Registrasi merchant: https://my.ipaymu.com/register/ref/ardian08221

Versi plugin: 1.0.1
