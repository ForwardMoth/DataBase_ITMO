# Лабораторная работа 5 

## 1. Найти среднее количество покупок на чек для каждого покупателя (2 способа)
```sql
SELECT tmp.c, AVG(tmp.cnt) FROM (
SELECT soh.CustomerID c, COUNT(*) as cnt 
FROM Sales.SalesOrderDetail sod
INNER JOIN Sales.SalesOrderHeader soh
ON sod.SalesOrderID = soh.SalesOrderID
GROUP BY sod.SalesOrderID, soh.CustomerID) tmp 
GROUP BY tmp.c
ORDER BY tmp.c;
```

```sql
WITH count_products (CustomerID, CountProducts) 
	AS (SELECT CustomerID, COUNT(*) as CountProducts 
		FROM Sales.SalesOrderDetail sod INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID 
		GROUP BY sod.SalesOrderID, soh.CustomerID) 
SELECT CustomerID, AVG(CountProducts) FROM count_products
GROUP BY CustomerID;
```
## 2. Найти для каждого продукта и каждого покупателя соотношение количества фактов покупки данного товара данным покупателем к общему количеству фактов покупки товаров данным покупателем
```sql
WITH customers_product (CustomerID, ProductID)
	AS (SELECT soh.CustomerID, sod.ProductID FROM Sales.SalesOrderHeader soh
	INNER JOIN Sales.SalesOrderDetail sod
	ON soh.SalesOrderID = sod.SalesOrderID), 
	TotalProductsCustomer (CustomerID, TotalCountProduct) 
	AS (SELECT  CustomerID, COUNT(*) 
		FROM customers_product
		GROUP BY CustomerID), 
	ProductbyCustomer (CustomerID, CountProduct) 
	AS (SELECT CustomerID, COUNT (*) 
		FROM customers_product
		GROUP BY CustomerID, ProductID) 
SELECT CAST(CAST(CountProduct as float) / TotalCountProduct as decimal(3,2))
FROM TotalProductsCustomer tpc 
INNER JOIN ProductbyCustomer pbc
ON tpc.CustomerID = pbc.CustomerID;
```
## 3. Вывести на экран следящую информацию: Название продукта, Общее количество фактов покупки этого продукта,  Общее количество покупателей этого продукта
```sql
WITH TotalProductBuy (ProductID, TotalCountBuyings)
	AS (SELECT ProductID, COUNT(*) 
		FROM Sales.SalesOrderDetail
		GROUP BY ProductID), 
	TotalCustomersCount (ProductID, CustomersCount)
	AS (SELECT ProductID, COUNT(*) 
		FROM Sales.SalesOrderDetail sod
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID
		GROUP BY ProductID)
SELECT p.ProductID, TotalCountBuyings, CustomersCount
FROM Production.Product p 
LEFT JOIN TotalProductBuy tpb
ON tpb.ProductID = p.ProductID
LEFT JOIN TotalCustomersCount tcc
ON tcc.ProductID = p.ProductID; 
```
## 4. Вывести для каждого покупателя информацию о максимальной и минимальной стоимости одной покупки, чеке, в виде таблицы: номер покупателя, максимальная сумма, минимальная сумма. 
```sql
WITH PriceCustomer (CustomerID, Price)
	AS (SELECT CustomerID, UnitPrice FROM Sales.SalesOrderDetail sod
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID)
SELECT CustomerID, MAX(Price) as max_price, MIN(Price) as min_price
FROM PriceCustomer
GROUP BY CustomerID;
```
## 5. Найти номера покупателей, у которых не было нет ни одной пары чеков с одинаковым количеством наименований товаров.
```sql
WITH orders(CustomerID, SalesOrderID, ProductID)
	as (SELECT CustomerID, sod.SalesOrderID, ProductID
		FROM Sales.SalesOrderDetail sod 
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID)
SELECT CustomerID 
FROM Sales.Customer 
WHERE CustomerID NOT IN (
	SELECT DISTINCT o1.CustomerID 
	FROM orders o1
	INNER JOIN orders o2
	ON o1.CustomerID = o2.CustomerID
	AND o1.SalesOrderID != o2.SalesOrderID
	AND o1.ProductID = o2.ProductID)
```
## 6. Найти номера покупателей, у которых все купленные ими товары были куплены как минимум дважды, т.е. на два разных чека.
```sql
WITH order_product(SalesOrderID, ProductID)
	AS (SELECT SalesOrderID, ProductID 
		FROM Sales.SalesOrderDetail
		GROUP BY SalesOrderID, ProductID), 
	product_condition(ProductID)
	AS (SELECT ProductID 
		FROM order_product
		GROUP BY ProductID
		HAVING COUNT(*) >= 2), 
	customers_product (CustomerID, ProductID)
	AS (SELECT CustomerID, ProductID
		FROM Sales.SalesOrderDetail sod
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID)
SELECT CustomerID 
FROM customers_product 
WHERE ProductID IN (SELECT * FROM product_condition) 
```
