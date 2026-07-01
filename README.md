# UAS Jaringan Nirkabel & Administrasi Jaringan

## Informasi Kelompok
- Nama Kelompok : ........................................
- Anggota Kelompok :
  1. ........................................
  2. ........................................
  3. ........................................

## Mata Kuliah
UAS Jaringan Nirkabel (Topik Khusus: Wireless P2P Underlay, SD-WAN Overlay, & Cloudflare Tunnel)

## Ketentuan Pengerjaan
- Jenis Pengerjaan : Kelompok
- Tenggat Waktu : H-1 Sebelum Jadwal UAS

--------------------------------------------------

# Konfigurasi Jaringan (P2P Wireless, SD-WAN, & Tunneling)

## 1. Topologi Jaringan
Koneksi dilakukan secara bertingkat berdasarkan diagram topologi berikut:
- **Jaringan Lokal (Underlay)**: Menggunakan Wireless Access Point (Pemancar Sinyal) yang menghubungkan Client A dan Client B secara nirkabel.
- **Jaringan Virtual (Overlay)**: Menggunakan Software-Defined WAN (ZeroTier) secara privat untuk menghubungkan Client A dan Client B.
- **Secure Tunneling**: Menggunakan Cloudflare Tunnel (cloudflared) di Client A untuk mengekspos layanan HTTP Server lokal ke Internet.

Topologi Detail:
```
               [ Internet (Cloud) ]
                 ^              ^
                 |              |
           (Koneksi AP)   (Cloudflare Tunnel)
                 |              |
[ Wireless AP (172.10.18.1) ]   |
      )))                  (((  |
       ~                    ~   |
[ Wireless Client A ]    [ Wireless Client B ]
- IP Local: 172.10.18.2  - IP Local: 172.10.18.3
- HTTP Server running    - ZeroTier: 10.16.xx.2
- ZeroTier: 10.16.xx.1   - (xx bebas)
- Cloudflare Tunnel      
       |                        |
       +--- [ ZeroTier Private ]+
```

Nilai:
- Berhasil (Semua koneksi terhubung dan terkonfigurasi) : 30 Point
- Gagal : 0 Point

--------------------------------------------------

# Konfigurasi Wireless & IP Lokal (Underlay)

## 2. SSID & Keamanan Nirkabel (Access Point)
- **SSID**: `[NamaKelompok]_UAS` (Contoh: `KelompokA_UAS`)
- **SSID Mode / Broadcast**: **Hidden / OFF / Disable** (SSID disembunyikan, client harus terhubung secara manual).
- **Password SSID**: `UASudb2026!#`
- **Security Mode**: WPA2-PSK atau WPA3
- **IP Address AP**: `172.10.18.1/24`
- **DHCP**: OFF (Static IP)

Ketentuan Penilaian:
- Sesuai ketentuan : 10 Point
- Salah nama / salah password / broadcast ON / salah tipe keamanan / DHCP On : Potong 50%
  (Nilai menjadi 5 Point)

--------------------------------------------------

## 3. Konfigurasi IP Local Client A & Client B
Alokasi alamat IP lokal untuk perangkat nirkabel yang terhubung ke AP:
- **Subnet Mask**: `255.255.255.0` (atau prefix `/24`)
- **Wireless Client A**:
  - IP Address Local: `172.10.18.2`
- **Wireless Client B**:
  - IP Address Local: `172.10.18.3`

Ketentuan Penilaian:
- IP Local sesuai ketentuan : 10 Point
- Salah IP / salah network range potong 50% : 5 Point

--------------------------------------------------

# Konfigurasi ZeroTier (Virtual Overlay)

## 4. Nama Network & Otorisasi ZeroTier
- **Nama Network**: `[NamaKelompok]_UAS_ZT` (Contoh: `KelompokA_UAS_ZT`)
- **Access Control**: **PRIVATE** (Bukan Public).
- **Otorisasi**: Client A dan Client B wajib di-authorize secara manual melalui dashboard ZeroTier Central setelah join agar dapat berkomunikasi.

Ketentuan Penilaian:
- Sesuai ketentuan (Private + Authorized) : 10 Point
- Status Public / Tanpa otorisasi manual : Potong 50%
  (Nilai menjadi 5 Point)

--------------------------------------------------

## 5. Konfigurasi IP Address ZeroTier
Alokasi alamat IP virtual (Overlay) di dashboard ZeroTier Central:
- **Network Range (Managed IP)**: `10.16.xx.0/24`
  *(Catatan: Nilai `xx` bebas ditentukan oleh kelompok, misal nomor kelompok atau dua digit NIM terakhir anggota).*
- **Wireless Client A**:
  - IP Address ZeroTier: `10.16.xx.1` (Wajib berakhiran `.1`)
- **Wireless Client B**:
  - IP Address ZeroTier: `10.16.xx.2` (Wajib berakhiran `.2`)

Ketentuan Penilaian:
- IP ZeroTier sesuai ketentuan (menggunakan format `10.16.xx.1` untuk Client A dan `10.16.xx.2` untuk Client B) : 10 Point
- Salah konfigurasi range IP potong 50%: 5 Point

--------------------------------------------------

# Konfigurasi Cloudflare Tunnel (cloudflared)

## 6. Konfigurasi Tunnel & Publikasi Layanan
- **Lokasi Instalasi**: Dijalankan di **Wireless Client A**.
- **Target Service**: Mengarahkan tunnel ke local port web server / HTTP Server yang berjalan di Client A (pilihan port: 80, 8080, atau 8000).
- **Publikasi**: Menggunakan Cloudflare Tunnel (Named Tunnel dengan domain atau Quick Tunnel dengan subdomain `*.trycloudflare.com`) untuk mengekspos web server tersebut ke Internet.

Ketentuan Penilaian:
- Sesuai ketentuan (Tunnel aktif & mengarah ke port HTTP Client A) : 10 Point
- Salah konfigurasi / layanan tidak terarah ke port yang benar : Potong 50%
  (Nilai menjadi 5 Point)
- Tidak berhasil : 0 Point

--------------------------------------------------

# Langkah Pengerjaan

## Langkah 1: Konfigurasi Jaringan Lokal Nirkabel (Underlay)
1. Login ke perangkat Access Point.
2. Konfigurasikan SSID sesuai ketentuan (`[NamaKelompok]_UAS`), aktifkan mode **Hidden SSID (Broadcast OFF)**, dan set keamanan WPA2PSK/WPA3 dengan password `UASudb2026!#`.
3. Set IP Address AP ke `172.10.18.1/24`.
4. Set DHCP Off
5. Pada **Wireless Client A**, sambungkan ke wifi secara manual (karena Hidden SSID) dan set IP lokal ke `172.10.18.2/24`.
6. Pada **Wireless Client B**, lakukan hal yang sama dan set IP lokal ke `172.10.18.3/24`.

## Langkah 2: Konfigurasi Jaringan Privat (ZeroTier Overlay)
1. Login ke dashboard [ZeroTier Central](https://my.zerotier.com/).
2. Buat Network baru bernama `[NamaKelompok]_UAS_ZT` dan pastikan Access Control diset ke **Private**.
3. Konfigurasikan IPv4 Auto-Assign Range dengan IP `10.16.xx.0/24` (tentukan nilai `xx` bebas).
4. Install ZeroTier One client pada Client A dan Client B, lalu lakukan join ke Network ID tersebut.
5. Masuk ke dashboard ZeroTier Central, lakukan otorisasi (centang *Auth*) untuk kedua client.
6. Set managed IP Address secara manual/statis di dashboard (Client A: `10.16.xx.1`, Client B: `10.16.xx.2`).

## Langkah 3: Konfigurasi HTTP Server & Cloudflare Tunnel
1. Jalankan layanan HTTP Server lokal di **Wireless Client A** (misal: Nginx, Apache, Node.js, atau python `python3 -m http.server 8080`).
2. Unduh dan install `cloudflared` di **Wireless Client A**.
3. Jalankan Cloudflare Tunnel untuk mengekspos HTTP Server Client A ke Internet (bisa menggunakan Named Tunnel atau Quick Tunnel `cloudflared tunnel --url http://localhost:[PORT_ANDA]`).
4. Dapatkan domain/URL publik yang dihasilkan.

--------------------------------------------------

# Pengujian Koneksi & Layanan (Wajib Didokumentasikan)

## 1. Pengujian Ping Jaringan Lokal (Underlay Wireless)
Buktikan konektivitas jaringan nirkabel lokal secara menyeluruh dengan melakukan pengujian ping berikut:
- **Ping dari Wireless AP ke Client**:
  - Dari AP (`172.10.18.1`) ke Client A (`172.10.18.2`)
  - Dari AP (`172.10.18.1`) ke Client B (`172.10.18.3`)
  *(Gunakan menu ping utility pada dashboard admin AP).*
- **Ping dari Client ke Wireless AP**:
  - Dari Client A (`172.10.18.2`) ke AP (`172.10.18.1`)
  - Dari Client B (`172.10.18.3`) ke AP (`172.10.18.1`)
- **Ping antar Client via IP Lokal**:
  - Dari Client A (`172.10.18.2`) ke Client B (`172.10.18.3`)
  - Dari Client B (`172.10.18.3`) ke Client A (`172.10.18.2`)
*Syarat Lulus: Semua status ping harus REPLY.*

## 2. Pengujian Ping Jaringan Virtual (ZeroTier Overlay)
Buktikan konektivitas private virtual network di atas jaringan wireless lokal:
- **Ping antar Client via IP ZeroTier**:
  - Dari Client A (`10.16.xx.1`) ke Client B (`10.16.xx.2`)
  - Dari Client B (`10.16.xx.2`) ke Client A (`10.16.xx.1`)
*Syarat Lulus: Status ping harus REPLY.*

## 3. Pengujian Layanan Publik (Cloudflare Tunnel)
Akses URL publik yang dihasilkan oleh Cloudflare Tunnel (misal: `*.trycloudflare.com` atau domain kustom) menggunakan perangkat yang berada di luar jaringan (misal: smartphone menggunakan koneksi data seluler/selain wifi lokal). Pastikan layanan HTTP Server dari Client A dapat dibuka dengan sempurna.

--------------------------------------------------

# Dokumentasi Laporan (Screenshot Wajib)

Laporan dalam format PDF wajib melampirkan bukti screenshot konfigurasi utuh dan lengkap sebagai berikut:

### A. Screenshot Konfigurasi Jaringan Lokal (Underlay)
1. **Konfigurasi Wireless AP**: Screenshot dashboard/menu admin AP yang menunjukkan SSID `[NamaKelompok]_UAS`, SSID Mode `Hidden` (Broadcast OFF), Security Mode `WPA2-PSK`/`WPA3`, password `UASudb2026!#`, IP Address AP `172.10.18.1`, dan status **DHCP Server: OFF**.
2. **Koneksi Client**: Screenshot pada Client A dan Client B yang menunjukkan detail koneksi Wi-Fi terhubung ke SSID tersembunyi `[NamaKelompok]_UAS`.
3. **IP Address Static Client**: Screenshot konfigurasi network adapter / IP address static pada Client A (`172.10.18.2`) dan Client B (`172.10.18.3`).

### B. Screenshot Konfigurasi Jaringan Virtual & Web Server (Overlay)
4. **Dashboard ZeroTier Central**: Screenshot panel ZeroTier yang menampilkan nama network `[NamaKelompok]_UAS_ZT`, status network `Private`, dan list member Client A & Client B yang ter-authorize lengkap dengan managed IP `10.16.xx.1` dan `10.16.xx.2`.
5. **ZeroTier One Client**: Screenshot aplikasi/client ZeroTier One yang running di Client A dan Client B yang menunjukkan status `Joined` ke Network ID.
6. **HTTP Server & Cloudflared**: Screenshot jalannya HTTP Server lokal pada Client A beserta screenshot terminal/log `cloudflared` yang sedang berjalan mempublikasikan port tersebut hingga muncul URL publik HTTPS.

### C. Screenshot Pengujian Konektivitas & Akses
7. **Ping Lokal AP & Client**:
   - Screenshot ping dari AP ke Client A dan AP ke Client B (via tool ping di AP).
   - Screenshot ping dari Client A ke AP dan Client B ke AP (via CMD/Terminal).
   - Screenshot ping dua arah antar Client A (`172.10.18.2`) <-> Client B (`172.10.18.3`).
8. **Ping ZeroTier**: Screenshot ping dua arah antar Client A (`10.16.xx.1`) <-> Client B (`10.16.xx.2`) via IP ZeroTier.
9. **Akses Publik**: Screenshot halaman web HTTP Server Client A yang diakses via browser smartphone menggunakan koneksi data seluler (menampilkan URL publik Cloudflare secara jelas).

Ketentuan Penilaian:
- Membuat laporan lengkap & melampirkan seluruh screenshot wajib di atas : 10 Point
- Laporan tidak lengkap (ada screenshot yang kurang) : Potong 50% (Nilai menjadi 5 Point)
- Tidak membuat laporan : 0 Point

--------------------------------------------------

# Video Presentasi

Buat video presentasi berdurasi maksimal 3 menit (maksimal 1GB) yang menjelaskan:
1. Penjelasan topologi jaringan UAS secara terstruktur (Underlay P2P Wireless, Overlay ZeroTier, dan Cloudflare Tunnel).
2. Demonstrasi konfigurasi pada AP (Hidden SSID, DHCP OFF) dan Client (IP Statis lokal).
3. Demonstrasi dashboard ZeroTier Central dan otorisasi client.
4. Demonstrasi command line running `cloudflared` dan web server di Client A.
5. Uji coba langsung:
   - Ping wireless AP ke client & client ke AP.
   - Ping antar client via IP Lokal.
   - Ping antar client via IP ZeroTier.
   - Akses web server dari browser smartphone via URL publik Cloudflare.
6. Kesimpulan praktikum.

Ketentuan Penilaian:
- Membuat video presentasi lengkap sesuai instruksi : 10 Point
- Tidak membuat video : 0 Point

--------------------------------------------------

# Rekap Penilaian

| No | Kriteria | Nilai Maksimal | Potongan |
|----|-----------|----------------|-----------|
| 1 | Koneksi P2P Wireless, ZeroTier & Cloudflare Tunnel berhasil | 30 | Gagal = 0 |
| 2 | SSID, Security & DHCP AP sesuai ketentuan | 10 | Salah format / DHCP On = -50% |
| 3 | Konfigurasi IP Lokal Client A & B sesuai (Static IP) | 10 | Salah IP / DHCP On = -50% |
| 4 | Nama Network & Otorisasi ZeroTier sesuai | 10 | Public / Tanpa Auth = -50% |
| 5 | Konfigurasi IP ZeroTier sesuai format (`10.16.xx.1` & `10.16.xx.2`) | 10 | Salah IP / format = -50% |
| 6 | Cloudflare Tunnel berjalan di Client A menuju port lokal | 10 | Salah konfigurasi/lokasi = -50%, Tidak berhasil = 0 |
| 7 | Laporan dokumentasi lengkap (9 item screenshot wajib terlampir) | 10 | Kurang screenshot = -50%, Tidak ada = 0 |
| 8 | Membuat video presentasi sesuai durasi & ketentuan | 10 | Tidak membuat = 0 |

--------------------------------------------------

# Catatan
- Kerjakan secara kolaboratif dalam kelompok.
- Pastikan koneksi fisik/lokal nirkabel berjalan stabil dan bisa saling ping sebelum mengaktifkan ZeroTier dan Cloudflare Tunnel.
- Gunakan Cloudflare Quick Tunnel (`trycloudflare.com`) jika kelompok Anda tidak memiliki domain kustom.
- Upload file laporan PDF dan link video presentasi ke portal e-learning/email yang ditentukan.
- Nilai kelompok berlaku untuk semua anggota. Selamat berjuang dan semoga sukses!
