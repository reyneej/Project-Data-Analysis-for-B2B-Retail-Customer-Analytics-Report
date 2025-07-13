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

## 2. Menghitung persentasi keseluruhan penjualan
- Pilih kolom “orderNumber”, “status”, “quantity”, dan “priceEach” dari tabel orders_1, lalu tambahkan sebuah kolom baru bernama “quarter” dengan nilai tetap 1. Lakukan hal yang sama untuk tabel orders_2, namun isikan kolom “quarter” dengan nilai 2. Setelah itu, gabungkan kedua hasil tersebut.

- Gunakan hasil penggabungan ini sebagai subquery dan beri alias “tabel_a”.

- Dari tabel_a, hitung total nilai dari kolom quantity menggunakan fungsi agregat SUM() dan beri nama hasilnya “total_penjualan”. Selanjutnya, kalikan nilai quantity dengan priceEach, lalu jumlahkan seluruh hasil perkaliannya dan beri nama “revenue”.

- Hanya data dengan status “Shipped” yang diikutkan dalam perhitungan dengan menerapkan filter pada kolom status.

- Terakhir, kelompokkan hasil total_penjualan berdasarkan kolom quarter.

    SELECT 
  	  quarter,
  	  sum(quantity) as total_penjualan,
  	  sum(quantity*priceeach) as revenue 
    FROM 
  	  (select ordernumber,status,quantity,priceeach, 1 as quarter from orders_1
    UNION
  	  select ordernumber,status,quantity,priceeach, 2 as quarter from orders_2) as tabel_a 
    WHERE status = 'Shipped'
    GROUP BY quarter;
Output:

<img width="237" height="85" alt="image" src="https://github.com/user-attachments/assets/01bbb995-95ea-40c7-9cce-b54cbc6f53f9" />

Untuk project ini, perhitungan pertumbuhan penjualan akan dilakukan secara manual menggunakan formula:

%Growth Penjualan = (6717 – 8694)/8694 = -22%
%Growth Revenue = (607548320 – 799579310)/ 799579310 = -24%

## 3. Apakah jumlah customers xyz.com semakin bertambah?
Penambahan jumlah customers dapat diukur dengan membandingkan total jumlah customers yang registrasi di periode saat ini dengan total jumlah customers yang registrasi diakhir periode sebelumnya.

    select  QUARTER(createDate) as quarter, count(distinct customerID) as total_customers
    from (select customerID, createDate, QUARTER(createDate) quarter 
      from customer
    where createDate BETWEEN '2004-01-01' and '2004-06-30') as tabel_b
    group by QUARTER(createDate);
Output:

<img width="162" height="84" alt="image" src="https://github.com/user-attachments/assets/8fdc2073-4502-497b-b86b-cccfd5244204" />


## 4. Seberapa banyak customers tersebut yang sudah melakukan transaksi?

Problem ini merupakan kelanjutan dari problem sebelumnya yaitu dari sejumlah customer yang registrasi di periode quarter-1 dan quarter-2, berapa banyak yang sudah melakukan transaksi.

    select QUARTER(CreateDate) quarter, count(distinct customerID) total_customers
    from (Select  customerID, createDate, QUARTER(CreateDate) quarter from customer
    where createDate between '2004-01-01' and '2004-06-30' AND customerID in (select distinct customerID from orders_1 union   select distinct customerID from orders_2)) as tabel_b
    group by QUARTER(CreateDate);

Output:

<img width="168" height="81" alt="image" src="https://github.com/user-attachments/assets/27aef7d1-20d3-4be4-a7ce-1fc408e472fa" />

## 5. Category produk apa saja yang paling banyak di-order oleh customers di Quarter-2?

Untuk mengetahui kategori produk yang paling banyak dibeli, maka dapat dilakukan dengan menghitung total order dan jumlah penjualan dari setiap kategori produk.

    select LEFT(productCode,3) AS categoryID,COUNT(DISTINCT orderNumber) AS total_order, SUM(quantity) AS total_penjualan
    from (select productCode,  orderNumber, quantity, status 
	      from orders_2
	      where status = 'Shipped') AS tabel_c
    group by categoryID
    order by total_order DESC;
Output:

<img width="263" height="168" alt="image" src="https://github.com/user-attachments/assets/3c89ac7f-a0c1-460f-9df2-a8c2246c18de" />

## 6. Seberapa banyak customers yang tetap aktif bertransaksi setelah transaksi pertamanya?

Mengetahui seberapa banyak customers yang tetap aktif menunjukkan apakah xyz.com tetap digemari oleh customers untuk memesan kebutuhan bisnis mereka. Hal ini juga dapat menjadi dasar bagi tim product dan business untuk pengembangan product dan business kedepannya. Adapun metrik yang digunakan disebut retention cohort. Oleh karena baru terdapat 2 periode yang Quarter 1 dan Quarter 2, maka retention yang dapat dihitung adalah retention dari customers yang berbelanja di Quarter 1 dan kembali berbelanja di Quarter 2, sedangkan untuk customers yang berbelanja di Quarter 2 baru bisa dihitung retentionnya di Quarter 3.

    SELECT COUNT(DISTINCT customerID) as total_customers FROM orders_1;
    SELECT '1' as quarter, (COUNT(DISTINCT customerID)*100)/25 AS Q2
    from orders_1 where customerID in (select DISTINCT customerID as total_customers FROM orders_2);

Output:

<img width="121" height="142" alt="image" src="https://github.com/user-attachments/assets/24b410bf-f475-4a3c-981b-61762d41c1a7" />

# Kesimpulan
- Performance xyz.com menurun signifikan di quarter ke-2, terlihat dari nilai penjualan dan revenue yang drop hingga 20% dan 24%,
perolehan customer baru juga tidak terlalu baik, dan sedikit menurun dibandingkan quarter sebelumnya.
- Ketertarikan customer baru untuk berbelanja di xyz.com masih kurang, hanya sekitar 56% saja yang sudah bertransaksi. Disarankan tim Produk untuk perlu mempelajari behaviour customer dan melakukan product improvement, sehingga conversion rate (register to transaction) dapat meningkat.
Produk kategori S18 dan S24 berkontribusi sekitar 50% dari total order dan 60% dari total penjualan, sehingga xyz.com sebaiknya fokus untuk pengembangan category S18 dan S24.
- Retention rate customer xyz.com juga sangat rendah yaitu hanya 24%, artinya banyak customer yang sudah bertransaksi di quarter-1 tidak kembali melakukan order di quarter ke-2 (no repeat order).
- xyz.com mengalami pertumbuhan negatif di quarter ke-2 dan perlu melakukan banyak improvement baik itu di sisi produk dan bisnis marketing, jika ingin mencapai target dan positif growth di quarter ke-3. Rendahnya retention rate dan conversion rate bisa menjadi diagnosa awal bahwa customer tidak tertarik/kurang puas/kecewa berbelanja di xyz.com.

