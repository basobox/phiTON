# Rangkuman LC2 --- Phase 0 Week 2 Day 3 PM

## Relational Database and Queries: Google BigQuery

Dokumen ini merangkum materi BigQuery dan menghubungkannya dengan
kisi-kisi Live Code 2 (LC2) Hacktiv8 FTDS.

## 1. Peta Kisi-Kisi LC2

Materi yang perlu dikuasai:

1.  **DQL / SQL BigQuery**
    -   `GROUP BY` dan aggregation function
    -   Subquery
    -   Datetime: `EXTRACT`, `DATE_DIFF`, `DATE_ADD`
2.  **Pandas DataFrame**
    -   Membaca dan memahami DataFrame
    -   Filtering dan Pandas `.query()`
    -   Aggregation dan `groupby()`
    -   Looping dan conditional pada DataFrame
3.  **Datetime Processing**
    -   Mengolah tanggal di BigQuery SQL
    -   Mengolah tanggal di Pandas

> **Catatan:** Window Function tidak termasuk kisi-kisi LC2 yang
> dibagikan. Prioritaskan materi yang tercantum di atas.

------------------------------------------------------------------------

## 2. Hierarki Google BigQuery

  -----------------------------------------------------------------------
  Komponen                Penjelasan              Analogi PostgreSQL
  ----------------------- ----------------------- -----------------------
  Project                 Kontainer utama untuk   Level project
                          resource, billing, dan  
                          IAM/permission          

  Dataset                 Wadah untuk tabel,      Schema
                          view, fungsi, dan       
                          prosedur                

  Table                   Tempat data disimpan    Table
                          dalam baris dan kolom   

  View                    Tabel virtual yang      View
                          didefinisikan oleh      
                          query                   

  External Table          Tabel yang merujuk data Tabel eksternal
                          di luar BigQuery,       
                          misalnya Cloud Storage  

  Job                     Operasi seperti query,  Eksekusi query
                          load, atau export yang  
                          dijalankan BigQuery     
  -----------------------------------------------------------------------

Struktur sederhananya:

``` text
Project
└── Dataset
    ├── Table
    ├── View
    └── External Table
```

Hal penting: - Project mengatur resource, billing, dan akses. - Dataset
mengelompokkan tabel dan memiliki lokasi data, misalnya US, EU, atau
Tokyo. - Lokasi dataset tidak dapat dipindahkan begitu saja setelah
dibuat. - Setiap query atau proses BigQuery dijalankan sebagai Job dan
memiliki Job ID. - Job History dapat digunakan untuk memeriksa status
pekerjaan dan jumlah data yang diproses.

### Contoh hierarki

``` text
Project ID : bigquery-public-data
Dataset    : austin_crime
Table      : crime
```

Nama lengkap tabel:

``` sql
`bigquery-public-data.austin_crime.crime`
```

## 3. Project Pribadi dan Public Dataset

`bigquery-public-data` adalah project yang menyediakan dataset publik
untuk latihan dan analisis.

Contoh dari materi: - Project: `bigquery-public-data` - Dataset:
`austin_crime` - Table: `crime`

Query:

``` sql
SELECT *
FROM `bigquery-public-data.austin_crime.crime`;
```

Bedakan dua project berikut:

  -----------------------------------------------------------------------
  Jenis project                       Fungsi
  ----------------------------------- -----------------------------------
  Project publik                      Sumber data yang akan dibaca/query

  Project GCP pribadi                 Project yang digunakan untuk
                                      autentikasi dan menjalankan query,
                                      termasuk billing
  -----------------------------------------------------------------------

**Project ID berbeda dengan Project Name.** Saat membuat koneksi di
Colab, gunakan GCP Project ID, bukan nama tampilannya.

## 4. Dasar DQL BigQuery

DQL (*Data Query Language*) digunakan untuk mengambil dan menganalisis
data.

### 4.1 SELECT

Mengambil kolom dari tabel.

``` sql
SELECT *
FROM `bigquery-public-data.austin_crime.crime`;
```

Mengambil kolom tertentu:

``` sql
SELECT
    primary_type,
    year
FROM `bigquery-public-data.austin_crime.crime`;
```

### 4.2 COUNT

Menghitung jumlah baris.

``` sql
SELECT
    COUNT(*) AS total_crimes
FROM `bigquery-public-data.austin_crime.crime`;
```

`COUNT(*)` menghitung semua baris, sedangkan `COUNT(column)` menghitung
nilai non-NULL pada kolom tersebut.

### 4.3 DISTINCT

Mengambil nilai unik.

``` sql
SELECT DISTINCT
    primary_type
FROM `bigquery-public-data.austin_crime.crime`
ORDER BY primary_type ASC;
```

### 4.4 GROUP BY

Mengelompokkan baris berdasarkan nilai kolom yang sama.

Contoh data dummy:

  order_id   customer   category        amount
  ---------- ---------- ------------- --------
  O001       Andi       Electronics     500000
  O002       Budi       Fashion         300000
  O003       Andi       Fashion         200000
  O004       Citra      Electronics     400000
  O005       Budi       Food            150000

Query:

``` sql
SELECT
    customer,
    COUNT(*) AS total_orders,
    SUM(amount) AS total_spending
FROM `my-project.sales.transactions`
GROUP BY customer;
```

Hasil:

  customer     total_orders   total_spending
  ---------- -------------- ----------------
  Andi                    2           700000
  Budi                    2           450000
  Citra                   1           400000

**Inti:** setiap kolom yang ditampilkan dan bukan hasil aggregation
umumnya harus dimasukkan ke `GROUP BY`.

### 4.5 Aggregation Function

  Function    Kegunaan                                Contoh
  ----------- --------------------------------------- ---------------------
  `COUNT()`   Menghitung jumlah data non-NULL/baris   Jumlah transaksi
  `SUM()`     Menjumlahkan nilai                      Total revenue
  `AVG()`     Menghitung rata-rata                    Rata-rata transaksi
  `MIN()`     Mencari nilai terkecil                  Transaksi terendah
  `MAX()`     Mencari nilai terbesar                  Transaksi tertinggi

Contoh:

``` sql
SELECT
    COUNT(*) AS total_orders,
    SUM(amount) AS total_revenue,
    AVG(amount) AS average_order,
    MIN(amount) AS minimum_order,
    MAX(amount) AS maximum_order
FROM `my-project.sales.transactions`;
```

### 4.6 ORDER BY dan LIMIT

``` sql
SELECT
    customer,
    SUM(amount) AS total_spending
FROM `my-project.sales.transactions`
GROUP BY customer
ORDER BY total_spending DESC
LIMIT 5;
```

-   `ASC`: urutan naik, dari kecil ke besar.
-   `DESC`: urutan turun, dari besar ke kecil.
-   `LIMIT`: membatasi jumlah baris hasil.

### 4.7 WHERE vs HAVING

  -----------------------------------------------------------------------
  WHERE                               HAVING
  ----------------------------------- -----------------------------------
  Memfilter baris sebelum             Memfilter grup setelah `GROUP BY`
  pengelompokan                       

  Umumnya memfilter nilai kolom asli  Umumnya memfilter hasil agregasi
  -----------------------------------------------------------------------

Contoh `WHERE`:

``` sql
SELECT *
FROM `my-project.sales.transactions`
WHERE amount > 300000;
```

Contoh `HAVING`:

``` sql
SELECT
    customer,
    SUM(amount) AS total_spending
FROM `my-project.sales.transactions`
GROUP BY customer
HAVING SUM(amount) > 500000;
```

Urutan penulisan SQL:

``` sql
SELECT ...
FROM ...
WHERE ...
GROUP BY ...
HAVING ...
ORDER BY ...
LIMIT ...;
```

Urutan logika pemrosesan secara konseptual:

1.  `FROM`
2.  `WHERE`
3.  `GROUP BY`
4.  `HAVING`
5.  `SELECT`
6.  `ORDER BY`
7.  `LIMIT`

## 5. Subquery

Subquery adalah query yang ditulis di dalam query lain. Hasil query
bagian dalam digunakan oleh query bagian luar.

### Contoh kasus

Cari transaksi yang nilainya lebih besar daripada rata-rata seluruh
transaksi.

``` sql
SELECT *
FROM `my-project.sales.transactions`
WHERE amount > (
    SELECT AVG(amount)
    FROM `my-project.sales.transactions`
);
```

Cara memahami query: 1. Query dalam menghitung `AVG(amount)`. 2. Hasil
rata-rata dikembalikan sebagai satu nilai. 3. Query luar memilih
transaksi dengan `amount` lebih besar dari nilai tersebut.

Pola umum:

``` sql
SELECT *
FROM table_name
WHERE column_name > (
    SELECT AGGREGATE_FUNCTION(column_name)
    FROM table_name
);
```

Subquery juga dapat digunakan pada `FROM` atau `SELECT`, bergantung pada
kebutuhan analisis.

Hal yang perlu diperhatikan: - Subquery setelah operator `>` atau `=`
harus menghasilkan satu nilai jika digunakan sebagai scalar subquery. -
Jika subquery menghasilkan banyak baris, pertimbangkan `IN`, `EXISTS`,
atau bentuk query lain yang sesuai. - Pastikan hasil query bagian dalam
sesuai dengan kebutuhan query luar.

## 6. Datetime Processing di BigQuery

Materi LC2 menyebut `EXTRACT`, `DATE_DIFF`, dan `DATE_ADD`.

Contoh data:

  order_id   order_date
  ---------- ------------
  O001       2026-01-05
  O002       2026-02-10
  O003       2026-03-15

### 6.1 EXTRACT

Mengambil bagian tertentu dari tanggal.

``` sql
SELECT
    order_date,
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(MONTH FROM order_date) AS month,
    EXTRACT(DAY FROM order_date) AS day
FROM `my-project.sales.transactions`;
```

Contoh jumlah transaksi per bulan:

``` sql
SELECT
    EXTRACT(MONTH FROM order_date) AS month,
    COUNT(*) AS total_orders
FROM `my-project.sales.transactions`
GROUP BY month
ORDER BY month;
```

### 6.2 DATE_DIFF

Menghitung selisih dua tanggal.

``` sql
SELECT
    DATE_DIFF(
        DATE '2026-03-15',
        DATE '2026-01-05',
        DAY
    ) AS difference_days;
```

Hasil: `69`.

Pola:

``` sql
DATE_DIFF(end_date, start_date, DAY)
```

Tanggal akhir ditulis lebih dahulu, kemudian tanggal awal.

### 6.3 DATE_ADD

Menambahkan interval ke tanggal.

``` sql
SELECT
    DATE_ADD(
        DATE '2026-01-05',
        INTERVAL 7 DAY
    ) AS new_date;
```

Hasil: `2026-01-12`.

Contoh menambahkan satu bulan:

``` sql
SELECT
    DATE_ADD(order_date, INTERVAL 1 MONTH) AS next_month
FROM `my-project.sales.transactions`;
```

Interval yang umum: `DAY`, `WEEK`, `MONTH`, `QUARTER`, `YEAR`.

### Perhatikan tipe data tanggal

BigQuery memiliki tipe `DATE`, `DATETIME`, dan `TIMESTAMP`.

-   `DATE`: tanggal tanpa jam.
-   `DATETIME`: tanggal dan jam tanpa informasi zona waktu.
-   `TIMESTAMP`: titik waktu absolut yang berkaitan dengan zona waktu.

`DATE_DIFF` dan `DATE_ADD` digunakan untuk tipe `DATE`. Untuk `DATETIME`
atau `TIMESTAMP`, gunakan fungsi yang sesuai, misalnya `DATETIME_DIFF`,
`DATETIME_ADD`, `TIMESTAMP_DIFF`, atau `TIMESTAMP_ADD`.

## 7. Menghubungkan Google Colab dengan BigQuery

Pada LC2, query BigQuery dikerjakan melalui Google Colab. Code template
koneksi dapat disediakan dalam soal.

### 7.1 Import library dan autentikasi

``` python
from google.colab import auth
from google.cloud import bigquery

auth.authenticate_user()
print("Authenticated")

GCP_PROJECT_ID = "project-id-kamu"

client = bigquery.Client(
    project=GCP_PROJECT_ID
)
```

  -----------------------------------------------------------------------
  Code                                Fungsi
  ----------------------------------- -----------------------------------
  `auth.authenticate_user()`          Meminta autentikasi akun Google

  `GCP_PROJECT_ID`                    ID project GCP pribadi

  `bigquery.Client()`                 Membuat client untuk berinteraksi
                                      dengan BigQuery

  `client`                            Objek yang digunakan untuk
                                      menjalankan query
  -----------------------------------------------------------------------

### 7.2 Menjalankan query SQL dari Python

``` python
df = client.query("""
    SELECT COUNT(*) AS total_crimes
    FROM `bigquery-public-data.austin_crime.crime`
""").to_dataframe()

df
```

Pola penting:

``` python
df = client.query("""
    SQL QUERY
""").to_dataframe()
```

Alur kerja:

``` text
Google BigQuery
      |
      v
client.query()
      |
      v
Hasil query
      |
      v
.to_dataframe()
      |
      v
Pandas DataFrame
```

Artinya: 1. SQL dijalankan di BigQuery. 2. BigQuery mengembalikan hasil
query. 3. `.to_dataframe()` mengubah hasil menjadi Pandas DataFrame. 4.
DataFrame dapat diproses lebih lanjut menggunakan Python.

## 8. Pandas DataFrame

Pandas digunakan untuk mengolah data di Python setelah data tersedia
sebagai DataFrame.

### 8.1 Membuat DataFrame dummy

``` python
import pandas as pd
import numpy as np

data = {
    "customer": ["Andi", "Budi", "Citra", "Deni"],
    "amount": [500000, 300000, 700000, 150000],
    "city": ["Jakarta", "Bandung", "Jakarta", "Surabaya"]
}

df = pd.DataFrame(data)
```

### 8.2 Memahami struktur DataFrame

``` python
df.head()       # 5 baris pertama
df.tail()       # 5 baris terakhir
df.shape        # (jumlah_baris, jumlah_kolom)
df.info()       # struktur dan tipe data
df.describe()   # statistik deskriptif numerik
df.columns      # nama kolom
df.dtypes       # tipe data setiap kolom
```

### 8.3 Filtering dengan Boolean Indexing

Menampilkan transaksi di atas 300.000:

``` python
df[df["amount"] > 300000]
```

Menggunakan beberapa kondisi:

``` python
df[
    (df["amount"] > 300000) &
    (df["city"] == "Jakarta")
]
```

Operator penting: - `&`: AND - `|`: OR - `~`: NOT

Gunakan tanda kurung pada setiap kondisi ketika menggabungkan kondisi
Boolean.

### 8.4 Pandas `.query()`

Alternatif filtering yang lebih ringkas:

``` python
df.query("amount > 300000")
```

Beberapa kondisi:

``` python
df.query("amount > 300000 and city == 'Jakarta'")
```

Jika nilai berasal dari variabel Python, gunakan `@`:

``` python
minimum_amount = 300000

df.query("amount > @minimum_amount")
```

### 8.5 Aggregation dengan Pandas

``` python
df["amount"].sum()
df["amount"].mean()
df["amount"].min()
df["amount"].max()
df["amount"].count()
```

Mengelompokkan data:

``` python
df.groupby("city")["amount"].sum()
```

Menghasilkan beberapa aggregation sekaligus:

``` python
df.groupby("city")["amount"].agg(
    ["count", "sum", "mean", "min", "max"]
)
```

Padanan konsep SQL:

  SQL          Pandas
  ------------ --------------------------------
  `GROUP BY`   `.groupby()`
  `COUNT()`    `.count()` atau `.size()`
  `SUM()`      `.sum()`
  `AVG()`      `.mean()`
  `MIN()`      `.min()`
  `MAX()`      `.max()`
  `WHERE`      Boolean filtering / `.query()`
  `ORDER BY`   `.sort_values()`

**Perhatian:** `.count()` menghitung nilai non-NULL per kolom, sedangkan
`.size()` menghitung jumlah baris dalam setiap grup. Pilih sesuai
kebutuhan.

## 9. Conditional pada DataFrame

Conditional digunakan untuk memberikan label atau nilai berdasarkan
kondisi.

### 9.1 Menggunakan `np.where()`

Kasus: transaksi di atas 400.000 diberi label `High Value`, lainnya
`Regular`.

``` python
df["category"] = np.where(
    df["amount"] > 400000,
    "High Value",
    "Regular"
)
```

Bentuk umum:

``` python
np.where(kondisi, nilai_jika_benar, nilai_jika_salah)
```

### 9.2 Menggunakan `np.select()` untuk banyak kondisi

``` python
conditions = [
    df["amount"] > 500000,
    df["amount"] >= 300000
]

choices = ["High", "Medium"]

df["level"] = np.select(
    conditions,
    choices,
    default="Low"
)
```

### 9.3 Menggunakan `.apply()` dan function

``` python
def classify(amount):
    if amount > 500000:
        return "High"
    elif amount >= 300000:
        return "Medium"
    else:
        return "Low"

df["level"] = df["amount"].apply(classify)
```

Padanan konsep SQL:

``` sql
CASE
    WHEN amount > 500000 THEN 'High'
    WHEN amount >= 300000 THEN 'Medium'
    ELSE 'Low'
END
```

## 10. Looping pada DataFrame

Looping menjalankan instruksi berulang untuk setiap elemen atau baris.

### 10.1 Looping sederhana pada kolom

``` python
for amount in df["amount"]:
    print(amount)
```

### 10.2 Looping menggunakan `iterrows()`

``` python
for index, row in df.iterrows():
    print(row["customer"], row["amount"])
```

### 10.3 Looping dengan conditional

``` python
for index, row in df.iterrows():

    if row["amount"] > 400000:
        print(row["customer"], "High Value")
    else:
        print(row["customer"], "Regular")
```

**Catatan:** Looping penting untuk dipahami karena masuk kisi-kisi LC2.
Namun, untuk operasi DataFrame secara umum, vectorization seperti
`np.where()` biasanya lebih efisien daripada looping per baris.

## 11. Datetime Processing dengan Pandas

### 11.1 Membuat data tanggal

``` python
data = {
    "order_id": ["O001", "O002", "O003"],
    "order_date": [
        "2026-01-05",
        "2026-02-10",
        "2026-03-15"
    ]
}

df = pd.DataFrame(data)
```

### 11.2 Mengubah string menjadi datetime

``` python
df["order_date"] = pd.to_datetime(df["order_date"])
```

Periksa hasilnya:

``` python
df.info()
```

Tipe data biasanya menjadi `datetime64[ns]`.

### 11.3 Mengambil tahun, bulan, dan hari

``` python
df["year"] = df["order_date"].dt.year
df["month"] = df["order_date"].dt.month
df["day"] = df["order_date"].dt.day
```

Padanan BigQuery: `EXTRACT()`.

### 11.4 Date Difference

``` python
today = pd.Timestamp("2026-04-01")

df["days_difference"] = (
    today - df["order_date"]
).dt.days
```

Padanan BigQuery: `DATE_DIFF()`.

### 11.5 Date Add

Menambahkan tujuh hari:

``` python
df["next_week"] = (
    df["order_date"] + pd.Timedelta(days=7)
)
```

Menambahkan satu bulan kalender:

``` python
df["next_month"] = (
    df["order_date"] + pd.DateOffset(months=1)
)
```

`Timedelta` cocok untuk durasi tetap seperti hari atau jam. `DateOffset`
cocok untuk interval kalender seperti bulan atau tahun.

## 12. Perbandingan Datetime BigQuery dan Pandas

  ------------------------------------------------------------------------------------------
  Kebutuhan               BigQuery                             Pandas
  ----------------------- ------------------------------------ -----------------------------
  Mengubah nilai menjadi  `DATE()` / `CAST()`                  `pd.to_datetime()`
  tanggal                                                      

  Mengambil tahun         `EXTRACT(YEAR FROM date)`            `.dt.year`

  Mengambil bulan         `EXTRACT(MONTH FROM date)`           `.dt.month`

  Mengambil hari          `EXTRACT(DAY FROM date)`             `.dt.day`

  Selisih dua tanggal     `DATE_DIFF()`                        Pengurangan datetime lalu
                                                               `.dt.days`

  Menambah 7 hari         `DATE_ADD(date, INTERVAL 7 DAY)`     `+ pd.Timedelta(days=7)`

  Menambah 1 bulan        `DATE_ADD(date, INTERVAL 1 MONTH)`   `+ pd.DateOffset(months=1)`
  ------------------------------------------------------------------------------------------

Contoh BigQuery:

``` sql
SELECT
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(MONTH FROM order_date) AS month,
    DATE_DIFF(end_date, order_date, DAY) AS duration,
    DATE_ADD(order_date, INTERVAL 7 DAY) AS next_week
FROM `my-project.sales.transactions`;
```

## 13. Cheat Sheet LC2

### SQL BigQuery --- Aggregation

``` sql
SELECT
    category,
    COUNT(*) AS total_orders,
    SUM(amount) AS revenue,
    AVG(amount) AS average_amount,
    MIN(amount) AS minimum_amount,
    MAX(amount) AS maximum_amount
FROM `my-project.sales.transactions`
GROUP BY category
HAVING SUM(amount) > 100000
ORDER BY revenue DESC;
```

### SQL BigQuery --- Subquery

``` sql
SELECT *
FROM `my-project.sales.transactions`
WHERE amount > (
    SELECT AVG(amount)
    FROM `my-project.sales.transactions`
);
```

### SQL BigQuery --- Datetime

``` sql
SELECT
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(MONTH FROM order_date) AS month,
    DATE_ADD(order_date, INTERVAL 7 DAY) AS next_week
FROM `my-project.sales.transactions`;
```

### Pandas

``` python
# Filtering
df.query("amount > 300000")

# Grouping dan aggregation
df.groupby("category")["amount"].sum()

# Conditional
df["status"] = np.where(
    df["amount"] > 400000,
    "High",
    "Regular"
)

# Datetime conversion
df["date"] = pd.to_datetime(df["date"])

# Extract
df["year"] = df["date"].dt.year
df["month"] = df["date"].dt.month

# Date difference
(pd.Timestamp("2026-04-01") - df["date"]).dt.days

# Date add
df["date"] + pd.Timedelta(days=7)
```

## 14. Strategi Mengerjakan LC2 di Google Colab

Mekanisme LC2 yang dibagikan:

1.  Clone repository dari GitHub.
2.  Buka folder hasil clone di VS Code.
3.  Baca instruksi soal dengan teliti.
4.  Karena dataset berasal dari BigQuery, kerjakan query dan pemrosesan
    di Google Colab.
5.  Gunakan code template koneksi BigQuery yang disediakan.
6.  Selesaikan soal sesuai instruksi.
7.  Download notebook Colab (`.ipynb`).
8.  Simpan notebook ke folder repository yang sudah dibuka di VS Code.
9.  Jalankan `git add`, `git commit`, dan `git push`.

Contoh perintah Git:

``` bash
git status

git add nama_notebook.ipynb

git commit -m "Complete LC2"

git push
```

Pastikan file notebook sudah tersimpan di folder repository yang benar
sebelum melakukan commit dan push.

## 15. Checklist Kesiapan LC2

### SQL BigQuery

-   [ ] Memahami format `Project.Dataset.Table`.
-   [ ] Memahami penggunaan backticks.
-   [ ] Menggunakan `SELECT`, `WHERE`, dan `ORDER BY`.
-   [ ] Memahami `COUNT`, `SUM`, `AVG`, `MIN`, dan `MAX`.
-   [ ] Menggunakan `GROUP BY`.
-   [ ] Memahami perbedaan `WHERE` dan `HAVING`.
-   [ ] Membuat subquery untuk membandingkan nilai dengan rata-rata.
-   [ ] Menggunakan `EXTRACT`.
-   [ ] Menggunakan `DATE_DIFF`.
-   [ ] Menggunakan `DATE_ADD`.

### Pandas

-   [ ] Membuat dan membaca DataFrame.
-   [ ] Memahami `head()`, `shape`, `info()`, dan `describe()`.
-   [ ] Melakukan filtering dengan Boolean indexing.
-   [ ] Melakukan filtering dengan `.query()`.
-   [ ] Menggunakan `groupby()` dan aggregation.
-   [ ] Menggunakan conditional dengan `np.where()`.
-   [ ] Memahami `np.select()` dan `.apply()`.
-   [ ] Melakukan looping dengan `for` dan `iterrows()`.

### Datetime Pandas

-   [ ] Mengubah kolom menjadi datetime dengan `pd.to_datetime()`.
-   [ ] Mengambil tahun, bulan, dan hari dengan `.dt`.
-   [ ] Menghitung selisih tanggal.
-   [ ] Menambahkan hari dengan `pd.Timedelta()`.
-   [ ] Menambahkan bulan dengan `pd.DateOffset()`.

### Mekanisme pengerjaan

-   [ ] Bisa melakukan autentikasi Google Colab.
-   [ ] Bisa membuat BigQuery client.
-   [ ] Bisa menjalankan query dengan `client.query()`.
-   [ ] Bisa mengubah hasil query dengan `.to_dataframe()`.
-   [ ] Bisa mengunduh notebook `.ipynb`.
-   [ ] Bisa melakukan Git add, commit, dan push.

## 16. Urutan Simulasi yang Disarankan

  -----------------------------------------------------------------------
  Tahap                   Materi                  Target
  ----------------------- ----------------------- -----------------------
  1                       SQL Aggregation         Menguasai `GROUP BY`,
                                                  `HAVING`, dan
                                                  aggregation

  2                       SQL Subquery            Menggunakan hasil query
                                                  dalam query lain

  3                       Pandas DataFrame        Membaca, memfilter, dan
                                                  mengelompokkan data

  4                       Conditional & Looping   Membuat klasifikasi dan
                                                  iterasi data

  5                       Datetime SQL            Menguasai `EXTRACT`,
                                                  `DATE_DIFF`, `DATE_ADD`

  6                       Datetime Pandas         Mengolah tanggal dengan
                                                  `.dt`, `Timedelta`, dan
                                                  `DateOffset`

  7                       Full Simulation         Menyelesaikan soal
                                                  campuran seperti Live
                                                  Code
  -----------------------------------------------------------------------

**Strategi utama:** gunakan satu dataset untuk latihan SQL dan Pandas.
Kerjakan satu requirement dengan SQL terlebih dahulu, lalu coba
selesaikan kebutuhan analisis yang sama menggunakan Pandas jika
memungkinkan. Dengan begitu, kamu memahami hubungan antara DQL BigQuery
dan DataFrame.

------------------------------------------------------------------------

## Kesimpulan

LC2 bukan hanya ujian SQL. Kamu perlu memahami alur kerja analisis data
dari BigQuery sampai Pandas:

``` text
BigQuery
   |
   v
SQL Query (DQL)
   |
   v
Hasil Query
   |
   v
Pandas DataFrame
   |
   v
Filtering, Aggregation,
Conditional, Looping, Datetime
   |
   v
Hasil Analisis
```

Fokus utama persiapan adalah **SQL Aggregation, Subquery, Pandas
DataFrame, Conditional/Looping, dan Datetime Processing**. Window
Function tidak perlu menjadi prioritas karena tidak tercantum dalam
kisi-kisi LC2 yang dibagikan.




==============



# Panduan Mencari Data Kosong dan Spasi di Google Colab

Dokumen ini berisi panduan praktis menggunakan **Pandas** untuk mendeteksi data kosong (`Missing Values`) dan mendeteksi data teks yang tidak sengaja diawali oleh spasi (`Leading Spaces`).

---

## 1. Persiapan Data Dummy

Jalankan kode di bawah ini di cell Google Colab Anda untuk membuat tabel contoh (*DataFrame*) yang memiliki masalah data kosong dan spasi di depan.

```python
import pandas as pd
import numpy as np

# Membuat data dummy
data = {
    'Nama': [' Budi', 'Andi', None, '  Siti', 'Rendi'],
    'Kota': ['Jakarta', ' Bandung', 'Surabaya', None, 'Medan'],
    'Usia': [25, 30, np.nan, 22, 28]
}

df = pd.DataFrame(data)
print("--- Data Dummy Awal ---")
print(df)
```

**Kondisi Data Saat Ini:**
- Kolom **Nama**: `' Budi'` (1 spasi depan), `'  Siti'` (2 spasi depan), dan `None` (kosong).
- Kolom **Kota**: `' Bandung'` (1 spasi depan) dan `None` (kosong).
- Kolom **Usia**: `NaN` (kosong).

---

## 2. Mencari Kolom & Data yang Kosong (`Missing Values`)

Untuk mendeteksi nilai `NaN` atau `None`, gunakan fungsi `.isnull()`.

### A. Menghitung Jumlah Data Kosong Per Kolom
Kode ini digunakan untuk melihat ringkasan berapa banyak data yang hilang di setiap kolom:
```python
df.isnull().sum()
```

### B. Menampilkan Baris yang Kosong di Kolom Tertentu
Jika Anda hanya ingin melihat baris yang kolom **Nama**-nya kosong:
```python
df[df['Nama'].isnull()]
```

### C. Menampilkan Semua Baris yang Mengandung Data Kosong
Jika Anda ingin menyaring baris yang memiliki bolong/kosong di kolom mana saja:
```python
df[df.isnull().any(axis=1)]
```

---

## 3. Mendeteksi Spasi di Depan Teks (`Leading Spaces`)

Teks yang memiliki spasi di awal sering kali tidak terdeteksi sebagai data kosong, tetapi bisa membuat pencarian data menjadi eror (misal: `"Andi"` tidak sama dengan `" Andi"`).

### A. Mendeteksi Spasi Depan pada Kolom 'Nama'
Gunakan `.str.startswith(' ', na=False)` untuk menyaring teks yang diawali karakter spasi. Parameter `na=False` wajib diisi agar data yang benar-benar kosong tidak menyebabkan eror.
```python
spasi_nama = df[df['Nama'].str.startswith(' ', na=False)]
print(spasi_nama)
```
*Hasil: Menampilkan data **Budi** dan **Siti**.*

### B. Mendeteksi Spasi Depan pada Kolom 'Kota'
```python
spasi_kota = df[df['Kota'].str.startswith(' ', na=False)]
print(spasi_kota)
```
*Hasil: Menampilkan data **Andi** (karena kotanya ditulis `' Bandung'`).*

---

## 4. Solusi Pembersihan Data (Bonus)

Untuk menghapus spasi di awal (*leading*) maupun di akhir (*trailing*) teks secara otomatis, Anda bisa memanfaatkan fungsi `.str.strip()`:

```python
# Membersihkan kolom teks
df['Nama'] = df['Nama'].str.strip()
df['Kota'] = df['Kota'].str.strip()

print("--- Data Setelah Dibersihkan ---")
print(df)
```



=====


# Panduan Lengkap Penggunaan Subquery di SQL (Dengan Output)

Dokumen ini menjelaskan konsep, jenis, contoh penerapan, serta **hasil (output)** dari penggunaan **Subquery** (Query di dalam Query) pada database SQL.

---

## 1. Persiapan Data Dummy

Untuk mempermudah pemahaman, kita akan menggunakan dua tabel referensi berikut: `departemen` dan `karyawan`.

### Tabel: `departemen`

| id_dept | nama_dept | kota |
| :--- | :--- | :--- |
| 1 | IT | Jakarta |
| 2 | HRD | Bandung |
| 3 | Marketing | Jakarta |

### Tabel: `karyawan`

| id_karyawan | nama | id_dept | gaji |
| :--- | :--- | :--- | :--- |
| 101 | Andi | 1 | 9000000 |
| 102 | Budi | 1 | 12000000 |
| 103 | Cici | 2 | 7000000 |
| 104 | Dodi | 3 | 8500000 |
| 105 | Eka | 2 | 6500000 |

---

## 2. Jenis Subquery, Contoh Kode, & Output

### A. Scalar Subquery (Hasil Nilai Tunggal)
Subquery ini hanya menghasilkan **satu nilai (satu baris dan satu kolom)**. Biasanya dipadukan dengan operator seperti `=`, `>`, `<`, atau `!=`.

**Kasus:** Menampilkan karyawan yang gajinya **di atas rata-rata** gaji seluruh karyawan (Rata-rata gaji dari data di atas adalah `8.600.000`).

```sql
SELECT nama, gaji 
FROM karyawan
WHERE gaji > (SELECT AVG(gaji) FROM karyawan);
```

#### 📊 Output:

| nama | gaji |
| :--- | :--- |
| Andi | 9000000 |
| Budi | 12000000 |

*Keterangan: Andi dan Budi masuk ke dalam output karena gaji mereka berdua di atas 8.600.000.*

---

### B. Multiple-Row Subquery (Hasil Banyak Baris)
Subquery ini menghasilkan **satu kolom tetapi berisi beberapa baris nilai**. Digunakan bersama operator pencocokan massal seperti `IN`, `ANY`, atau `ALL`.

**Kasus:** Menampilkan semua karyawan yang bekerja di departemen yang **berlokasi di Jakarta** (Subquery mendeteksi id_dept `1` dan `3`).

```sql
SELECT nama, id_dept 
FROM karyawan
WHERE id_dept IN (SELECT id_dept FROM departemen WHERE kota = 'Jakarta');
```

#### 📊 Output:

| nama | id_dept |
| :--- | :--- |
| Andi | 1 |
| Budi | 1 |
| Dodi | 3 |

*Keterangan: Cici dan Eka tidak muncul karena departemen mereka (HRD) berada di Bandung.*

---

### C. Subquery di dalam Klausa FROM (Inline View)
Subquery ini bertindak sebagai **tabel bayangan/sementara** yang hasilnya bisa kita kueri kembali di query utama. Wajib menggunakan nama alias (misal: `AS tabel_sementara`).

**Kasus:** Menghitung selisih gaji setiap karyawan di departemen IT (id_dept = 1) dengan rata-rata gaji khusus departemen IT itu sendiri (`10.500.000`).

```sql
SELECT k.nama, k.gaji, rata_rata.gaji_it, (k.gaji - rata_rata.gaji_it) AS selisih
FROM karyawan k,
     (SELECT AVG(gaji) AS gaji_it FROM karyawan WHERE id_dept = 1) AS rata_rata
WHERE k.id_dept = 1;
```

#### 📊 Output:

| nama | gaji | gaji_it | selisih |
| :--- | :--- | :--- | :--- |
| Andi | 9000000 | 10500000 | -1500000 |
| Budi | 12000000 | 10500000 | 1500000 |

---

### D. Correlated Subquery (Subquery Saling Terkait)
Subquery yang nilai di dalamnya **bergantung langsung pada kolom di query utama**. Proses pencariannya berjalan baris demi baris secara dinamis.

**Kasus:** Menampilkan karyawan yang gajinya **lebih tinggi dari rata-rata gaji di departemen tempat dia bekerja sendiri**.

```sql
SELECT k1.nama, k1.id_dept, k1.gaji
FROM karyawan k1
WHERE k1.gaji > (SELECT AVG(k2.gaji) 
                 FROM karyawan k2 
                 WHERE k2.id_dept = k1.id_dept);
```

#### 📊 Output:

| nama | id_dept | gaji |
| :--- | :--- | :--- |
| Budi | 1 | 12000000 |
| Cici | 2 | 7000000 |

*Keterangan:*
- *Di Dept 1 (IT), rata-ratanya 10,5jt $\rightarrow$ Budi (12jt) masuk.*
- *Di Dept 2 (HRD), rata-ratanya 6,75jt $\rightarrow$ Cici (7jt) masuk.*
- *Di Dept 3 (Marketing), hanya ada Dodi sendirian, sehingga gajinya sama dengan rata-rata (tidak lebih besar) $\rightarrow$ Dodi tidak masuk.*
