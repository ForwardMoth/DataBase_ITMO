# Лабораторная работа 3

## 1. Найти и вывести на экран название продуктов и название категорий товаров, к которым относится этот продукт, с учетом того, что в выборку попадут только товары с цветом Red и ценой не менее 100.
```sql
SELECT p.Name, c.Name 
FROM Production.Product p 
INNER JOIN Production.ProductSubcategory s 
ON p.ProductSubcategoryID = s.ProductSubcategoryID
INNER JOIN Production.ProductCategory c
ON s.ProductCategoryID = c.ProductCategoryID
WHERE p.color = 'red' AND p.ListPrice >= 100;
```
## 2. Вывести на экран названия подкатегорий с совпадающими именами.
```sql
SELECT s1.Name FROM Production.ProductSubcategory s1
INNER JOIN Production.ProductSubcategory s2
ON s1.Name = s2.Name
AND s1.ProductSubcategoryID != s2.ProductSubcategoryID;
```
## 3. Вывести на экран название категорий и количество товаров в данной категории.
```sql
SELECT c.Name, COUNT(*) FROM Production.Product p 
INNER JOIN Production.ProductSubcategory s 
ON p.ProductSubcategoryID = s.ProductSubcategoryID
INNER JOIN Production.ProductCategory c
ON s.ProductCategoryID = c.ProductCategoryID
GROUP BY c.Name;
```
## 4. Вывести на экран название подкатегории, а также количество товаров в данной подкатегории с учетом ситуации, что могут существовать подкатегории с одинаковыми именами.
```sql
SELECT s.Name, COUNT(*) FROM Production.Product p
INNER JOIN Production.ProductSubcategory s 
ON p.ProductSubcategoryID = s.ProductSubcategoryID
GROUP BY s.Name;
```
## 5. Вывести на экран название первых трех подкатегорий с небольшим количеством товаров.
```sql
SELECT TOP 3 s.Name FROM Production.Product p
INNER JOIN Production.ProductSubcategory s 
ON p.ProductSubcategoryID = s.ProductSubcategoryID
GROUP BY s.Name 
ORDER BY COUNT(*); 
```
## 6. Вывести на экран название подкатегории и максимальную цену продукта с цветом Red в этой подкатегории.
```sql
SELECT s.Name, MAX(p.listPrice) FROM Production.Product p 
INNER JOIN Production.ProductSubcategory s 
ON p.ProductSubcategoryID = s.ProductSubcategoryID 
WHERE color = 'red'
GROUP BY s.Name;
```
## 7. Вывести на экран название поставщика и количество товаров, которые он поставляет.
```sql
SELECT v.Name, COUNT(*) FROM Production.Product p 
INNER JOIN Purchasing.ProductVendor pv
ON p.ProductID = pv.ProductID 
INNER JOIN Purchasing.Vendor v
ON pv.BusinessEntityID = v.BusinessEntityID 
GROUP BY v.Name;
```
## 8.  Вывести на экран название товаров, которые поставляются более чем одним поставщиком.
```sql
SELECT p.Name FROM Production.Product p 
INNER JOIN Purchasing.ProductVendor pv
ON p.ProductID = pv.ProductID 
INNER JOIN Purchasing.Vendor v
ON pv.BusinessEntityID = v.BusinessEntityID 
GROUP BY p.Name 
HAVING COUNT(*) > 1; 
```
## 9. Вывести на экран название самого продаваемого товара.
```sql
SELECT TOP 1 p.Name FROM Sales.SalesOrderDetail sod
INNER JOIN Sales.SpecialOfferProduct sop
ON sod.ProductID = sop.ProductID
INNER JOIN Production.Product p 
ON p.ProductID = sop.ProductID
GROUP BY p.Name 
ORDER BY COUNT(*) DESC;
```
## 10. Вывести на экран название категории, товары из которой продаются наиболее активно.
```sql
SELECT TOP 1 c.Name FROM Production.ProductCategory c
INNER JOIN Production.ProductSubcategory s
ON c.ProductCategoryID = s.ProductCategoryID
INNER JOIN Production.Product p 
ON p.ProductSubcategoryID = s.ProductSubcategoryID
INNER JOIN Sales.SpecialOfferProduct sop 
ON sop.ProductID = p.ProductID
INNER JOIN Sales.SalesOrderDetail sod
ON sod.ProductID = sop.ProductID
GROUP BY c.Name 
ORDER BY COUNT(*) DESC;
```
## 11. Вывести на экран названия категорий, количество подкатегорий и количество товаров в них.
```sql
SELECT c.Name, COUNT(DISTINCT s.ProductSubcategoryID), COUNT(DISTINCT p.ProductID) 
FROM Production.Product p 
INNER JOIN Production.ProductSubcategory s 
ON p.ProductSubcategoryID = s.ProductSubcategoryID
INNER JOIN Production.ProductCategory c
ON c.ProductCategoryID = s.ProductCategoryID
GROUP BY c.Name;
```
## 12. Вывести на экран номер кредитного рейтинга и количество товаров, поставляемых компаниями, имеющими этот кредитный рейтинг.
```sql
SELECT v.CreditRating, COUNT(*) FROM Production.Product p
INNER JOIN Purchasing.ProductVendor pv
ON p.ProductID = pv.ProductID
INNER JOIN Purchasing.Vendor v
ON pv.BusinessEntityID = v.BusinessEntityID
GROUP BY v.CreditRating; 
```
