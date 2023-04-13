[original](https://github.com/RSafin12/12.5-Indexes/blob/main/2nd.md)
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
нужно, условие совпадения по дате
`AND p.payment_date = r.rental_date`
нужно, условие совпадения по id
`AND r.customer_id = c.customer_id `
Тут связующая таблица к film, наверно можно было использовать JOIN
`AND i.inventory_id = r.inventory_id `
