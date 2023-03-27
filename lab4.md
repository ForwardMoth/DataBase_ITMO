# Лабораторная работа 4

## 1. Найти название самого продаваемого продукта.
```sql
SELECT Name FROM Production.Product WHERE
ProductID = (SELECT TOP 1 ProductID
FROM Sales.SalesOrderDetail
GROUP BY ProductID
ORDER BY COUNT(*) DESC);
```
## 2. Найти покупателя, совершившего покупку на самую большую сумм, считая сумму покупки исходя из цены товара без скидки (UnitPrice)
```sql
SELECT CustomerID 
FROM Sales.SalesOrderHeader 
WHERE SalesOrderID IN (
	SELECT SalesOrderID 
	FROM Sales.SalesOrderDetail 
	WHERE UnitPrice =(
		SELECT TOP 1 MAX(UnitPrice) 
		FROM Sales.SalesOrderDetail)

); 
```
## 3. Найти такие продукты, которые покупал только один покупатель.
```sql
SELECT DISTINCT ProductID FROM Sales.SalesOrderDetail WHERE SalesOrderID IN(
	SELECT SalesOrderID 
	FROM Sales.SalesOrderHeader 
	WHERE CustomerID IN (
		SELECT CustomerID FROM Sales.SalesOrderHeader soh
		INNER JOIN Sales.SalesOrderDetail sod 
		ON soh.SalesOrderID = sod.SalesOrderID
		GROUP BY CustomerID
		HAVING COUNT(*) = 1)
);
```
## 4. Вывести список продуктов, цена которых выше средней цены товаров в подкатегории, к которой относится товар.
```sql
SELECT ProductID 
FROM Production.Product p1 
WHERE ListPrice > ALL (
	SELECT AVG(ListPrice) 
	FROM Production.Product p 
	WHERE p.ProductSubcategoryID = p1.ProductSubcategoryID
);
```
## 5. Найти такие товары, которые были куплены более чем одним покупателем, при этом все покупатели этих товаров покупали товары только одного цвета и товары не входят в список покупок покупателей, купивших товары только двух цветов
```sql
SELECT DISTINCT ProductID 
FROM Sales.SalesOrderDetail 
WHERE SalesOrderDetailID IN (
	SELECT SalesOrderDetailID  
	FROM Sales.SalesOrderHeader
	WHERE CustomerID IN (
		SELECT CustomerID 
		FROM Sales.SalesOrderHeader soh1
		INNER JOIN Sales.SalesOrderDetail sod1			
		ON soh1.SalesOrderID = sod1.SalesOrderID
		WHERE CustomerID IN (
			SELECT CustomerID 
			FROM Sales.SalesOrderHeader soh2
			INNER JOIN Sales.SalesOrderDetail sod2
			ON soh2.SalesOrderID = sod2.SalesOrderID
			INNER JOIN Production.Product p1
			ON p1.ProductID = sod2.ProductID
			WHERE color IS NOT NULL AND p1.ProductID not IN (
				SELECT ProductID 
				FROM Production.Product 
				WHERE CustomerID IN (
					SELECT soh3.CustomerID 
					FROM Sales.SalesOrderHeader soh3
					INNER JOIN Sales.SalesOrderDetail sod3
					ON soh3.SalesOrderID = sod3.SalesOrderID
					INNER JOIN Production.Product p2
					ON p2.ProductID = sod3.ProductID
					WHERE color IS NOT NULL
					GROUP BY CustomerID, color 
					HAVING COUNT(*) = 2)
			)
			GROUP BY CustomerID, color 
			HAVING COUNT(*) = 1) 
		GROUP BY CustomerID
		HAVING COUNT(*) > 1) 
);
```
## 6. Найти такие товары, которые были куплены такими покупателями, у которых они присутствовали в каждой их покупке. 
```sql
SELECT DISTINCT sod1.ProductID 
FROM Sales.SalesOrderDetail sod1
WHERE (
	SELECT COUNT(DISTINCT CustomerID) FROM Sales.SalesOrderHeader soh
	INNER JOIN Sales.SalesOrderDetail sod
	ON soh.SalesOrderID = sod.SalesOrderID
	WHERE ProductID = sod1.ProductID) 
= (SELECT COUNT(DISTINCT CustomerID) FROM Sales.Customer);
```
## 7. Найти покупателей, у которых есть товар, присутствующий в каждой покупке/чеке
```sql
SELECT CustomerID 
FROM Sales.SalesOrderHeader soh1
INNER JOIN Sales.SalesOrderDetail sod1
ON soh1.SalesOrderID = sod1.SalesOrderID
WHERE ( 
	SELECT COUNT(DISTINCT SalesOrderID) 
	FROM Sales.SalesOrderDetail WHERE sod1.ProductID = ProductID) 
= (SELECT COUNT(DISTINCT SalesOrderID) FROM Sales.SalesOrderDetail);
```
## 8. Найти такой товар или товары, которые были куплены не более чем тремя различными покупателями
```sql
SELECT DISTINCT ProductID 
FROM Sales.SalesOrderDetail 
WHERE ProductID =ANY(
	SELECT ProductID FROM Sales.SalesOrderHeader soh
	INNER JOIN Sales.SalesOrderDetail sod
	ON soh.SalesOrderID = sod.SalesOrderID
	GROUP BY ProductID
	HAVING COUNT(DISTINCT CustomerID) > 0 AND COUNT(DISTINCT CustomerID) < 4
);
```
## 9. Найти все товары, такие что их покупали всегда с товаром, цена которого максимальна в своей категории 
```sql
SELECT DISTINCT ProductID FROM Sales.SalesOrderDetail
WHERE SalesOrderID IN(
	SELECT SalesOrderID 
	FROM Sales.SalesOrderDetail sod1
	WHERE sod1.ProductID IN ( 
		SELECT ProductID 
		FROM Production.Product
		WHERE ListPrice =ANY(
			SELECT MAX(p.ListPrice) FROM Production.Product p 
			INNER JOIN Production.ProductSubcategory ps
			ON p.ProductSubcategoryID = ps.ProductSubcategoryID
			INNER JOIN Production.ProductCategory pc
			ON ps.ProductCategoryID = pc.ProductCategoryID
			GROUP BY pc.ProductCategoryID
		)
	)
);
```
## 10. Найти номера тех покупателей, у которых есть как минимум два чека, и каждый из этих чеков содержит как минимум три товара, каждый из которых как минимум был куплен другими покупателями три раза. 
```sql
SELECT CustomerID 
FROM Sales.SalesOrderHeader soh1
WHERE SalesOrderID IN (
	SELECT SalesOrderID
	FROM Sales.SalesOrderDetail
	WHERE ProductID IN (
		SELECT ProductID 
		FROM Sales.SalesOrderDetail sod
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID
		GROUP BY ProductID
		HAVING COUNT(DISTINCT CustomerID) >= 3
	)
	GROUP BY SalesOrderID 
	HAVING COUNT(DISTINCT ProductID) >= 3  	
	)
GROUP BY CustomerID
HAVING COUNT(DISTINCT SalesOrderID) >= 2
```
## 11. Найти все чеки, в которых каждый товар был куплен дважды этим же покупателем
```sql
SELECT SalesOrderID 
FROM Sales.SalesOrderDetail sod
GROUP BY SalesOrderID
HAVING COUNT(DISTINCT ProductID) =ALL(
	SELECT COUNT(*) FROM Sales.SalesOrderDetail sod1
	WHERE ProductID IN (
		SELECT DISTINCT ProductID
		FROM Sales.SalesOrderDetail sod
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID
		GROUP BY ProductID, CustomerID
		HAVING COUNT(*) = 2)	
	AND sod1.SalesOrderID = sod.SalesOrderID); 
```
## 12. Найти товары, которые были куплены минимум три раза различными покупателями.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail sod
INNER JOIN Sales.SalesOrderHeader soh
ON sod.SalesOrderID = soh.SalesOrderID
GROUP BY ProductID
HAVING COUNT(DISTINCT CustomerID) >= 3;	
```
## 13. Найти такую подкатегорию или подкатегории товаров, которые содержат более трех товаров, купленных более трех раз.
```sql
SELECT ProductSubcategoryID 
FROM Production.Product
WHERE ProductSubcategoryID IN (
	SELECT ProductSubcategoryID 
	FROM Production.Product
	WHERE ProductID IN (
		SELECT ProductID 
		FROM Sales.SalesOrderDetail sod
		INNER JOIN Sales.SalesOrderHeader soh
		ON sod.SalesOrderID = soh.SalesOrderID
		GROUP BY ProductID
		HAVING COUNT(*) >= 3)
	)
GROUP BY ProductSubcategoryID
HAVING COUNT(*) > 3
```
## 14. Найти те товары, которые не были куплены более трех раз, и как минимум дважды одним и тем же покупателем.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail sod
INNER JOIN Sales.SalesOrderHeader soh
ON sod.SalesOrderID = soh.SalesOrderID
WHERE ProductID IN (
	SELECT ProductID 
	FROM Sales.SalesOrderDetail sod
	INNER JOIN Sales.SalesOrderHeader soh
	ON sod.SalesOrderID = soh.SalesOrderID
	GROUP BY ProductID, CustomerID
	HAVING COUNT(*) >= 2)
GROUP BY ProductID
HAVING COUNT(*) <= 3; 
```

