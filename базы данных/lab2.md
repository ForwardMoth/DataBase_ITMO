# Лабораторная работа 2

## 1. Найти и вывести на экран количество товаров каждого цвета, исключив из поиска товары, цена которых меньше 30. 
```sql
SELECT color, COUNT(*) 
FROM Production.Product 
WHERE ListPrice < 30 
GROUP BY color;
```
## 2. Найти и вывести на экран список, состоящий из цветов товаров, таких, что минимальная цена товара данного цвета более 100.
```sql
SELECT color 
FROM Production.Product 
GROUP BY color 
HAVING MIN(ListPrice) > 100;
```
## 3. Найти и вывести на экран номера подкатегорий товаров и количество товаров в каждой подкатегории.
```sql
SELECT ProductSubcategoryID, COUNT(*) 
FROM Production.Product 
WHERE ProductSubcategoryID IS NOT NULL 
GROUP BY ProductSubcategoryID;
```
## 4. Найти и вывести на экран номера товаров и количество фактов продаж данного товара (используется таблица SalesORDERDetail).
```sql
SELECT ProductID, count(*) 
FROM Sales.SalesOrderDetail 
GROUP BY ProductID;
```
## 5. Найти и вывести на экран номера товаров, которые были куплены более пяти раз.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail 
GROUP BY ProductID 
HAVING count(*) > 5; 
```
## 6. Найти и вывести на экран номера покупателей, CustomerID, у которых существует более одного чека, SalesORDERID, с одинаковой датой.
```sql
SELECT DISTINCT CustomerID 
FROM Sales.SalesOrderHeader
GROUP BY CustomerID, OrderDate 
HAVING count(OrderDate) > 1; 
```
## 7. Найти и вывести на экран все номера чеков, на которые приходится более трех продуктов.
```sql
SELECT SalesOrderID 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID 
HAVING count(ProductID) > 3;
```
## 8. Найти и вывести на экран все номера продуктов, которые были куплены более трех раз.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail 
GROUP BY ProductID 
HAVING count(*) > 3; 
```
## 9. Найти и вывести на экран все номера продуктов, которые были куплены или три или пять раз.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail 
GROUP BY ProductID 
HAVING count(*) = 3 OR count(*) = 5;
```
## 10. Найти и вывести на экран все номера подкатегорий, в которым относится более десяти товаров.
```sql
SELECT ProductSubcategoryID 
FROM Production.Product 
WHERE ProductSubcategoryID IS NOT NULL 
GROUP BY ProductSubcategoryID 
HAVING count(Name) > 10;
```
## 11. Найти и вывести на экран номера товаров, которые всегда покупались в одном экземпляре за одну покупку.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail 
GROUP BY ProductID 
HAVING MAX(OrderQty) = 1;
```
## 12. Найти и вывести на экран номер чека, SalesORDERID, на который приходится с наибольшим разнообразием товаров купленных на этот чек.
```sql
SELECT TOP 1 SalesOrderID 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID 
ORDER BY COUNT(*) DESC;
```
## 13. Найти и вывести на экран номер чека, SalesORDERID с наибольшей суммой покупки, исходя из того, что цена товара – это UnitPrice, а количество конкретного товара в чеке – это ORDERQty.
```sql
SELECT TOP 1 SalesOrderID 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID 
ORDER BY MAX(UnitPrice * OrderQty) DESC;
```
## 14. Определить количество товаров в каждой подкатегории, исключая товары, для которых подкатегория не определена, и товары, у которых не определен цвет.
```sql
SELECT ProductSubcategoryID, COUNT(*) 
FROM Production.Product 
WHERE ProductSubcategoryID IS NOT NULL AND color IS NOT NULL 
GROUP BY ProductSubcategoryID;
```

## 15. Получить список цветов товаров в порядке убывания количества товаров данного цвета.
```sql
SELECT color 
FROM Production.Product 
GROUP BY color 
ORDER BY count(color) DESC;
```

## 16. Вывести на экран ProductID тех товаров, что всегда покупались в количестве более 1 единицы на один чек, при этом таких покупок было более двух.
```sql
SELECT ProductID 
FROM Sales.SalesOrderDetail 
GROUP BY ProductID 
HAVING count(*) > 2 AND MIN(OrderQty) > 1;
```
