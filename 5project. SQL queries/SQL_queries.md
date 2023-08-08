 1. Отобразить все записи из таблицы `company` по компаниям, которые закрылись.

```SQL
SELECT COUNT(id)
FROM company
WHERE status = 'closed';
```

Результат: 
| count | 
|----   |  
| 2584 |
___

 2. Отобразитье количество привлечённых средств для новостных компаний США. Использовать данные из таблицы `company`. Отсортировать таблицу по убыванию значений в поле `funding_total`.

```SQL
SELECT funding_total
FROM company
WHERE country_code = 'USA' AND category_code = 'news'
ORDER BY funding_total DESC;
```

Результат: 
| funding_total | 
|------------   |  
| 6.22553e+08 | 
| 2.5e+08 | 
| 1.605e+08 | 
| 1.28e+08 | 
|...|
___

 3. Найти общую сумму сделок по покупке одних компаний другими в долларах.  
Отобрать сделки, которые осуществлялись только за наличные с 2011 по 2013 год включительно.

```SQL
SELECT SUM(price_amount)
FROM acquisition
WHERE EXTRACT(YEAR FROM acquired_at) BETWEEN 2011 AND 2013
        AND term_code = 'cash';
```

Результат: 
| sum | 
|--   |  
| 1.37762e+11 |
___

 4. Отобразить имя, фамилию и названия аккаунтов людей в твиттере, у которых названия аккаунтов начинаются на `Silver`.

```SQL
SELECT first_name,
       last_name,
       twitter_username
FROM people
WHERE twitter_username LIKE 'Silver%';
```

Результат: 
| first_name | last_name | twitter_username |
|---------   |--------   |---------------   | 
| Rebecca	| Silver | SilverRebecca | 
| Silver	| Teede | SilverMatrixx | 
| Mattias	| Guilotte | Silverreven | 
___

 5. Вывести на экран всю информацию о людях, у которых названия аккаунтов в твиттере содержат подстроку `money`, а фамилия начинается на `K`.

```SQL
SELECT *
FROM people
WHERE twitter_username LIKE '%money%' AND last_name LIKE 'K%';
```

Результат: 
| id	| first_name | last_name | company_id | twitter_username | created_at | updated_at |
|--   |---------   |--------   |---------   |---------------   |---------   |---------   | 
| 63081	| Gregory	| Kim | | gmoney75 | 2010-07-13 03:46:28 | 2011-12-12 22:01:34 | 
___

 6. Для каждой страны отобразить общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране.  
Страну, в которой зарегистрирована компания, можно определить по коду страны.
Отсортировать данные по убыванию суммы.
```SQL
SELECT country_code,
       SUM(funding_total) AS total
FROM company 
GROUP BY country_code
ORDER BY total DESC;
```

Результат: 
| country_code | sum |
|-----------   |--   | 
| USA	| 3.10588e+11	| 
| GBR | 1.77056e+10	| 
|   | 1.08559e+10	| 
| CHN | 1.06897e+10	| 
| CAN | 9.86636e+09	| 
|...| 
___

 7. Составить таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату.  
Оставить в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.  

```SQL
SELECT funded_at,
       MIN(raised_amount) AS min_amount,
       MAX(raised_amount) AS max_amount
FROM funding_round
GROUP BY funded_at
HAVING MIN(raised_amount) != 0 AND MIN(raised_amount) != MAX(raised_amount);
```

Результат: 
| funded_at |	min |	max | 
|--------   |--   |--   | 
| 2012-08-22 |	40000 |	7.5e+07 | 
| 2010-07-25 |	3.27825e+06| 	9e+06 | 
| 2002-03-01 |	2.84418e+06 |	8.95915e+06 | 
| 2010-10-11 |	28000 |	2e+08 | 
| 2007-01-18 |	5.5e+06 |	2.3e+07 | 
|...| 
___

 8. Создать поле с категориями:
- Для фондов, которые инвестируют в 100 и более компаний, назначьте категорию high_activity.
- Для фондов, которые инвестируют в 20 и более компаний до 100, назначьте категорию middle_activity.
- Если количество инвестируемых компаний фонда не достигает 20, назначьте категорию low_activity.  
Отобразите все поля таблицы fund и новое поле с категориями.

```SQL
SELECT *,
       CASE
           WHEN invested_companies < 20 THEN 'low_activity'
           WHEN invested_companies >= 20 AND invested_companies < 100 THEN 'middle_activity'
           ELSE 'high_activity'
        END
FROM fund;
```

Результат:  

| id |	name	| founded_at	| domain	| twitter_username |	country_code	| investment_rounds |	invested_companies	| milestones |	created_at	| updated_at	| case | 
|-   |-----   |----------   |------   |---------------   |-------------   |----------------   |------------------   |---------   |-----------   |----------   |---   | 
| 1  | Greylock Partners |	1965-01-01	| greylock.com	| greylockvc	| USA	| 307	| 196 |	0	| 2007-05-25 20:18:23	| 2012-12-27 00:42:24	| high_activity |
| 10	| Mission Ventures	| 1996-01-01	| missionventures.com	|	USA	| 58	| 33	| 0	| 2007-06-05 05:24:58	| 2013-10-10 22:06:31 |	| middle_activity |
| 100	| Kapor Enterprises, Inc.	|	kei.com	|	USA	| 2	| 1 |	0	| 2007-07-12 09:42:21	| 2008-11-21 05:41:53	| | | low_activity |
| 1000	| Speed Ventures |	|	|	|	|	0	| 0	| 1	| 2008-04-13 23:52:27 |	2008-12-10 09:37:18 |	low_activity | 
|...|
___  

 9. Для каждой из категорий, назначенных в предыдущем задании, посчитать округлённое до ближайшего целого числа среднее количество инвестиционных раундов, в которых фонд принимал участие.  
Вывести на экран категории и среднее число инвестиционных раундов. Отсортировать таблицу по возрастанию среднего.

```SQL
SELECT a.activity,
       ROUND(AVG(investment_rounds))
FROM (SELECT *,
             CASE
                  WHEN invested_companies>=100 THEN 'high_activity'
                  WHEN invested_companies>=20 THEN 'middle_activity'
                  ELSE 'low_activity'
             END AS activity
      FROM fund) AS a
GROUP BY a.activity
ORDER BY ROUND(AVG(investment_rounds));
```

Результат:  
| activity	| round | 
|--------   |----   | 
| low_activity	| 2 |
| middle_activity	| 51 |
| high_activity	| 252 |  
___  

 10. Проанализироваьб, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы. 
Для каждой страны посчитать минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой страны, основанные с 2010 по 2012 год включительно.  
Исключить страны с фондами, у которых минимальное число компаний, получивших инвестиции, равно нулю.  
Выгрузить десять самых активных стран-инвесторов: отсортировать таблицу по среднему количеству компаний от большего к меньшему.  
Затем добавить сортировку по коду страны в лексикографическом порядке.

```SQL
SELECT country_code,
       MIN(invested_companies),
       MAX(invested_companies),
       AVG(invested_companies)
FROM fund
WHERE EXTRACT(YEAR FROM founded_at) BETWEEN 2010 and 2012
GROUP BY country_code
HAVING MIN(invested_companies) != 0
ORDER BY AVG(invested_companies) DESC,
         country_code
LIMIT 10;
```

Результат:  

| country_code	| min	| max	| avg | 
|------------   |--   |--   |--   | 
| BGR	| 25	| 35	| 30 | 
| CHL	| 29	| 29	| 29 |
| UKR	| 8	| 10	| 9 |
| LTU	| 5	| 5	| 5 |
| IRL	| 4	| 5	| 4.5 | 
|...|
___ 

 11. Отобразить имя и фамилию всех сотрудников стартапов. Добавить поле с названием учебного заведения, которое окончил сотрудник, если эта информация известна.

```SQL
SELECT p.first_name,
       p.last_name,
       e.instituition
FROM people AS p
LEFT JOIN education AS e ON p.id=e.person_id;
```

Результат:   
| first_name	| last_name	| instituition | 
|----------   |--------   |-----------   | 
| John	| Green	| Washington University, St. Louis |
| John	| Green	| Boston University | 
| David	| Peters	| Rice University | 
|Dan |	Birdwhistell	| University of Cambridge | 
|...|
___ 

 12. Для каждой компании найти количество учебных заведений, которые окончили её сотрудники.  
Вывести название компании и число уникальных названий учебных заведений. Составить топ-5 компаний по количеству университетов.  

```SQL
SELECT c.name,
       COUNT(DISTINCT e.instituition)
FROM company AS c
JOIN people AS p ON c.id=p.company_id
JOIN education AS e ON p.id=e.person_id
GROUP BY c.name
ORDER BY COUNT(DISTINCT e.instituition) DESC
LIMIT 5;
```

Результат:   
| name	| count |
|----   |----   | 
| Google	| 167 | 
| Yahoo!	| 115 | 
| Microsoft	| 111 | 
| Knight Foundation	| 74 | 
| Comcast | 66 |
___  

 13. Составить список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.

```SQL
SELECT DISTINCT name 
FROM company
WHERE id IN (SELECT company_id
             FROM funding_round
             WHERE is_last_round = 1
                AND is_first_round = 1) AND status = 'closed';
```

Результат:  
| name |
|---   | 
| 10BestThings | 
| 11i Solutions | 
| 169 ST. | 
| 1bib | 
| 1Cast | 
|...|
___ 

 14. Составить список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.

```SQL
SELECT DISTINCT id
FROM people
WHERE company_id IN (SELECT DISTINCT id
                     FROM company
                     WHERE id IN (SELECT company_id
                                  FROM funding_round
                                  WHERE is_last_round = 1
                                      AND is_first_round = 1) AND status = 'closed');
```

Результат: 
| id | 
|-   | 
| 62 | 
| 97 | 
| 98 | 
| 225 | 
| 226| 
|...|
___  

 15. Составить таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, которое окончил сотрудник.

```SQL
SELECT DISTINCT p.id, 
       e.instituition
FROM people AS p
JOIN education AS e ON p.id=e.person_id
WHERE company_id IN (SELECT DISTINCT id
                     FROM company
                     WHERE id IN (SELECT company_id
                                  FROM funding_round
                                  WHERE is_last_round = 1
                                      AND is_first_round = 1) AND status = 'closed')
;
```

Результат:   
| id	| instituition | 
|--   |-----------   | 
| 349	| AKI | 
| 349	| ArtEZ Hogeschool voor de Kunsten | 
| 349	| Rijks Akademie | 
| 699	| Imperial College | 
|...| 
___

 16. Посчитать количество учебных заведений для каждого сотрудника из предыдущего задания.  
При подсчёте учитывать, что некоторые сотрудники могли окончить одно и то же заведение дважды.

```SQL
SELECT DISTINCT p.id, 
       COUNT(e.instituition)
FROM people AS p
JOIN education AS e ON p.id=e.person_id
WHERE company_id IN (SELECT DISTINCT id
                     FROM company
                     WHERE id IN (SELECT company_id
                                  FROM funding_round
                                  WHERE is_last_round = 1
                                      AND is_first_round = 1) AND status = 'closed')
GROUP BY p.id;
```

Результат:   

| id	| count | 
|--   |----   | 
| 349	| 3 | 
| 699	| 1 | 
| 779	| 2 | 
| 968	| 1 |
|...| 
___ 

 17. Дополнить предыдущий запрос и вывести среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники разных компаний.  
Нужно вывести только одну запись, группировка здесь не понадобится.

```SQL
SELECT AVG(t.total)
 FROM (SELECT DISTINCT p.id,
              COUNT(e.instituition) AS total
       FROM people AS p
       JOIN education AS e ON p.id=e.person_id
       WHERE company_id IN (SELECT DISTINCT id
                            FROM company
                            WHERE id IN (SELECT company_id
                                         FROM funding_round
                                         WHERE is_last_round = 1
                                             AND is_first_round = 1) AND status = 'closed')
       GROUP BY p.id) AS t;
```

Результат:   

| avg | 
|--   | 
| 1.41509 | 
___  

 18. Написать похожий запрос: вывести среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники Facebook*.  
*(сервис, запрещённый на территории РФ)

```SQL
SELECT AVG(t.total)
FROM (SELECT DISTINCT p.id,
             COUNT(e.instituition) AS total
       FROM people AS p
       JOIN education AS e ON p.id=e.person_id
       WHERE company_id IN (SELECT id
                            FROM company
                            WHERE name = 'Facebook')
       GROUP BY p.id) AS t;
```

Результат:  

| avg | 
|--   | 
| 1.51111 | 
___  

 19. Составить таблицу из полей:
- `name_of_fund` — название фонда;
- `name_of_company` — название компании;
- `amount` — сумма инвестиций, которую привлекла компания в раунде.
В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, а раунды финансирования проходили с 2012 по 2013 год включительно.

```SQL
SELECT f.name AS name_of_fund, 
       c.name AS name_of_company, 
	   fr.raised_amount AS amount
FROM investment AS i 
LEFT JOIN company AS c ON c.id = i.company_id
LEFT JOIN fund AS f ON f.id = i.fund_id
INNER JOIN (SELECT * 
           FROM funding_round
           WHERE EXTRACT(YEAR FROM funded_at) BETWEEN 2012 AND 2013) AS fr ON fr.id = i.funding_round_id
WHERE c.milestones > 6;
```

Результат:  

| name_of_fund	| name_of_company	| amount | 
|------------   |--------------   |-----   | 
| SAP Ventures |	OpenX	| 2.50112e+07 | 
| Samsung Ventures	| OpenX	| 2.50112e+07 | 
| Index Ventures	| OpenX	| 2.50112e+07 | 
| Presidio Ventures	| OpenX |	2.50112e+07 | 
|...| 
___  

 20. Выгрузите таблицу, в которой будут такие поля:
- название компании-покупателя;
- сумма сделки;
- название компании, которую купили;
- сумма инвестиций, вложенных в купленную компанию;
- доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа.
Не учитывать те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, исключите такую компанию из таблицы. 
Отсортировать таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в лексикографическом порядке.
Ограничить таблицу первыми десятью записями.

```SQL
SELECT buyers.name_buyers, 
       buyers.deal_amount, 
       sold.sold_company, 
       sold.invest_amount, 
       ROUND(deal_amount/invest_amount) AS share
FROM (SELECT a.id AS id,
             c.name AS name_buyers,
             a.price_amount AS deal_amount
      FROM acquisition AS a
      LEFT JOIN company AS c ON a.acquiring_company_id=c.id
      WHERE price_amount > 0) AS buyers
JOIN (SELECT a.id AS id,
             c.name AS sold_company,
             c.funding_total AS invest_amount
      FROM acquisition AS a
      LEFT JOIN company AS c ON a.acquired_company_id=c.id
      WHERE funding_total > 0) AS sold ON buyers.id=sold.id
ORDER BY deal_amount DESC, sold_company
LIMIT 10;
```

Результат:  

| name_buyers	| deal_amount	| sold_company	| invest_amount	| share | 
|----------   |----------   |------------   |------------   |----   | 
| Microsoft |	8.5e+09	| Skype	| 7.6805e+07	| 111 | 
| Scout Labs	| 4.9e+09	| Varian Semiconductor Equipment Associates	| 4.8e+06	| 1021 | 
| Broadcom	| 3.7e+09	| Aeluros	| 7.97e+06	| 464 | 
| Broadcom	| 3.7e+09	| NetLogic Microsystems	| 1.88527e+08	| 20 | 
|...| 
___  

 21. Выгрузить таблицу, в которую войдут названия компаний из категории `social`, получившие финансирование с 2010 по 2013 год включительно.  
Проверить, что сумма инвестиций не равна нулю. Вывести также номер месяца, в котором проходил раунд финансирования.

```SQL
SELECT c.name, EXTRACT(MONTH FROM funded_at)
FROM company AS c
LEFT JOIN funding_round AS fr ON c.id=fr.company_id
WHERE category_code = 'social'
    AND EXTRACT(YEAR FROM funded_at) BETWEEN 2010 and 2013 AND raised_amount !=0;
```

Результат:  

| name	| date_part | 
|----   |--------   | 
| Klout	| 1 | 
| WorkSimple	| 3 | 
| HengZhi	| 1 | 
| Twitter	| 1 | 
|...| 
___  

 22. Отобрать данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды.  
Сгруппировать данные по номеру месяца и получить таблицу, в которой будут поля:
- номер месяца, в котором проходили раунды;
- количество уникальных названий фондов из США, которые инвестировали в этом месяце;
- количество компаний, купленных за этот месяц;
- общая сумма сделок по покупкам в этом месяце.

```SQL
WITH
fund_usa AS (SELECT EXTRACT(MONTH FROM funded_at) AS month,
                    COUNT(DISTINCT f.name) AS total_fund
             FROM fund AS f
             LEFT JOIN investment AS i ON f.id=i.fund_id
             LEFT JOIN funding_round AS fr ON i.funding_round_id=fr.id
             WHERE f.country_code = 'USA' 
                 AND EXTRACT(YEAR FROM funded_at) BETWEEN 2010 and 2013
             GROUP BY EXTRACT(MONTH FROM funded_at)),
bought_month AS (SELECT EXTRACT(MONTH FROM acquired_at) AS month,
                        COUNT(acquired_company_id) AS total_company,
                        SUM(price_amount) AS total_amount
                 FROM acquisition
                 WHERE EXTRACT(YEAR FROM acquired_at) BETWEEN 2010 and 2013
                 GROUP BY EXTRACT(MONTH FROM acquired_at))

SELECT fund_usa.month,
       fund_usa.total_fund,
       bought_month.total_company,
       bought_month.total_amount
FROM fund_usa
JOIN bought_month ON fund_usa.month=bought_month.month;
```

Результат:  

| month	| total_fund	| total_company	| total_amount | 
|----   |----------   |------------   |-----------   | 
| 1	| 815 |	600	| 2.71083e+10 | 
| 2	| 637	| 418	| 4.13903e+10 | 
| 3	| 695	| 458	| 5.95016e+10 | 
| 4	| 718	| 411	| 3.03837e+10 |
|...| 
___  

 23. Составить сводную таблицу и вывести среднюю сумму инвестиций для стран, в которых есть стартапы, зарегистрированные в 2011, 2012 и 2013 годах.  
Данные за каждый год должны быть в отдельном поле.  
Отсортировать таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.

```SQL
WITH
inv_2011 AS (SELECT country_code AS country, AVG(funding_total) AS aver_amount_2011
             FROM company
             WHERE EXTRACT(YEAR FROM founded_at) = 2011
             GROUP BY country_code),
inv_2012 AS (SELECT country_code AS country, AVG(funding_total) AS aver_amount_2012
             FROM company
             WHERE EXTRACT(YEAR FROM founded_at) = 2012
             GROUP BY country_code),
inv_2013 AS (SELECT country_code AS country, AVG(funding_total) AS aver_amount_2013
             FROM company
             WHERE EXTRACT(YEAR FROM founded_at) = 2013
             GROUP BY country_code)
SELECT inv_2011.country,
       inv_2011.aver_amount_2011,
       inv_2012.aver_amount_2012,
       inv_2013.aver_amount_2013
FROM inv_2011
JOIN inv_2012 ON inv_2011.country=inv_2012.country
JOIN inv_2013 ON inv_2012.country=inv_2013.country
ORDER BY aver_amount_2011 DESC;
```

Результат:  

| country	| aver_amount_2011	| aver_amount_2012	| aver_amount_2013 | 
|------   |----------------   |----------------   |---------------   | 
| PER	| 4e+06	| 41000	| 25000 | 
| USA	| 2.24396e+06	| 1.20671e+06	| 1.09336e+06 | 
| HKG	| 2.18078e+06	| 226227	| 0 | 
| PHL	| 1.75e+06	| 4218.75	| 2500 | 
|...| 
___
