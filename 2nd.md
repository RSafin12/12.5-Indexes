```
select distinct concat(c.last_name, ' ', c.first_name), 
sum(p.amount) over (partition by c.customer_id, f.title)

from payment p, rental r, customer c, inventory i, film f

where date(p.payment_date) = '2005-07-30' 
and p.payment_date = r.rental_date 
and r.customer_id = c.customer_id 
and i.inventory_id = r.inventory_id
```

![original](https://github.com/RSafin12/12.5-Indexes/blob/main/original.png)
Первая строка простая, тут мы запрашиваем фио пользователя  
`SELECT DISTINCT CONCAT(c.last_name, ' ', c.first_name) AS FIO,`  

Оконная функция.  
Здесь мы "разбиваем" выборку на партиции по c.customer_id(т.е. по каждому пользователю) и для каждого пользователя находим сумму платежей. Однако не очевидно, зачем тут f.title, но без нее другой результат.  
`SUM(p.amount) OVER (PARTITION BY c.customer_id, f.title) AS Сумма_Платежей`  
Если наша цель найти соответствие пользователь - платежи, то наверно можно упростить/переделать.  

Здесь перечислены используемые таблицы  
`FROM payment p, customer c, inventory i, film f`  

Условия.  
Конкретизируем дату  
`WHERE DATE(p.payment_date) = '2005-07-30' `  
`AND p.payment_date = r.rental_date` - нужно, условие совпадения по дате  
`AND r.customer_id = c.customer_id ` - нужно, условие совпадения по id  
`AND i.inventory_id = r.inventory_id `- Тут связующая таблица к film, наверно можно было использовать JOIN  

Пробовал двигаться в этом напаравлении  
```
SELECT DISTINCT CONCAT(c.last_name, ' ', c.first_name) AS FIO, 
SUM(p.amount) OVER (PARTITION BY c.customer_id, f.title)
FROM payment p
LEFT JOIN rental r USING(rental_id)
LEFT JOIN customer c ON r.customer_id = c.customer_id
LEFT JOIN inventory i USING(inventory_id)
LEFT JOIN film f USING(film_id)
WHERE DATE(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id;
```
Финальный вариант с учетом рекомендаций  
```
SELECT  CONCAT(c.last_name, ' ', c.first_name), 
SUM(p.amount)  
FROM payment p
LEFT JOIN rental r USING(rental_id)
LEFT JOIN customer c ON r.customer_id = c.customer_id
WHERE DATE(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id 
GROUP BY c.customer_id 
ORDER BY c.customer_id 
```
Узкие места  
- лишние таблицы  
- лишние условие  
- оконная функция, с ней запрос тяжелее  
- кажется, с JOIN работает быстрее, чем с несколькими таблицами в FROM  
  

Итого
1. Нужно понять что именно выводит запрос.  
2. После декомпозировать его на отдельные составляющие.  
3. Подумать как можно "упростить" каждую составляющую или найти более легковесный вариант.  
4. Подумать, а все ли таблицы в запросе нужны? Можно ли обойтись без каких-либо таблиц?  





































