Anggota Kelompok 6: Aisyah Imani Khoirunnisa (24031554020)
                    Nasliyatul Intani (24031554097)
                    Devita Nur Aisyah (24031554125)

1. Definisi proses bisnis:
Proses bisnis yang digunakan adalah proses analisis penjualan produk pada perusahaan distribusi. Proses ini berfokus pada pengolahan data transaksi penjualan yang melibatkan pelanggan, produk, karyawan, pengiriman, serta waktu transaksi. Tujuan utamanya adalah menghasilkan informasi analitik untuk mengetahui performa penjualan dari berbagai dimensi yang mendukung kebutuhan pengambilan keputusan.

2. Deskripsi proses bisnis:
Proses bisnis dimulai ketika pelanggan melakukan pemesanan produk yang kemudian dicatat sebagai transaksi penjualan. Setiap transaksi memuat detail seperti produk yang dibeli, jumlah, harga satuan, diskon, serta biaya pengiriman, dan dilayani oleh karyawan serta dikirim melalui jasa pengirim tertentu. Data transaksi tersebut selanjutnya digabungkan dengan data dimensi pelanggan, produk, karyawan, pengirim, dan waktu untuk membentuk data analitik yang terstruktur. Melalui pengolahan ini, perusahaan dapat menganalisis penjualan berdasarkan berbagai perspektif, seperti penjualan per produk, kontribusi pelanggan, kinerja karyawan, serta tren penjualan berdasarkan waktu, sehingga membantu manajemen dalam membuat keputusan strategis yang lebih tepat.

3. Query Dimension Tables:

CREATE TABLE dim_customer AS
SELECT 
    c.customer_id,
    c.company_name,
    c.contact_name,
    c.contact_title,
    c.city,
    c.region,
    c.country,
    cd.customer_type_id,
    d.customer_desc
FROM customers c
LEFT JOIN customer_customer_demo cd 
    ON c.customer_id = cd.customer_id
LEFT JOIN customer_demographics d 
    ON cd.customer_type_id = d.customer_type_id;

ALTER TABLE dim_customer
ADD CONSTRAINT pk_dim_customer PRIMARY KEY (customer_id);

CREATE TABLE dim_product AS
SELECT 
    p.product_id,
    p.product_name,
    p.quantity_per_unit,
    p.unit_price,
    p.units_in_stock,
    p.discontinued,
    c.category_name,
    s.company_name AS supplier_name,
    s.country AS supplier_country
FROM products p
LEFT JOIN categories c 
    ON p.category_id = c.category_id
LEFT JOIN suppliers s 
    ON p.supplier_id = s.supplier_id;

ALTER TABLE dim_product
ADD CONSTRAINT pk_dim_product PRIMARY KEY (product_id);

CREATE TABLE dim_employee AS
SELECT 
    employee_id,
    first_name,
    last_name,
    title,
    city,
    region,
    country,
    hire_date
FROM employees;

ALTER TABLE dim_employee
ADD CONSTRAINT pk_dim_employee PRIMARY KEY (employee_id);

CREATE TABLE dim_shipper AS
SELECT 
    shipper_id,
    company_name,
    phone
FROM shippers;

ALTER TABLE dim_shipper
ADD CONSTRAINT pk_dim_shipper PRIMARY KEY (shipper_id);

CREATE TABLE dim_date AS
SELECT DISTINCT
    order_date AS date_id,
    EXTRACT(DAY FROM order_date) AS day,
    EXTRACT(MONTH FROM order_date) AS month,
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(QUARTER FROM order_date) AS quarter
FROM orders
WHERE order_date IS NOT NULL;

ALTER TABLE dim_date
ADD CONSTRAINT pk_dim_date PRIMARY KEY (date_id);

4. Query Fact Tables:

CREATE TABLE fact_sales AS
SELECT 
    o.order_id,
    o.customer_id,
    o.employee_id,
    o.ship_via AS shipper_id,
    o.order_date AS date_id,
    od.product_id,
    od.unit_price,
    od.quantity,
    od.discount,
    (od.unit_price * od.quantity * (1 - od.discount)) AS total_sales,
    o.freight
FROM orders o
JOIN order_details od 
    ON o.order_id = od.order_id;

ALTER TABLE fact_sales
ADD CONSTRAINT pk_fact_sales PRIMARY KEY (order_id, product_id);

ALTER TABLE fact_sales
ADD CONSTRAINT fk_fact_customer
FOREIGN KEY (customer_id)
REFERENCES dim_customer(customer_id);

ALTER TABLE fact_sales
ADD CONSTRAINT fk_fact_product
FOREIGN KEY (product_id)
REFERENCES dim_product(product_id);

ALTER TABLE fact_sales
ADD CONSTRAINT fk_fact_employee
FOREIGN KEY (employee_id)
REFERENCES dim_employee(employee_id);

ALTER TABLE fact_sales
ADD CONSTRAINT fk_fact_date
FOREIGN KEY (date_id)
REFERENCES dim_date(date_id);

ALTER TABLE fact_sales
ADD CONSTRAINT fk_fact_shipper
FOREIGN KEY (shipper_id)
REFERENCES dim_shipper(shipper_id);

5. Dokumentasi Tables (Dimension dan Fact) dalam sebuah view spesifikasi:

CREATE VIEW KLP06_KLSD AS
SELECT
    fs.order_id,
    dc.customer_id,
    dc.company_name,
    dp.product_id,
    dp.product_name,
    de.employee_id,
    de.first_name,
    de.last_name,
    ds.shipper_id,
    ds.company_name AS shipper_name,
    dd.year,
    dd.month,
    dd.quarter,
    fs.quantity,
    fs.unit_price,
    fs.discount,
    fs.total_sales,
    fs.freight
FROM fact_sales fs
JOIN dim_customer dc 
    ON fs.customer_id = dc.customer_id
JOIN dim_product dp 
    ON fs.product_id = dp.product_id
JOIN dim_employee de 
    ON fs.employee_id = de.employee_id
JOIN dim_shipper ds 
    ON fs.shipper_id = ds.shipper_id
JOIN dim_date dd 
    ON fs.date_id = dd.date_id;
