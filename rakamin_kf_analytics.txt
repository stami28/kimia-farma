SELECT DISTINCT
    t.transaction_id,
    t.date,
    t.branch_id AS transaction_branch_id,
    kc.branch_category,
    kc.branch_name,
    kc.kota,
    kc.provinsi,
    t.customer_name,
    t.product_id AS transaction_product_id,
    p.product_name AS transaction_product_name,
    p.product_category,
    t.price,
    t.discount_percentage,
    t.rating,
    CASE 
        WHEN p.price <= 50000 THEN 0.10
        WHEN p.price > 50000 AND p.price <= 100000 THEN 0.15
        WHEN p.price > 100000 AND p.price <= 300000 THEN 0.20
        WHEN p.price > 300000 AND p.price <= 500000 THEN 0.25
        ELSE 0.30
    END AS persentase_gross_laba,
    t.price - (t.price * t.discount_percentage / 100) AS nett_sales,
    (t.price - (t.price * t.discount_percentage / 100)) * 
    CASE 
        WHEN p.price <= 50000 THEN 0.10
        WHEN p.price > 50000 AND p.price <= 100000 THEN 0.15
        WHEN p.price > 100000 AND p.price <= 300000 THEN 0.20
        WHEN p.price > 300000 AND p.price <= 500000 THEN 0.25
        ELSE 0.30
    END AS nett_profit
FROM 
    rakamin-kf-analytics-1.kimia_farma.kf_final_transaction t
JOIN 
    rakamin-kf-analytics-1.kimia_farma.kf_kantor_cabang kc
ON 
    t.branch_id = kc.branch_id
JOIN 
    rakamin-kf-analytics-1.kimia_farma.kf_product p
ON 
    t.product_id = p.product_id
JOIN 
    rakamin-kf-analytics-1.kimia_farma.kf_inventory i
ON 
    t.branch_id = i.branch_id
    AND t.product_id = i.product_id;
