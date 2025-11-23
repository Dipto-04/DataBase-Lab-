# DataBase-Lab-


CREATE TABLE Company (
    company_id INT AUTO_INCREMENT PRIMARY KEY,
    company_name VARCHAR(100),
    jurisdiction VARCHAR(50),
    risk_score INT,
    balance_millions DECIMAL(10,2)
);

CREATE TABLE Transaction (
    transfer_id INT AUTO_INCREMENT PRIMARY KEY,
    from_company_id INT,
    to_company_id INT,
    amount DECIMAL(10,2) CHECK (amount > 0),
    transfer_date DATE,
    CONSTRAINT fk_from FOREIGN KEY (from_company_id) REFERENCES Company(company_id),
    CONSTRAINT fk_to FOREIGN KEY (to_company_id) REFERENCES Company(company_id),
    CONSTRAINT chk_not_self CHECK (from_company_id <> to_company_id)
);







2

WITH totals AS (
    SELECT 
        c.company_id,
        SUM(IF(t.from_company_id = c.company_id, t.amount, 0) +
            IF(t.to_company_id = c.company_id, t.amount, 0)) AS total_amt
    FROM Company c
    LEFT JOIN Transaction t
        ON c.company_id IN (t.from_company_id, t.to_company_id)
    GROUP BY c.company_id
),
ranked AS (
    SELECT company_id, total_amt,
           DENSE_RANK() OVER (ORDER BY total_amt DESC) AS rnk
    FROM totals
)
SELECT company_id, total_amt
FROM ranked
WHERE rnk IN (2,4);






3
WITH RECURSIVE paths AS (
    SELECT 
        from_company_id AS source,
        from_company_id AS current,
        to_company_id AS next,
        CONCAT(from_company_id, '->', to_company_id) AS path
    FROM Transaction
    UNION ALL
    SELECT 
        p.source,
        p.next,
        t.to_company_id,
        CONCAT(p.path, '->', t.to_company_id)
    FROM paths p
    JOIN Transaction t ON p.next = t.from_company_id
    WHERE t.to_company_id <> p.source   -- avoid infinite loop until return
),
loops AS (
    SELECT DISTINCT source AS company, path
    FROM paths
    WHERE next = source   -- loop detected
)
SELECT * FROM loops;
