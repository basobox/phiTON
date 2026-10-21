# Dokumentasi GitHub di Termux untuk Python FP2

## 1. Tujuan

Dokumentasi ini menjelaskan cara menyiapkan Termux di Android/tablet untuk menulis, menjalankan, mengelola versi, dan mengunggah latihan Python ke GitHub dalam konteks proyek **FP2 — Odoo Sales Prediction**.

## 2. Peran setiap alat

| Alat | Fungsi |
|---|---|
| **Termux** | Lingkungan terminal Linux di Android |
| **Python** | Menjalankan program Python |
| **Helix** | Editor teks/kode berbasis terminal |
| **Git** | Mencatat perubahan dan mengelola versi kode secara lokal |
| **GitHub** | Menyimpan repository secara online dan menampilkan portofolio |

**Alur kerja:** tulis kode di Helix → jalankan dengan Python → catat perubahan dengan Git → unggah ke GitHub.

## 3. Instalasi awal

Buka Termux dan jalankan:

```bash
pkg update && pkg upgrade
pkg install git python helix
```

Verifikasi instalasi:

```bash
git --version
python --version
hx --version
```

Jika ketiga perintah menampilkan versi, alat terkait sudah terpasang.

## 4. Konfigurasi identitas Git

Ganti nilai contoh dengan username dan email yang ingin digunakan untuk commit:

```bash
git config --global user.name "USERNAME_GITHUB"
git config --global user.email "EMAIL_GITHUB"
```

Periksa konfigurasi:

```bash
git config --global --list
```

Nama dan email ini dicantumkan pada riwayat commit Git. Email yang digunakan sebaiknya sesuai preferensi privasi akun GitHub.

## 5. Membuat proyek latihan Python

Buat folder proyek dan masuk ke dalamnya:

```bash
mkdir -p ~/fp2-python
cd ~/fp2-python
```

Buat file latihan:

```bash
hx latihan_penjualan.py
```

Masukkan kode berikut:

```python
total_penjualan = 6000000

if total_penjualan >= 5000000:
    print("Target penjualan tercapai")
else:
    print("Target penjualan belum tercapai")
```

Simpan di Helix dengan `:w`, lalu keluar dengan `:q`.

Jalankan program:

```bash
python latihan_penjualan.py
```

Output yang diharapkan:

```text
Target penjualan tercapai
```

Contoh ini melatih variabel, perbandingan `>=`, percabangan `if/else`, dan konteks aturan target penjualan FP2.

## 6. Membuat repository GitHub

1. Buka [GitHub — New repository](https://github.com/new).
2. Buat repository bernama `fp2-python`.
3. Pilih **Public** jika ingin kode menjadi portofolio yang dapat dilihat publik. Pilih **Private** jika belum ingin memublikasikannya.
4. Untuk alur awal yang paling sederhana, buat repository kosong tanpa README, `.gitignore`, atau lisensi. File-file tersebut bisa ditambahkan kemudian.

## 7. Menginisialisasi Git dan mengunggah kode

Dari folder proyek `~/fp2-python`, jalankan:

```bash
git init -b main
git add latihan_penjualan.py
git commit -m "Add Python sales exercise"
git remote add origin https://github.com/USERNAME_GITHUB/fp2-python.git
git push -u origin main
```

Ganti `USERNAME_GITHUB` dengan username GitHub yang benar.

Penjelasan singkat:

- `git init -b main`: membuat repository Git lokal dengan branch awal `main`.
- `git add latihan_penjualan.py`: memasukkan file ke staging area.
- `git commit -m "..."`: merekam snapshot perubahan.
- `git remote add origin ...`: menghubungkan repository lokal dengan repository GitHub.
- `git push -u origin main`: mengunggah branch `main` dan mengatur upstream untuk push berikutnya.

### Autentikasi HTTPS

Jika GitHub meminta autentikasi melalui HTTPS, gunakan metode autentikasi yang didukung GitHub, misalnya **Personal Access Token (PAT)** sebagai pengganti password akun untuk operasi Git melalui HTTPS. Jangan menaruh token di dalam kode, README, atau repository. Jangan membagikan token kepada orang lain.

Jika autentikasi bermasalah, periksa panduan resmi GitHub: [About authentication to GitHub](https://docs.github.com/en/authentication).

## 8. Workflow harian

Setelah mengubah kode dan mengujinya, periksa status:

```bash
git status
```

Kemudian simpan perubahan ke riwayat Git dan unggah:

```bash
git add .
git commit -m "Practice Python conditionals"
git push
```

Ganti pesan commit agar menjelaskan perubahan yang benar-benar dilakukan, misalnya:

```bash
git commit -m "Add sales threshold condition"
```

Sebelum commit, pastikan tidak ada file rahasia, token, password, atau data pelanggan yang ikut ditambahkan.

## 9. Struktur repository yang disarankan

Struktur awal untuk latihan Python FP2:

```text
fp2-python/
├── 01_fundamentals/
├── 02_control_flow/
├── 03_data_structures/
├── 04_functions/
├── 05_csv_pandas/
├── 06_eda/
├── 07_machine_learning/
├── latihan_penjualan.py
└── README.md
```

Contoh fungsi folder:

| Folder | Materi |
|---|---|
| `01_fundamentals/` | Variabel, tipe data, operator |
| `02_control_flow/` | `if`, `for`, `while` |
| `03_data_structures/` | List, dictionary, tuple, set |
| `04_functions/` | Fungsi dan pemecahan masalah |
| `05_csv_pandas/` | Membaca CSV dan mengolah data dengan Pandas |
| `06_eda/` | Exploratory Data Analysis untuk transaksi penjualan |
| `07_machine_learning/` | Model prediksi dan evaluasi |

Struktur ini dapat dikembangkan bertahap. Tidak perlu membuat semua folder sekaligus sebelum materinya dipelajari.

## 10. Prinsip penggunaan Git yang baik

- **Sering commit dalam perubahan kecil:** satu commit sebaiknya menjelaskan satu perubahan yang jelas.
- **Gunakan pesan commit yang deskriptif:** hindari pesan seperti `update` atau `fix` tanpa konteks.
- **Uji kode sebelum commit:** pastikan program berjalan sesuai harapan.
- **Jangan menyimpan rahasia:** hindari token, password, kredensial, dan data pribadi.
- **Pisahkan latihan dan proyek akhir:** latihan kecil bisa berada di repository belajar, sementara proyek akhir FP2 dapat memiliki repository tersendiri.
- **Dokumentasikan tujuan:** README sebaiknya menjelaskan tujuan proyek, cara menjalankan kode, struktur folder, dan progres.

## 11. Ringkasan perintah penting

| Kebutuhan | Perintah |
|---|---|
| Memeriksa status perubahan | `git status` |
| Melihat riwayat commit | `git log --oneline` |
| Menambahkan file ke staging | `git add nama_file.py` |
| Membuat commit | `git commit -m "Pesan commit"` |
| Mengunggah ke GitHub | `git push` |
| Melihat remote | `git remote -v` |
| Menjalankan Python | `python nama_file.py` |
| Membuka file di Helix | `hx nama_file.py` |

## 12. Referensi

- [GitHub — Membuat repository baru](https://github.com/new)
- [GitHub Docs — Menambahkan kode yang di-host secara lokal ke GitHub](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github)
- [GitHub Docs — Autentikasi ke GitHub](https://docs.github.com/en/authentication)

---

**Catatan:** Git mengelola riwayat perubahan di perangkat lokal, sedangkan GitHub menyimpan salinan repository secara online. Menggunakan `git commit` saja belum mengunggah perubahan; gunakan `git push` untuk mengirimkannya ke GitHub.
