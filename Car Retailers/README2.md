# Question 1- Print the address of the customer as follows,Format: addressLine1, {state}, city {- postcode}, country addressLine1 followed by the state if present, followed by postal code if present else city, and followed by the country.
```js
SELECT Concat(contactfirstname, "", contactlastname) AS 'FullName',
customername, addressline1, state, postalcode, city, country, 
Concat_ws(', ', addressline1, 
        Coalesce(state, IFNULL(postalcode, city)),country) AS 'Address'
FROM cr_customers; 

SELECT Concat(contactfirstname, "", contactlastname) AS 'FullName',
Concat_ws(', ', addressline1, 
        Coalesce(state, IFNULL(postalcode, city)),country) AS 'Address'
FROM cr_customers;
```

# Question 2-Find the Month on Month growth in profit for each year. MoM_growth is calculated as follows.
# For example, MoM (Feb 2003) = ((Annual Revenue of Feb 2003/Annual Revenue of Jan 2003) - 1) * 100
# Note: Please make sure that MoM is calculated for each year. Ideally, the MoM growth of the first month of every year should be NULL, as shown in the sample output below.
```js
SELECT Year, Month_name, profit,
    ((profit / LAG(profit, 1, NULL) OVER (PARTITION BY Year ORDER BY month_num)) - 1) * 100 AS MoM_Growth 
FROM ( SELECT YEAR(o.orderDate) AS Year, MONTHNAME(o.orderDate) AS Month_name, MONTH(o.orderDate) AS month_num,
       SUM((od.priceEach - p.buyPrice) * od.quantityOrdered) AS Profit 
    FROM
        cr_orderdetails od
    JOIN cr_orders o 
    ON od.orderNumber = o.orderNumber
    JOIN cr_products p 
    ON p.productCode = od.productCode
    GROUP BY
    Year, month_name, month_num
) x;
```
# Question 3 - Above question with CTE and to find What is the MoM for Feb 2005?
```js
With cte as (SELECT YEAR(o.orderDate) AS Year, MONTHNAME(o.orderDate) AS Month_name, MONTH(o.orderDate) AS month_num,
       SUM((od.priceEach - p.buyPrice) * od.quantityOrdered) AS Profit 
    FROM
        cr_orderdetails od
    JOIN cr_orders o 
    ON od.orderNumber = o.orderNumber
    JOIN cr_products p 
    ON p.productCode = od.productCode
    GROUP BY
    Year, month_name, month_num
    ), 
cte1 as ( SELECT Year, Month_name, profit,
    ((profit / LAG(profit, 1, NULL) OVER (PARTITION BY Year ORDER BY month_num)) - 1) * 100 AS MoM_Growth 
FROM cte
)

Select * from cte1
where Year = 2005 AND Month_name= "February";
```


# Question 4- What is the Average Order Value? Submit the value in the answer.  
```js
SELECT DISTINCT avg_order_value
FROM (
    SELECT c.customerNumber, c.contactfirstname, c.contactlastname, o.ordernumber, od.quantityordered, 
        od.priceeach, priceeach * quantityordered AS order_value,
        ROUND(AVG(priceeach * quantityordered) OVER(), 2) avg_order_value
    FROM cr_orderdetails od
    JOIN cr_orders o 
    ON od.ordernumber = o.ordernumber
    JOIN cr_customers c 
    ON c.customernumber = o.customernumber
) a;
```
# Question 5 - Submit the orders above and below AOV for customer number 205. A number of orders above AOVFor each customer i.e (customerNumber) count the number of orders above and below the average order value. “Average Order Value(AOV)” is calculated as below, Total Revenue is calculated as the sum of the revenue of each order.Each order revenue is Price per unit * No. Of Units. Open Hint: AVG() OVER() Windows Function can be used here to calculate AOV. Since you need to get the count of orders above and below the AOV, the final output looks like the one below. Conditions to get the following details, Order Above AVG =  Actual Order Value >= AVG Order Value 2. Order Below AVG =  Actual Order Value < AVG Order Value
```js
Select customernumber, contactfirstname, contactlastname, 
sum(order_value >= avg_order_value) order_above_avg,
sum(order_value < avg_order_value) order_below_avg
from (select c.customernumber, c.contactfirstname, c.contactlastname, o.ordernumber, 
od.quantityordered, od.priceeach, priceeach * quantityordered as order_value,
Round(avg(priceeach * quantityordered) over() , 2) avg_order_value
From cr_orderdetails od
Join cr_orders o 
On od.ordernumber = o.ordernumber
Join cr_customers c
On c.customernumber = o.customernumber) a 
Where customernumber = 205
Group by customernumber, contactfirstname,contactlastname;
```

