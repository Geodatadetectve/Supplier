# Supplier
strategic alliance

 📈 Strategic Alliances Unleashed: Elevating Supplier Collaboration for Mutual Growth at Sparkles Inc.
In this project, I use mySQL to identify key suppliers for a retail company. I gained hands-on experience with querying databases to retrieve relevant data, and discovered how SQL can be a valuable tool in supply chain operations.

🔍 Business Overview/Problem
Sparkles Inc. recently hired a new procurement manager, John Doe, to optimize procurement operations. He sees an opportunity to identify and partner with top suppliers to negotiate discounted deals and other incentives. John has requested the analytics team to identify Sparkles' top suppliers based on purchase volumes, frequency of purchase, and amount spent. He needs a list of these suppliers and their contact information. John wants to know who Sparkles' most important suppliers are so that he can negotiate better deals with them. He needs the analytics team to identify these suppliers based on how much they sell to Sparkles and how often Sparkles buys from them.

📈 Rationale for the Project
Supplier collaboration in supply chains is essential for reducing costs, minimizing risks, and creating more resilient supply chain networks. By working together, suppliers and businesses can identify and address inefficiencies, proactively manage disruptions, and drive mutual growth. Sparkles Inc. can improve its supply chain and save money by identifying its key suppliers. It can negotiate better prices, bulk purchase discounts, and stronger supplier relationships, which can lead to better reliability and fewer supply chain disruptions. By knowing who its most important suppliers are, Sparkles can work with them to improve its supply chain and make it more efficient. This can lead to lower costs, better quality products, and more reliable deliveries.

 🎯 Aim of the Project
Building on the foundation of Sparkles Inc.'s strong retail presence and in line with John Doe's vision, the objectives of this project are outlined as follows:
 
•	✓ Supplier Analysis: Dive deep into historical data to spotlight top-performing suppliers. This analysis would consider criteria such as purchase volume, frequency, and total amount spent.
 
•	✓ Data Extraction: After a meticulous analysis, create a list of these suppliers and extract their details from the database onto a spreadsheet for easy access and presentation.
 
•	✓ Stakeholder Presentation: Present John Doe with the final list, ensuring it clearly displays the suppliers' contact information and meets all of his specified requirements.

📊 Data Description
✓ ProductId: unique identifier for the product.
✓ ProductName: The name of the product.
✓ Size: The size of the product.
✓ VendorNumber: unique identifier for each vendor.
✓ Classification: The category that the product belongs to (1=wine, 2=spirit).
✓ PODate: The date when the purchase order was placed for the product.
✓ PONumber: purchase order number associated with the invoice.
✓ InvoiceDate: date on which the invoice was issued
✓ PayDate: The date when the product was paid for.
✓ ReceivingDate: The date when the product was received by the store.
✓ Quantity: The number of units of the product that were purchased.
✓ PurchasePrice: The price per unit of the product that was purchased.
✓ InventoryId: A unique identifier for the product in the store's inventory system.

🛠️ Tech Stack
MySQL

SELECT * FROM alliance.purchases;
-- Cleaning Table formats
-- Step 1: Add the new column
ALTER TABLE alliance.purchases ADD COLUMN nPODate DATE;
-- Step 2: Update the new column with the formatted date
UPDATE alliance.purchases
SET nPODate = STR_TO_DATE(PODate, '%d/%m/%Y');
ALTER TABLE  alliance.purchases DROP COLUMN PODate;
ALTER TABLE alliance.purchases 
ADD COLUMN nReceivingDate DATE,
ADD COLUMN nInvoiceDate Date,
ADD COLUMN nPayDate Date;
UPDATE alliance.purchases
SET nReceivingDate = STR_TO_DATE(ReceivingDate, '%d/%m/%Y');
UPDATE alliance.purchases
SET nInvoiceDate = STR_TO_DATE(InvoiceDate, '%d/%m/%Y');
UPDATE alliance.purchases
SET nPayDate = STR_TO_DATE(PayDate, '%d/%m/%Y');
ALTER TABLE  alliance.purchases DROP COLUMN ReceivingDate;
ALTER TABLE  alliance.purchases DROP COLUMN InvoiceDate;
ALTER TABLE  alliance.purchases DROP COLUMN PayDate;
SELECT * FROM alliance.purchases;
ALTER TABLE  alliance.purchases DROP COLUMN nPODate;

SELECT * FROM alliance.purchases;
-- Count number of rows in vendor table
SELECT Count(*) FROM alliance.vendor;

-- checking for null values within vendor table
Select *
from alliance.vendor
where Vendornumber IS NULL
OR Vendorname is NULL
OR Vendoremail is null;

-- Checking for duplicates in vendor table
Select Vendornumber, Vendorname, Vendoremail, count(*)
From alliance.vendor
Group by 1,2,3
Having count(*) >1;

-- Exploring the purchase table
Select *
from alliance.purchases
limit 10;

-- number of rows in purchases table
Select count(*)
from alliance.purchases;
-- number of unique product
Select count(distinct ProductID)
from alliance.purchases;

-- number of unique vendors
Select count(distinct VendorNumber)
from alliance.purchases;

-- Checking for duplicates in the purchases table
Select InventoryID, ProductID, ProductName, Size, VendorNumber, PONumber, PurchasePrice, Quantity, Classification, PODate, ReceivingDate, InvoiceDate, PayDate, count(*)
from alliance.purchases
Group by InventoryID, ProductID, ProductName, Size, VendorNumber, PONumber, PurchasePrice, Quantity, Classification, PODate, ReceivingDate, InvoiceDate, PayDate
Having count(*) >1;

-- checking date range within the data.
Select 
min(PODate) as min_date_PODate,
max(PODate) as max_date_PODate,
min(ReceivingDate) as min_date_ReceivingDate,
max(ReceivingDate) as max_date_ReceivingDate,
min(InvoiceDate) as min_date_InvoiceDate,
max(InvoiceDate) as max_date_InvoiceDate,
min(PayDate) as min_date_PayDate,
max(PayDate) as max_date_PayDate
from alliance.purchases
order by 1,2,3,4,5,6,7,8;

-- understanding purchasing price
Select 
round(min(PurchasePrice),2) as min_purchase_price,
round(max(PurchasePrice),2) as max_purchase_price,
round(avg(PurchasePrice),2) as avg_purchase_price,
round(sum(PurchasePrice),2) as sum_purchase_price
from alliance.purchases
order by 1,2,3,4

-- explore the classification column
Select 
classification, count(*)
from alliance.purchases
group by classification;

-- identify top-performing suppliers based on purchase volume, frequency of purchase, and amount spent.

Select 
v.Vendorname,
v.Vendoremail,
round(sum(p.Quantity * p.PurchasePrice),2) as amount_spent,
sum(p.Quantity) as purchase_volume,
Count(distinct p.PONumber) as purchase_frequency
from alliance.vendor as v
inner join alliance.purchases as p
on v.Vendornumber = p. VendorNumber
Group by v.Vendorname, v.Vendoremail
Having amount_spent >= 100000
order by amount_spent desc, purchase_volume desc, purchase_frequency desc

-- Who are the top suppliers of wine?
Select 
v.Vendorname,
v.Vendoremail,
sum(p.Quantity) as purchase_quantity
from alliance.vendor as v
inner join alliance.purchases as p
on v.Vendornumber = p. VendorNumber
where p.classification = 2
Group by v.Vendorname, v.Vendoremail
order by purchase_quantity desc
limit 10;

-- Who are the top suppliers of spirit?
Select 
v.Vendorname,
v.Vendoremail,
sum(p.Quantity) as purchase_quantity
from alliance.vendor as v
inner join alliance.purchases as p
on v.Vendornumber = p. VendorNumber
where p.classification = 1
Group by v.Vendorname, v.Vendoremail
order by purchase_quantity desc
limit 10;

-- select the top vendors with shortest lead time possible (days to ship).
SELECT 
    v.Vendorname,
    v.Vendoremail,
    ROUND(AVG(p.ReceivingDate - p.PODate), 2) AS Avg_lead_time,
    CASE
        WHEN p.classification = 1 THEN 'Spirit'
        ELSE 'Wine'
    END AS Category
FROM 
    alliance.vendor AS v
INNER JOIN 
    alliance.purchases AS p
ON 
    v.Vendornumber = p.VendorNumber
GROUP BY 
    v.Vendorname, 
    v.Vendoremail, 
    Category
ORDER BY 
    Avg_lead_time DESC
LIMIT 10;

-- select the top vendors that deliver above the lead time possible (days to ship).
SELECT 
    v.Vendorname,
    v.Vendoremail,
    AVG(DATEDIFF(p.ReceivingDate, p.PODate)) AS Avg_lead_time,
    CASE
        WHEN p.classification = 1 THEN 'Spirit'
        ELSE 'Wine'
    END AS Category
FROM 
    alliance.vendor AS v
INNER JOIN 
    alliance.purchases AS p
ON 
    v.Vendornumber = p.VendorNumber
GROUP BY 
    v.Vendorname, 
    v.Vendoremail, 
    Category
HAVING 
    AVG(DATEDIFF(p.ReceivingDate, p.PODate)) >= (
        SELECT 
            AVG(DATEDIFF(p2.ReceivingDate, p2.PODate)) AS Overall_Avg_lead_time
        FROM 
            alliance.purchases AS p2
    )
ORDER BY 
    Avg_lead_time DESC
LIMIT 10;
