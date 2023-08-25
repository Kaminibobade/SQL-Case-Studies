# Q1 How many orders of status shipped were shipped on or later than the required date?
```js
Select count(*) as total
From cr_orders
where status = "Shipped" AND shippedDate >= requiredDate;
```

# Q2 The average MSRP of the productline 'Motorcycles' is 97.18, and the average MSRP of the productLine‘ Classic Cars' is 118.02 Find out the product code for products mentioned above with a quantity more than equal to 1000.  Extend the SQL query to filter 'Motorcycles' with greater than its average price and  ‘Classic Cars' with less than its average price. Additionally, sort the results based on “MSRP” (highest to lowest) and “quantityInStock” (lowest to highest). Share the product code of the 3rd result from the top.
```js
SELECT productCode
FROM cr_products
WHERE (productLine = 'Motorcycles' AND MSRP > 97.18)
   OR (productLine = 'Classic Cars' AND MSRP < 118.02)
   AND quantityInStock >= 1000
ORDER BY MSRP DESC, quantityInStock ASC
LIMIT 1 OFFSET 2;
```
# Q3 Find out the product's revenue (based on MSRP) based on each productScale. revenue = quantity * MSRP Which ProductLine and ProductScale have the highest revenue? */
```js
Select ProductScale, ProductLine,Sum(MSRP*quantityInStock) as Revenue
From cr_products
group by ProductLine,ProductScale
Order by Revenue DESC;
```
# Q4 What is the Year and Month which has the highest profit? */
```js
Select Year(o.orderDate) as Year , Month(o.orderDate) as Month, Sum((od.priceEach - p.buyPrice)* od.quantityOrdered) as Profit
From cr_orders as o 
JOIN cr_orderdetails as od 
ON o.orderNumber = od.orderNumber
JOIN cr_products as p 
ON p.productCode = od.productCode
Group by Year ,Month
Order by Profit DESC
Limit 1; 
```
# Q5 Which product is not ordered yet? 
```js
Select productCode, productName
From cr_products 
Where productCode NOT IN (Select productCode
                          From cr_orderdetails);

## 2nd WAY
Select p.productCode, p.productName
From cr_products as p
LEFT JOIN cr_orderdetails as od 
ON p.productCode = od.productCode
where od.productCode IS NULL;
```
