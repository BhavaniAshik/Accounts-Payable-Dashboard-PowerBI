/* =========================================
   ACCOUNTS PAYABLE DASHBOARD – SQL QUERIES
   ========================================= */

/* ---------- KPI QUERIES ---------- */

/* Total Invoice Amount */
SELECT 
    SUM(InvoiceAmount) AS Total_Invoice_Amount
FROM Invoices;

/* Total Paid Amount */
SELECT 
    SUM(PaidAmount) AS Total_Paid_Amount
FROM Invoices;

/* Outstanding Amount */
SELECT 
    SUM(InvoiceAmount - PaidAmount) AS Outstanding_Amount
FROM Invoices;

/* Overdue Amount */
SELECT 
    SUM(InvoiceAmount - PaidAmount) AS Overdue_Amount
FROM Invoices
WHERE DueDate < GETDATE()
  AND PaymentDate IS NULL;

/* On-Time Payment Percentage */
SELECT 
    CAST(
        COUNT(CASE WHEN PaymentDate <= DueDate THEN 1 END) * 100.0 
        / COUNT(*) 
        AS DECIMAL(5,2)
    ) AS On_Time_Payment_Percentage
FROM Invoices
WHERE PaymentDate IS NOT NULL;

/* ---------- VISUAL QUERIES ---------- */

/* Outstanding Amount by Vendor */
SELECT 
    V.VendorName,
    SUM(I.InvoiceAmount - I.PaidAmount) AS Outstanding_Amount
FROM Invoices I
JOIN DimVendor V 
    ON I.VendorID = V.VendorID
GROUP BY V.VendorName
ORDER BY Outstanding_Amount DESC;

/* Top Vendors by Outstanding Amount */
SELECT TOP 10
    V.VendorName,
    SUM(I.InvoiceAmount - I.PaidAmount) AS Outstanding_Amount
FROM Invoices I
JOIN DimVendor V 
    ON I.VendorID = V.VendorID
GROUP BY V.VendorName
ORDER BY Outstanding_Amount DESC;

/* Invoice Count by Month-Year */
SELECT 
    YEAR(InvoiceDate) AS Year,
    MONTH(InvoiceDate) AS Month,
    COUNT(*) AS Invoice_Count
FROM Invoices
GROUP BY YEAR(InvoiceDate), MONTH(InvoiceDate)
ORDER BY Year, Month;

/* Monthly Invoice Amount Trend */
SELECT 
    YEAR(InvoiceDate) AS Year,
    MONTH(InvoiceDate) AS Month,
    SUM(InvoiceAmount) AS Monthly_Invoice_Amount
FROM Invoices
GROUP BY YEAR(InvoiceDate), MONTH(InvoiceDate)
ORDER BY Year, Month;

/* Aging by Outstanding Amount */
SELECT
    CASE
        WHEN DATEDIFF(DAY, DueDate, GETDATE()) <= 30 THEN '0–30 Days'
        WHEN DATEDIFF(DAY, DueDate, GETDATE()) <= 60 THEN '31–60 Days'
        WHEN DATEDIFF(DAY, DueDate, GETDATE()) <= 90 THEN '61–90 Days'
        ELSE '90+ Days'
    END AS Aging_Bucket,
    SUM(InvoiceAmount - PaidAmount) AS Outstanding_Amount
FROM Invoices
WHERE PaymentDate IS NULL
GROUP BY
    CASE
        WHEN DATEDIFF(DAY, DueDate, GETDATE()) <= 30 THEN '0–30 Days'
        WHEN DATEDIFF(DAY, DueDate, GETDATE()) <= 60 THEN '31–60 Days'
        WHEN DATEDIFF(DAY, DueDate, GETDATE()) <= 90 THEN '61–90 Days'
        ELSE '90+ Days'
    END
ORDER BY Aging_Bucket;
