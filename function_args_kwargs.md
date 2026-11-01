# Ringkasan Latihan Python: Function, `*args`, dan `**kwargs`

## 1. Tujuan latihan

Persiapan live coding bootcamp dengan materi:

-   Function Python
-   Parameter dan argument
-   Variable-length positional arguments (`*args`)
-   Variable-length keyword arguments (`**kwargs`)
-   Perulangan `for`
-   Akumulator dan perhitungan
-   `len()`, `.items()`, dan `return`
-   Menggabungkan `*args` dan `**kwargs`
-   Debugging sintaks, nama variabel, dan indentasi

Pendekatan latihan adalah bertahap: memahami konsep melalui contoh,
mengerjakan soal sendiri, lalu memperbaiki kesalahan dengan petunjuk
sebelum melihat solusi lengkap.

------------------------------------------------------------------------

## 2. Konsep dasar yang dipelajari

### A. Function

Function dibuat menggunakan `def`. Setelah parameter, deklarasi function
harus diakhiri tanda titik dua (`:`).

``` python
def nama_function(parameter):
    # isi function
    return hasil
```

Hal yang perlu diperhatikan:

-   Tanda titik dua (`:`) wajib setelah deklarasi function.
-   Isi function harus menggunakan indentasi yang konsisten.
-   `return` mengirimkan nilai kembali kepada pemanggil function.

### B. `*args`

`*args` digunakan ketika function perlu menerima sejumlah positional
arguments yang jumlahnya tidak ditentukan sebelumnya.

``` python
def contoh(*args):
    print(args)
```

Contoh pemanggilan:

``` python
contoh(10, 20, 30)
contoh(5, 10, 15, 20)
```

Di dalam function, `args` ditampung sebagai **tuple**.

Nama `args` bukan kata khusus Python. Nama variabelnya boleh berbeda,
tetapi tanda `*` yang membuat parameter tersebut menerima banyak
positional arguments.

### C. `**kwargs`

`**kwargs` digunakan ketika function perlu menerima sejumlah keyword
arguments yang jumlahnya tidak ditentukan sebelumnya.

``` python
def contoh(**kwargs):
    print(kwargs)
```

Contoh pemanggilan:

``` python
contoh(nama="Budi", umur=20, jurusan="Data Science")
```

Di dalam function, `kwargs` ditampung sebagai **dictionary**.

Nama `kwargs` juga bukan kata khusus Python. Tanda `**` adalah bagian
penting dari sintaksnya.

### D. Perbedaan `*args` dan `**kwargs`

  ------------------------------------------------------------------------------------
  Aspek                   `*args`                 `**kwargs`
  ----------------------- ----------------------- ------------------------------------
  Jenis argument          Positional arguments    Keyword arguments

  Bentuk data di dalam    Tuple                   Dictionary
  function                                        

  Contoh pemanggilan      `fungsi(10, 20, 30)`    `fungsi(nama="Budi")`

  Cara mengakses          `args[0]`               `kwargs["nama"]`

  Cara iterasi            `for nilai in args`     `for key, value in kwargs.items()`
  ------------------------------------------------------------------------------------

### E. `kwargs.items()`

Method `.items()` menghasilkan pasangan key dan value dari dictionary.

``` python
data = {
    "nama": "Budi",
    "umur": 20
}

for key, value in data.items():
    print(key, ":", value)
```

Output:

``` text
nama : Budi
umur : 20
```

Ingat, nama method yang benar adalah `.items()`, bukan `.item()`.

### F. `len()`

`len()` menghitung jumlah elemen pada objek seperti tuple, list,
dictionary, dan string.

``` python
args = (10, 20, 30)
print(len(args))  # 3
```

Dalam latihan ini:

-   `len(args)` menghitung banyaknya argument yang diterima.
-   `len(angka)` tidak tepat jika `angka` adalah integer, karena integer
    tidak memiliki panjang.

### G. Akumulator

Akumulator adalah variabel yang digunakan untuk mengumpulkan hasil
perhitungan secara bertahap.

``` python
total = 0

for angka in args:
    total += angka
```

`total += angka` setara dengan `total = total + angka`.

------------------------------------------------------------------------

## 3. Tahapan latihan dan hasilnya

### Latihan 1 --- Menjumlahkan banyak angka dengan `*args`

Soal: Buat function `hitung_total(*args)` untuk menjumlahkan semua angka
yang diberikan.

Kode:

``` python
def hitung_total(*args):
    total = 0

    for angka in args:
        total += angka

    return total


print(hitung_total(10, 20, 30))
print(hitung_total(5, 10, 15, 20, 50))
```

Output:

``` text
60
100
```

Pemahaman yang diperoleh:

1.  `*args` menampung semua angka dalam tuple.
2.  `for` mengambil angka satu per satu.
3.  `total += angka` menjumlahkan setiap angka.
4.  `return total` mengembalikan hasil penjumlahan.

### Latihan 2 --- Menghitung rata-rata dengan `*args`

Soal: Buat function `hitung_rata_rata(*args)`.

Kode:

``` python
def hitung_rata_rata(*args):
    total_angka = 0

    for angka in args:
        total_angka += angka

    jumlah_angka = len(args)
    rata_rata = total_angka / jumlah_angka

    return rata_rata


print(hitung_rata_rata(10, 20, 30))
print(hitung_rata_rata(80, 90, 100, 70))
```

Output:

``` text
20.0
85.0
```

Rumus:

\[ Rata`\text{-}`{=tex}rata =
`\frac{Total\ seluruh\ angka}{Jumlah\ angka}`{=tex} \]

Hal yang dipelajari:

-   `total_angka` menyimpan hasil penjumlahan.
-   `len(args)` menghitung jumlah angka.
-   `jumlah_angka` tidak perlu dihitung berulang kali di dalam
    perulangan.
-   Jangan menggunakan `len(angka)` jika `angka` berisi satu nilai
    integer.
-   Untuk kode yang lebih aman, function dapat diberi pemeriksaan jika
    tidak menerima argument agar tidak membagi dengan nol.

Contoh pemeriksaan tambahan:

``` python
def hitung_rata_rata(*args):
    if len(args) == 0:
        return 0

    total = 0
    for angka in args:
        total += angka

    return total / len(args)
```

### Latihan 3 --- Menampilkan data siswa dengan `**kwargs`

Soal: Buat function `data_siswa(**kwargs)` untuk menampilkan semua
informasi siswa.

Kode:

``` python
def data_siswa(**kwargs):
    for key, value in kwargs.items():
        print(key, ":", value)


data_siswa(
    nama="Budi",
    umur=20,
    jurusan="Data Science"
)
```

Output:

``` text
nama : Budi
umur : 20
jurusan : Data Science
```

Pemahaman yang diperoleh:

-   `kwargs` berbentuk dictionary.
-   `kwargs.items()` menghasilkan pasangan key-value.
-   `key` menerima nama atribut, sedangkan `value` menerima isinya.

### Latihan 4 --- Menggabungkan `*args` dan `**kwargs`

Soal: Buat function transaksi yang menerima banyak harga barang dan
informasi transaksi.

Kode:

``` python
def transaksi(*args, **kwargs):
    for key, value in kwargs.items():
        print(key, ":", value)

    total_transaksi = 0

    for pembelian in args:
        total_transaksi += pembelian

    print(f"Total transaksi : {total_transaksi}")
    return total_transaksi


transaksi(
    10000, 20000, 30000,
    nama="Budi",
    metode="Tunai"
)
```

Output:

``` text
nama : Budi
metode : Tunai
Total transaksi : 60000
```

Pembagian data saat function dipanggil:

`args`:

``` python
(10000, 20000, 30000)
```

`kwargs`:

``` python
{
    "nama": "Budi",
    "metode": "Tunai"
}
```

Catatan penting: ketika memanggil function dengan `**kwargs`, keyword
ditulis langsung seperti `nama="Budi"`, bukan dibungkus dalam tuple atau
dictionary.

### Latihan 5 --- Laporan penjualan

Soal: Buat function yang menerima harga produk melalui `*args` dan
informasi toko melalui `**kwargs`.

Kode akhir:

``` python
def laporan_penjualan(*args, **kwargs):
    for key, value in kwargs.items():
        print(key, ":", value)

    total_penjualan = 0

    for data in args:
        total_penjualan += data

    print(f"Jumlah produk : {len(args)}")
    print(f"Total penjualan : {total_penjualan}")

    return total_penjualan


laporan_penjualan(
    10000, 20000, 30000, 40000,
    toko="Supermarket ABC",
    kasir="Budi",
    tanggal="01-11-2026"
)
```

Output:

``` text
toko : Supermarket ABC
kasir : Budi
tanggal : 01-11-2026
Jumlah produk : 4
Total penjualan : 100000
```

Pembagian tanggung jawab:

-   `args`: menampung harga produk.
-   `kwargs`: menampung informasi toko, kasir, dan tanggal.
-   `len(args)`: menghitung jumlah produk yang direpresentasikan oleh
    setiap harga.
-   `total_penjualan`: menyimpan hasil penjumlahan harga.
-   `return total_penjualan`: mengembalikan total penjualan.

Catatan: contoh ini menganggap setiap argument harga mewakili satu
produk. Jika satu produk dapat dibeli dalam beberapa kuantitas, struktur
datanya perlu dikembangkan.

------------------------------------------------------------------------

## 4. Kesalahan yang ditemukan selama latihan

Kesalahan adalah bagian dari proses debugging. Berikut rangkumannya.

  --------------------------------------------------------------------------------------
  Kesalahan                       Perbaikan                      Penjelasan
  ------------------------------- ------------------------------ -----------------------
  `def hitung_total(*args)`       `def hitung_total(*args):`     Deklarasi function
                                                                 membutuhkan `:`

  `len(angka)`                    `len(args)`                    `angka` adalah integer,
                                                                 sedangkan `args` adalah
                                                                 tuple

  `hitung_total / jumlah_angka`   `total_angka / jumlah_angka`   Gunakan variabel yang
                                                                 menyimpan hasil
                                                                 penjumlahan

  `kwargs.item()`                 `kwargs.items()`               Method dictionary yang
                                                                 benar adalah `.items()`

  `kwags`                         `kwargs`                       Salah ketik nama
                                                                 variabel

  `arg`                           `args`                         Nama variabel harus
                                                                 konsisten

  `print(key, : , value)`         `print(key, ":", value)`       Tanda titik dua untuk
                                                                 output harus berupa
                                                                 string

  `{nama="Budi"}`                 `nama="Budi"` saat pemanggilan Keyword arguments
                                                                 ditulis langsung

  `(nama:"Budi")`                 `nama="Budi"`                  Bentuk tersebut bukan
                                                                 sintaks pemanggilan
                                                                 keyword arguments

  `penjualan += data`             `total_penjualan += data`      Gunakan nama variabel
                                                                 akumulator yang benar

  `return penjualan`              `return total_penjualan`       Kembalikan variabel
                                                                 yang memang menyimpan
                                                                 total

  Indentasi tidak sejajar         Rapikan blok function dan      Python menggunakan
                                  `for`                          indentasi untuk
                                                                 menentukan blok kode
  --------------------------------------------------------------------------------------

### Pola debugging yang perlu dibiasakan

Ketika kode error, periksa secara berurutan:

1.  **Sintaks:** apakah tanda `:`, kurung, koma, dan tanda kutip sudah
    benar?
2.  **Nama variabel:** apakah nama variabel yang digunakan sama dengan
    yang dideklarasikan?
3.  **Indentasi:** apakah isi function dan perulangan berada pada
    tingkat indentasi yang tepat?
4.  **Tipe data:** apakah operasi sesuai dengan tipe datanya?
5.  **Alur logika:** apakah nilai dihitung dan dikembalikan oleh
    variabel yang benar?

------------------------------------------------------------------------

## 5. Pola umum menggabungkan `*args` dan `**kwargs`

Gunakan pola ini sebagai kerangka, bukan untuk dihafalkan tanpa memahami
isinya.

``` python
def nama_function(*args, **kwargs):

    # Mengolah keyword arguments
    for key, value in kwargs.items():
        print(key, ":", value)

    # Mengolah positional arguments
    total = 0

    for nilai in args:
        total += nilai

    # Mengembalikan hasil
    return total
```

Urutan parameter penting. Dalam pola ini, `*args` diletakkan sebelum
`**kwargs`.

------------------------------------------------------------------------

## 6. Kesimpulan pembelajaran

Dari rangkaian latihan, konsep yang telah dipraktikkan adalah:

-   Membuat function menggunakan `def`.
-   Menerima jumlah positional arguments yang fleksibel dengan `*args`.
-   Memahami bahwa `args` berbentuk tuple.
-   Menerima jumlah keyword arguments yang fleksibel dengan `**kwargs`.
-   Memahami bahwa `kwargs` berbentuk dictionary.
-   Melakukan iterasi menggunakan `for`.
-   Mengakses pasangan key-value menggunakan `.items()`.
-   Menghitung jumlah elemen menggunakan `len()`.
-   Menggunakan akumulator untuk penjumlahan.
-   Mengembalikan hasil menggunakan `return`.
-   Menggabungkan `*args` dan `**kwargs` dalam satu function.
-   Memperbaiki kesalahan sintaks, penamaan variabel, tipe data, dan
    indentasi.

Hal yang paling penting: `*args` dan `**kwargs` bukan dua jenis function
yang berbeda. Keduanya adalah cara menerima argument dalam jumlah
fleksibel. Keduanya dapat digunakan bersama ketika function membutuhkan
input positional sekaligus keyword.

------------------------------------------------------------------------

## 7. Rencana latihan lanjutan untuk live coding

Sebelum live coding, ulangi latihan berikut tanpa melihat kode solusi:

1.  Buat function untuk menjumlahkan angka dengan `*args`.
2.  Buat function untuk menghitung rata-rata dengan `*args`.
3.  Buat function untuk menampilkan informasi siswa dengan `**kwargs`.
4.  Buat function transaksi yang menggabungkan `*args` dan `**kwargs`.
5.  Buat laporan penjualan dengan jumlah produk dan total penjualan.
6.  Tambahkan validasi untuk kondisi input kosong.
7.  Kerjakan ulang dengan batas waktu dan jelaskan fungsi setiap baris
    kode.

### Checklist kesiapan

-   [ ] Saya dapat menjelaskan perbedaan `*args` dan `**kwargs`.
-   [ ] Saya tahu bahwa `args` berbentuk tuple.
-   [ ] Saya tahu bahwa `kwargs` berbentuk dictionary.
-   [ ] Saya dapat menggunakan `for` untuk memproses keduanya.
-   [ ] Saya dapat menggunakan `.items()` untuk membaca key-value.
-   [ ] Saya dapat menggunakan `len(args)` untuk menghitung jumlah
    argument.
-   [ ] Saya dapat menggunakan akumulator untuk menghitung total.
-   [ ] Saya dapat mengembalikan hasil menggunakan `return`.
-   [ ] Saya dapat menggabungkan `*args` dan `**kwargs`.
-   [ ] Saya dapat menemukan dan memperbaiki kesalahan sendiri.

**Strategi utama:** Pahami → Lengkapi → Tulis ulang → Terapkan. Jangan
hanya membaca kode yang benar. Kemampuan live coding dibangun dengan
menulis sendiri, menjalankan, membaca error, dan memperbaikinya.
