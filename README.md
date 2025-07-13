# Project-Data-Analysis-for-B2B-Retail-Customer-Analytics-Report
 Project dari DQLab Melakukan analisis Performance dengan menggunakan MySQL

xyz.com adalah perusahan rintisan B2B yang menjual berbagai produk tidak langsung kepada end user tetapi ke bisnis/perusahaan lainnya. Sebagai data-driven company, maka setiap pengambilan keputusan di xyz.com selalu berdasarkan data. Setiap quarter xyz.com akan mengadakan townhall dimana seluruh atau perwakilan divisi akan berkumpul untuk me-review performance perusahaan selama quarter terakhir.

Beberapa pertanyaan yang dijawab pada customer analytics report ini
- Bagaimana pertumbuhan penjualan saat ini?
- Apakah jumlah customers xyz.com semakin bertambah ?
- Dan seberapa banyak customers tersebut yang sudah melakukan transaksi?
- Category produk apa saja yang paling banyak dibeli oleh customers?
- Seberapa banyak customers yang tetap aktif bertransaksi?
Tabel yang akan digunakan pada project kali ini adalah sebagai berikut (Asumsikan tahun yang sedang berjalan adalah tahun 2014)
<img width="422" height="183" alt="image" src="https://github.com/user-attachments/assets/9abf5f7f-47d0-4678-84ea-dbfaee9f9adb" />

Tabel orders_1 : Berisi data terkait transaksi penjualan periode quarter 1 (Jan – Mar 2004)

Tabel Orders_2 : Berisi data terkait transaksi penjualan periode quarter 2 (Apr – Jun 2004)

Tabel Customer : Berisi data profil customer yang mendaftar menjadi customer xyz.com

Memahami table

Sebelum memulai menyusun query SQL dan membuat Analisa dari hasil query, hal pertama yang perlu dilakukan adalah menjadi familiar dengan tabel yang akan digunakan. Hal ini akan sangat berguna dalam menentukan kolom mana sekiranya berkaitan dengan problem yang akan dianalisa, dan proses manipulasi data apa yang sekiranya perlu dilakukan untuk kolom – kolom tersebut, karena tidak semua kolom pada tabel perlu untuk digunakan.

    SELECT * FROM orders_1 limit 5;
    SELECT * FROM orders_2 limit 5;
    SELECT * FROM customer limit 5;
Output:

<img width="665" height="396" alt="image" src="https://github.com/user-attachments/assets/00645272-d9b1-4e3d-a7b8-f62690484390" />

## 1. Total Penjualan dan Revenue pada Quarter-1 (Jan, Feb, Mar) dan Quarter-2 (Apr,Mei,Jun)

    SELECT
      SUM(quantity) AS total_penjualan,
      SUM(quantity * priceeach) AS revenue
    FROM orders_1
    WHERE status = 'Shipped';

    SELECT
      SUM(quantity) AS total_penjualan,
      SUM(quantity * priceeach) AS revenue
    FROM orders_2
    WHERE status = 'Shipped';

Output:

<img width="182" height="145" alt="image" src="https://github.com/user-attachments/assets/ccc1093a-c154-487b-823e-0bf7d0bc2333" />
