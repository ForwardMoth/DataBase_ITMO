# Лабораторная работа 6 

## 1. Найти долю продаж каждого продукта (цена продукта * количество продукта), на каждый чек, в денежном выражении.
```sql
WITH PriceProduct(SalesOrderID, ProductID, Number, Price)
	AS (SELECT SalesOrderID, ProductID,OrderQty, UnitPrice
		FROM Sales.SalesOrderDetail)
SELECT SalesOrderID, ProductID, 
SUM(Number * Price) OVER (partitiON by SalesOrderID, ProductID) / 
SUM(Number * Price) OVER (partitiON by SalesOrderID)
FROM PriceProduct;
```

```sql 
WITH PriceProduct(SalesOrderID, ProductID, Price)
	AS (SELECT SalesOrderID, ProductID, SUM(UnitPrice * OrderQty)
		FROM Sales.SalesOrderDetail
		GROUP BY SalesOrderID, ProductID)
SELECT p.SalesOrderID,p.ProductID, 
Price / SUM(UnitPrice * OrderQty) OVER (partitiON by p.SalesOrderID)
FROM PriceProduct p 
INNER JOIN Sales.SalesOrderDetail sod
ON sod.SalesOrderID = p.SalesOrderID 
AND sod.ProductID = p.ProductID;
```
## 2. Вывести на экран список продуктов, их стоимость, а также разницу между стоимостью этого продукта и стоимостью самого дешевого продукта в той же подкатегории, к которой относится продукт.
```sql
SELECT ProductID, ListPrice, ProductSubcategoryID, 
ListPrice - MIN(ListPrice) OVER(Partition by ProductSubcategoryID)
FROM Production.Product
WHERE ProductSubcategoryID IS NOT NULL;
```

## 3. Вывести три колонки: номер покупателя, номер чека покупателя (отсортированный по возрастанию даты чека) и искусственно введенный порядковый номер текущего чека, начиная с 1, для каждого покупателя. 
```sql
SELECT CustomerID, SalesOrderID, OrderDate, 
ROW_NUMBER() OVER(partition by CustomerID 
ORDER BY OrderDate DESC)
FROM Sales.SalesOrderHeader; 
```

## 4. Вывести номера продуктов, таких что их цена выше средней цены продукта в подкатегории, к которой относится продукт. Запрос реализовать двумя способами. В одном из решений допускается использование обобщенного табличного выражения. 
```sql
WITH AvgPriceSubcategory (SubcategoryID, price)
	AS (SELECT ProductSubcategoryID, AVG(ListPrice) as price
		FROM Production.Product
		WHERE ProductSubcategoryID IS NOT NULL 
		GROUP BY ProductSubcategoryID)
SELECT ProductID 
FROM Production.Product p 
WHERE ListPrice > 
ANY(SELECT price 
FROM AvgPriceSubcategory aps
WHERE p.ProductSubcategoryID = aps.SubcategoryID)
```
