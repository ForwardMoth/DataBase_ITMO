# Лабораторная работа 1 

## 1. Найти и вывести на экран названия продуктов, их цвет и размер.
```sql
SELECT name, color, size FROM Production.Product;
```
## 2. Найти и вывести на экран названия, цвет и размер таких продуктов, у которых цена более 100.
```sql
SELECT name,color, size FROM Production.Product where ListPrice > 100;
```
## 3. Найти и вывести на экран название, цвет и размер таких продуктов, у которых цена менее 100 и цвет Black.
```sql
SELECT name,color, size FROM Production.Product where ListPrice < 100 and color = 'black';
```
## 4. Найти и вывести на экран название, цвет и размер таких продуктов, у которых цена менее 100 и цвет Black, упорядочив вывод по возрастанию стоимости продуктов.
```sql
SELECT name, color, size FROM Production.Product where ListPrice < 100 and color = 'black' order by ListPrice;
```
## 5. Найти и вывести на экран название и размер первых трех самых дорогих товаров с цветом Black.
```sql
SELECT TOP 3 name, color, size FROM Production.Product where color = 'black' order by ListPrice desc;
```
## 6. Найти и вывести на экран название и цвет таких продуктов, для которых определен и цвет, и размер.
```sql
SELECT name, color FROM Production.Product where color is NOT NULL and size is NOT NULL;
```
## 7. Найти и вывести на экран не повторяющиеся цвета продуктов, у которых цена находится в диапазоне от 10 до 50 включительно.
```sql
SELECT DISTINCT color FROM Production.Product where ListPrice BETWEEN 10 and 50;
```
## 8. Найти и вывести на экран все цвета таких продуктов, у которых в имени первая буква ‘L’ и третья ‘N’.
```sql
SELECT color FROM Production.Product where name LIKE 'L_N%';
```
## 9. Найти и вывести на экран названия таких продуктов, которых начинаются либо на букву ‘D’, либо на букву ‘M’, и при этом длина имени – более трех символов.
```sql
SELECT name FROM Production.Product where name LIKE '[DM]%' and len(name) > 3; 
```
## 10. Вывести на экран названия продуктов, у которых дата начала продаж – не позднее 2012 года.
```sql
SELECT name FROM Production.Product where DATEPART(YEAR,SellStartDate) < 2013;
```
## 11. Найти и вывести на экран названия всех подкатегорий товаров.
```sql
SELECT name FROM Production.ProductSubcategory;
```
## 12. Найти и вывести на экран названия всех категорий товаров.
```sql
SELECT name FROM Production.ProductCategory;
```
## 13. Найти и вывести на экран имена всех клиентов из таблицы Person, у которых обращение (Title) указано как «Mr.».
```sql
SELECT FirstName FROM Person.Person WHERE title = 'Mr.';
```
## 14. Найти и вывести на экран имена всех клиентов из таблицы Person, для которых не определено обращение (Title)
```sql
SELECT FirstName FROM Person.Person WHERE title is NULL;
```
