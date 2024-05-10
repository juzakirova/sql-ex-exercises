## 1
Найдите номер модели, скорость и размер жесткого диска для всех ПК стоимостью менее 500 дол. Вывести: model, speed и hd 

```sql
SELECT model, speed, hd
  FROM PC
 WHERE price < 500
```
## 2
Найдите производителей принтеров. Вывести: maker
``` sql
SELECT DISTINCT maker
  FROM Product
 WHERE type = 'Printer'
```
 
## 3
 Найдите номер модели, объем памяти и размеры экранов ПК-блокнотов, цена которых превышает 1000 дол.
 ```sql
 SELECT model, ram, screen
  FROM Laptop
 WHERE price > 1000
```

## 4
Найдите все записи таблицы Printer для цветных принтеров.
```sql
SELECT * 
  FROM Printer
 WHERE color = 'y'
```

## 5
Найдите номер модели, скорость и размер жесткого диска ПК, имеющих 12x или 24x CD и цену менее 600 дол.
```sql
SELECT model, speed, hd
  FROM PC
 WHERE price < 600 AND (cd = '12x' OR cd = '24x')
```

## 6
Для каждого производителя, выпускающего ПК-блокноты c объёмом жесткого диска не менее 10 Гбайт, найти скорости таких ПК-блокнотов. Вывод: производитель, скорость.
```sql
SELECT DISTINCT Product.maker, Laptop.speed
  FROM Product
 INNER JOIN Laptop
    ON Laptop.model = Product.model
 WHERE Laptop.hd >= 10
```

## 7
Найдите номера моделей и цены всех имеющихся в продаже продуктов (любого типа) производителя B (латинская буква).
```sql
SELECT DISTINCT p.model, COALESCE (pc.price, l.price, pr.price) AS price
  FROM product AS p
  LEFT JOIN pc 
    ON p.model = pc.model
  LEFT JOIN laptop AS l
    ON p.model = l.model
  LEFT JOIN printer AS pr
    ON p.model = pr.model
 WHERE p.maker = 'B'
   AND COALESCE (pc.model, pr.model, l.model) IS NOT NULL
```

--почему не прописать в coalesce в select 0, ведь модель может быть в Product и, например, в PC, но цена в PC отсутствовать--

## 8
Найдите производителя, выпускающего ПК, но не ПК-блокноты.
```sql
SELECT maker
  FROM Product
 WHERE type IN ('PC') EXCEPT (SELECT maker
                                FROM Product
                               WHERE type='Laptop')
```
## 9
Найдите производителей ПК с процессором не менее 450 Мгц. Вывести: Maker
```sql
SELECT DISTINCT Product.maker
  FROM Product
 WHERE EXISTS (SELECT PC.speed
                 FROM PC
                WHERE PC.model = Product.model AND PC.speed >= 450)
```

## 10
Найдите модели принтеров, имеющих самую высокую цену. Вывести: model, price
```sql
SELECT model, price
  FROM printer
 WHERE price IN (SELECT MAX(price)
                   FROM printer)
```

## 11
Найдите среднюю скорость ПК.
```sql
SELECT AVG(speed) AS avg_speed
  FROM PC
```

## 12
Найдите среднюю скорость ПК-блокнотов, цена которых превышает 1000 дол.
```sql
SELECT AVG(speed)
  FROM laptop
 WHERE price > 1000
```

## 13
Найдите среднюю скорость ПК, выпущенных производителем A.
```sql
SELECT AVG(speed) AS avg_speed
  FROM PC
 INNER JOIN product 
    ON pc.model = product.model
 WHERE maker = 'A'
```

## 14
Найдите класс, имя и страну для кораблей из таблицы Ships, имеющих не менее 10 орудий.
```sql
SELECT Classes.class, Ships.name, Classes.country
  FROM Ships
 INNER JOIN Classes
    ON Classes.class=Ships.class
 WHERE Classes.numGuns >= 10
```

## 15
Найдите размеры жестких дисков, совпадающих у двух и более PC. Вывести: HD
```sql
SELECT HD
  FROM pc
 GROUP BY HD
HAVING COUNT(model) >= 2
```

## 16
Найдите пары моделей PC, имеющих одинаковые скорость и RAM. В результате каждая пара указывается только один раз, т.е. (i,j), но не (j,i), Порядок вывода: модель с большим номером, модель с меньшим номером, скорость и RAM.
```sql
SELECT DISTINCT pc1.model, pc2.model, pc1.speed, pc1.ram
  FROM pc AS pc1
 CROSS JOIN pc AS pc2
 WHERE pc1.model > pc2.model
   AND pc1.speed = pc2.speed
   AND pc1.ram = pc2.ram
```

## 17
Найдите модели ПК-блокнотов, скорость которых меньше скорости каждого из ПК.  
Вывести: type, model, speed

```sql
SELECT DISTINCT Product.type, Laptop.model, Laptop.speed
  FROM Laptop
 INNER JOIN Product
    ON Product.model = Laptop.model
 WHERE Laptop.speed < ALL(SELECT DISTINCT speed
                            FROM PC)
```

## 18
Найдите производителей самых дешевых цветных принтеров. Вывести: maker, price
```sql
SELECT DISTINCT maker, price
  FROM printer AS pri
 INNER JOIN product AS pro
    ON pri.model = pro.model
   AND color = 'y'
   AND price IN (SELECT MIN(price)
                   FROM printer
                  WHERE color = 'y'
                 )
```

## 19
Для каждого производителя, имеющего модели в таблице Laptop, найдите средний размер экрана выпускаемых им ПК-блокнотов.  
Вывести: maker, средний размер экрана.
```sql
SELECT maker, AVG(screen) AS средний_размер_экрана
  FROM product AS p
 INNER JOIN laptop AS l
    ON p.model = l.model
 GROUP BY maker
```

## 20
Найдите производителей, выпускающих по меньшей мере три различных модели ПК. Вывести: Maker, число моделей ПК.
```sql
SELECT maker, COUNT(model) AS число_моделей_ПК
  FROM product
 WHERE type = 'PC'
 GROUP BY maker
HAVING COUNT(model) >= 3
```

## 21
Найдите максимальную цену ПК, выпускаемых каждым производителем, у которого есть модели в таблице PC.  
Вывести: maker, максимальная цена.
```sql
SELECT maker, MAX(price) AS максимальная_цена
  FROM product 
 INNER JOIN pc
    ON product.model = pc.model
 GROUP BY maker
```

## 22
Для каждого значения скорости ПК, превышающего 600 МГц, определите среднюю цену ПК с такой же скоростью. Вывести: speed, средняя цена.
```sql
SELECT speed, AVG(price) AS средняя_цена
  FROM pc
 WHERE speed > 600
 GROUP BY speed
```

## 23
Найдите производителей, которые производили бы как ПК  
со скоростью не менее 750 МГц, так и ПК-блокноты со скоростью не менее 750 МГц.  
Вывести: Maker
```sql
SELECT DISTINCT Product.maker
  FROM Product
 INNER JOIN PC
    ON Product.model=PC.model AND PC.speed>=750

INTERSECT

SELECT DISTINCT Product.maker
  FROM Product
 INNER JOIN Laptop
    ON Product.model=Laptop.model AND Laptop.speed>=750
```

## 24
Перечислите номера моделей любых типов, имеющих самую высокую цену по всей имеющейся в базе данных продукции. 
```sql
WITH mod_price AS (SELECT model, price
                     FROM PC
                    UNION 
                   SELECT model, price
                     FROM Printer
                    UNION 
                   SELECT model, price
                     FROM Laptop
                   )

SELECT DISTINCT p.model
  FROM mod_price AS p
 WHERE price IN (SELECT MAX(price)
                   FROM mod_price)
```

## 25
Найдите производителей принтеров, которые производят ПК с наименьшим объемом RAM и с самым быстрым процессором среди всех ПК, имеющих наименьший объем RAM. Вывести: Maker
```sql
select distinct maker  
  from pc inner join product p
       on p.model = pc.model  
where       ram in (select top 1 ram from pc order by ram, speed desc)
       and speed in (select top 1 speed from pc order by ram, speed desc)
       and maker in (select maker from product where type='printer')
```

## 26
Найдите среднюю цену ПК и ПК-блокнотов, выпущенных производителем A (латинская буква). Вывести: одна общая средняя цена.
```sql
SELECT AVG(COALESCE(pc.price, l.price, 0)) AS одна_общая_средняя_цена
  FROM product AS pr
  LEFT JOIN PC
    ON pr.model = pc.model
  LEFT JOIN laptop AS l
    ON pr.model = l.model
 WHERE pr.maker = 'A'
   AND type IN ('PC', 'laptop')
```

## 27
Найдите средний размер диска ПК каждого из тех производителей, которые выпускают и принтеры. Вывести: maker, средний размер HD.

```sql
SELECT Product.maker, AVG(PC.hd) AS avg_hd
  FROM Product
 INNER JOIN PC
    ON Product.model=PC.model
 WHERE Product.maker IN (SELECT maker
                           FROM Product
                          WHERE type='PC'
                         
                         INTERSECT
                         SELECT maker
                           FROM Product WHERE type='Printer') 
 GROUP BY Product.maker
```

## 28
Используя таблицу Product, определить количество производителей, выпускающих по одной модели.

```sql
SELECT COUNT(maker) AS qty
  FROM (
        SELECT maker 
          FROM product
         GROUP BY maker
        HAVING COUNT(model) = 1
        ) AS pr
```

## 29
В предположении, что приход и расход денег на каждом пункте приема фиксируется не чаще одного раза в день [т.е. первичный ключ (пункт, дата)], написать запрос с выходными данными (пункт, дата, приход, расход). Использовать таблицы Income_o и Outcome_o.

```sql
SELECT i.point, i.date, inc, out
  FROM income_o AS i
  LEFT JOIN outcome_o AS o    
    ON i.date = o.date
   AND i.point = o.point

 UNION

SELECT o.point, o.date, inc, out
  FROM outcome_o AS o
  LEFT JOIN income_o AS i   
    ON i.date = o.date
   AND i.point = o.point
```

## 30
В предположении, что приход и расход денег на каждом пункте приема фиксируется произвольное число раз (первичным ключом в таблицах является столбец code), требуется получить таблицу, в которой каждому пункту за каждую дату выполнения операций будет соответствовать одна строка.  
Вывод: point, date, суммарный расход пункта за день (out), суммарный приход пункта за день (inc). Отсутствующие значения считать неопределенными (NULL).

```sql
SELECT COALESCE(t1.point, t2.point) AS point,
       COALESCE(t1.date, t2.date) AS date,
       sum_out,
       sum_inc
  FROM
       (SELECT point, date, SUM(inc) AS sum_inc
          FROM income 
         GROUP BY point, date) AS t1

  FULL JOIN  

        (SELECT point, date, SUM(out) AS sum_out
           FROM outcome
          GROUP BY point, date) AS t2
    ON t1.point = t2.point
   AND t1.date = t2.date
```

## 31
Для классов кораблей, калибр орудий которых не менее 16 дюймов, укажите класс и страну.

```sql
SELECT class, country
  FROM Classes
 WHERE bore >= 16
```

## 32


## 33
Укажите корабли, потопленные в сражениях в Северной Атлантике (North Atlantic). Вывод: ship.

```sql
SELECT ship
  FROM Outcomes
 WHERE battle = 'North Atlantic' AND result = 'sunk'
```

## 34
По Вашингтонскому международному договору от начала 1922 г. запрещалось строить линейные корабли водоизмещением более 35 тыс.тонн. Укажите корабли, нарушившие этот договор (учитывать только корабли c известным годом спуска на воду). Вывести названия кораблей.
```sql
SELECT s.name
  FROM ships AS s
 INNER JOIN classes AS c
    ON s.class = c.class
   AND c.type = 'bb'
   AND c.displacement > 35000
   AND s.launched >= 1922
```

## 35
В таблице Product найти модели, которые состоят только из цифр или только из латинских букв (A-Z, без учета регистра).  
Вывод: номер модели, тип модели.
```sql
SELECT model, type
  FROM product
 WHERE model REGEXP '^[0-9]*$'
    OR model REGEXP '^[a-z]*$'
```

## 36
Перечислите названия головных кораблей, имеющихся в базе данных (учесть корабли в Outcomes).
```sql
SELECT DISTINCT c.class
  FROM classes AS c
  LEFT JOIN outcomes AS o
    ON c.class = o.ship
  LEFT JOIN ships AS s
    ON c.class = s.name
 WHERE o.ship IS NOT NULL
   OR s.name IS NOT NULL
```

## 37
Найдите классы, в которые входит только один корабль из базы данных (учесть также корабли в Outcomes). 
```sql
SELECT class
  FROM (SELECT c.class, s.name AS ship_name
          FROM classes AS c
         INNER JOIN ships AS s
            ON c.class = s.class

         UNION

        SELECT c.class, o.ship AS ship_name
          FROM classes AS c
         INNER JOIN outcomes AS o
            ON c.class = o.ship
       ) AS t
 GROUP BY class
HAVING COUNT(ship_name) = 1
```

## 38
Найдите страны, имевшие когда-либо классы обычных боевых кораблей ('bb') и имевшие когда-либо классы крейсеров ('bc').
```sql
SELECT DISTINCT country
  FROM Classes
 WHERE type='bb' AND country IN (SELECT DISTINCT country
                                   FROM Classes
                                  WHERE type='bc')
```

## 39
Найдите корабли, `сохранившиеся для будущих сражений`; т.е. выведенные из строя в одной битве (damaged), они участвовали в другой, произошедшей позже.

```sql
SELECT DISTINCT o.ship
  FROM outcomes AS o
 INNER JOIN battles AS b
    ON o.battle = b.name
 INNER JOIN (SELECT ship, battle, date
               FROM outcomes AS o
              INNER JOIN battles AS b
                 ON o.battle = b.name
              WHERE result = 'damaged') AS dmgd
   ON o.ship = dmgd.ship
  AND b.date > dmgd.date
```

## 40
Найти производителей, которые выпускают более одной модели, при этом все выпускаемые производителем модели являются продуктами одного типа.  
Вывести: maker, type

```sql
SELECT DISTINCT maker, type
  FROM product
 WHERE maker IN (SELECT maker
                   FROM product
                  GROUP BY maker
                 HAVING COUNT(DISTINCT type) = 1
                    AND COUNT(model) > 1)
```

## 41
Для каждого производителя, у которого присутствуют модели хотя бы в одной из таблиц PC, Laptop или Printer, определить максимальную цену на его продукцию.  
Вывод: имя производителя, если среди цен на продукцию данного производителя присутствует NULL, то выводить для этого производителя NULL, иначе максимальную цену.
```sql
SELECT maker,
       CASE
       WHEN MIN(price) = 0
       THEN NULL
       ELSE MAX(price)
        END
  FROM (SELECT maker, ISNULL(price, 0) AS price
          FROM product AS p
         INNER JOIN pc
            ON p.model = pc.model
        
         UNION
         
        SELECT maker, ISNULL(price, 0) AS price
          FROM product AS p
         INNER JOIN laptop AS l
            ON p.model = l.model

         UNION

        SELECT maker, ISNULL(price, 0) AS price
          FROM product AS p
         INNER JOIN printer AS pr
            ON p.model = pr.model
      ) AS t
 GROUP BY maker
```

## 42
Найдите названия кораблей, потопленных в сражениях, и название сражения, в котором они были потоплены.
```sql
SELECT ship, battle
  FROM Outcomes
 WHERE result = 'sunk'
```

## 43
Укажите сражения, которые произошли в годы, не совпадающие ни с одним из годов спуска кораблей на воду.
```sql
SELECT name
  FROM battles
 WHERE DATEPART(yy, date) NOT IN (SELECT DISTINCT launched
                                    FROM ships
                                   WHERE launched IS NOT NULL)
```

## 44
Найдите названия всех кораблей в базе данных, начинающихся с буквы R.
```sql
SELECT name
  FROM ships
 WHERE name LIKE 'R%'

UNION

SELECT ship
  FROM outcomes
 WHERE ship LIKE 'R%'
```

## 45
Найдите названия всех кораблей в базе данных, состоящие из трех и более слов (например, King George V).  Считать, что слова в названиях разделяются единичными пробелами, и нет концевых пробелов.
```sql
SELECT name
  FROM ships
 WHERE name LIKE '% % %'

UNION

SELECT ship
  FROM outcomes
 WHERE ship LIKE '% % %'
```
## 46
Для каждого корабля, участвовавшего в сражении при Гвадалканале (Guadalcanal), вывести название, водоизмещение и число орудий.
```sql
SELECT o.ship, c.displacement, c.numGuns
  FROM outcomes AS o
  LEFT JOIN ships AS s
    ON s.name = o.ship
  LEFT JOIN classes AS c
    ON s.class = c.class
    OR o.ship = c.class
 WHERE o.battle = 'Guadalcanal'
```

## 47
Определить страны, которые потеряли в сражениях все свои корабли.
```sql
SELECT country
  FROM (

SELECT country, COUNT(all_ships) AS cnt
  FROM classes AS c
  LEFT JOIN (SELECT DISTINCT name AS all_ships, class
               FROM ships

              UNION

             SELECT DISTINCT ship AS all_ships, ship AS class
               FROM outcomes
            ) AS s
   ON c.class = s.class
GROUP BY country

INTERSECT

SELECT country, COUNT(o.ship)
  FROM classes AS c
  LEFT JOIN ships AS s
    ON c.class = s.class
  LEFT JOIN outcomes AS o
    ON o.ship = s.name
    OR o.ship = c.class
 WHERE o.result = 'sunk'
 GROUP BY country
) AS total
```

## 48
Найдите классы кораблей, в которых хотя бы один корабль был потоплен в сражении. 
```sql
SELECT class
  FROM (SELECT class, result
          FROM ships AS s
         INNER JOIN outcomes AS o
            ON s.name = o.ship
         WHERE result = 'sunk'

         UNION

        SELECT class, result
          FROM classes AS c
         INNER JOIN outcomes AS o
            ON c.class = o.ship
         WHERE result = 'sunk'
        ) AS t
 GROUP BY class
HAVING COUNT(result) > 0
```

## 49
Найдите названия кораблей с орудиями калибра 16 дюймов (учесть корабли из таблицы Outcomes).
```sql
SELECT DISTINCT ship_name
  FROM (SELECT o.ship AS ship_name
          FROM outcomes AS o
         INNER JOIN classes AS c
            ON o.ship = c.class
         WHERE c.bore = 16

         UNION

        SELECT s.name AS ship_name
          FROM ships AS s
         INNER JOIN classes AS c
            ON s.class = c.class
         WHERE c.bore = 16
     ) AS all_ship
```

## 50
Найдите сражения, в которых участвовали корабли класса Kongo из таблицы Ships.
```sql
SELECT DISTINCT battle
  FROM outcomes
 WHERE ship IN (SELECT name
                  FROM ships
                 WHERE class = 'Kongo')
```

## 51
Найдите названия кораблей, имеющих наибольшее число орудий среди всех имеющихся кораблей такого же водоизмещения (учесть корабли из таблицы Outcomes). 
```sql
SELECT name
  FROM classes AS c
 INNER JOIN (SELECT displacement, MAX(numguns) AS numguns
               FROM (SELECT numguns, displacement
                       FROM classes AS c
                      INNER JOIN ships AS s
                         ON c.class = s.class

                      UNION

                     SELECT numguns, displacement
                       FROM classes AS c
                      INNER JOIN outcomes AS o
                         ON c.class = o.ship
                     ) AS t
              GROUP BY displacement
           ) AS t1
   ON c.numguns = t1.numguns
  AND c.displacement = t1.displacement
INNER JOIN (SELECT class, name
              FROM ships
             
              UNION 
    
             SELECT ship AS class, ship AS name
               FROM outcomes
           ) AS t2
   ON c.class = t2.class
```

## 52

## 53
Определите среднее число орудий для классов линейных кораблей.  
Получить результат с точностью до 2-х десятичных знаков.
```sql
SELECT ROUND(AVG(numGuns), 2)
  FROM classes
 WHERE type LIKE 'bb'
 GROUP BY type
```

## 54
С точностью до 2-х десятичных знаков определите среднее число орудий всех линейных кораблей (учесть корабли из таблицы Outcomes).

```sql
SELECT CAST(AVG(numGuns) AS NUMERIC(6, 2)) AS avg_numguns
  FROM (SELECT c.class, name AS ship, type, CAST(numGuns AS NUMERIC(4, 2)) AS numGuns
          FROM classes AS c
         INNER JOIN ships AS s
            ON s.class = c.class
         WHERE type LIKE 'bb'

         UNION 

        SELECT c.class, ship, type, CAST(numGuns AS NUMERIC(4, 2)) AS numGuns
          FROM classes AS c
         INNER JOIN outcomes AS o
            ON c.class = o.ship
         WHERE type LIKE 'bb'
     ) AS all_num
```

## 55
Для каждого класса определите год, когда был спущен на воду первый корабль этого класса. Если год спуска на воду головного корабля неизвестен, определите минимальный год спуска на воду кораблей этого класса. Вывести: класс, год.
```sql
SELECT c.class, MIN(launched) AS year
  FROM classes AS c
  LEFT JOIN ships AS s
    ON c.class = s.class
 WHERE c.class IN (SELECT c.class
                     FROM classes AS c
                     LEFT JOIN ships AS s
                       ON c.class = s.class
                      AND c.class = s.name  
                    WHERE launched IS NULL
                  )
 GROUP BY c.class

UNION

SELECT c.class, s.launched AS year
  FROM classes AS c
  LEFT JOIN ships AS s
    ON c.class = s.class
   AND c.class = s.name  
 WHERE launched IS NOT NULL
```

## 56
Для каждого класса определите число кораблей этого класса, потопленных в сражениях. Вывести: класс и число потопленных кораблей.
```sql
SELECT class, SUM(sunk) AS sunk 
  FROM (SELECT name, class, (CASE WHEN result = 'sunk' THEN 1 ELSE 0 END) AS sunk 
          FROM Ships AS s 
          LEFT JOIN Outcomes AS o
            ON o.ship = s.name 
        
         UNION 
     
        SELECT ship, class, (CASE WHEN result = 'sunk' THEN 1 ELSE 0 END) AS sunk 
          FROM Classes c
          LEFT JOIN (SELECT * 
                       FROM Outcomes
                      WHERE Ship NOT IN (SELECT name 
                                           FROM Ships)
                     ) AS o ON o.ship = c.class
        ) AS all_sunk_shups 
  GROUP BY class
```
## 57

## 58

## 59
Посчитать остаток денежных средств на каждом пункте приема для базы данных с отчетностью не чаще одного раза в день. Вывод: пункт, остаток.
```sql
SELECT inc.point, (sum_inc - ISNULL(sum_out, 0)) AS  balance
  FROM (SELECT point, SUM(inc) AS sum_inc
          FROM Income_o
         GROUP BY point) AS inc
  LEFT JOIN (SELECT point, SUM(out) AS sum_out
               FROM Outcome_o
              GROUP BY point) AS out
    ON out.point = inc.point
```

## 60
Посчитать остаток денежных средств на начало дня 15/04/01 на каждом пункте приема для базы данных с отчетностью не чаще одного раза в день. Вывод: пункт, остаток.  
Замечание. Не учитывать пункты, информации о которых нет до указанной даты.
```sql
SELECT inc.point, (sum_inc - ISNULL(sum_out, 0)) AS count_balance 
  FROM (SELECT point, SUM(inc) AS sum_inc
          FROM Income_o
         WHERE date < '2001-04-15 00:00:00.000'
         GROUP BY point) AS inc
  LEFT JOIN (SELECT point, SUM(out) AS sum_out
               FROM Outcome_o
              WHERE date < '2001-04-15 00:00:00.000'
              GROUP BY point) AS out
    ON inc.point = out.point
```

## 61
Посчитать остаток денежных средств на всех пунктах приема для базы данных с отчетностью не чаще одного раза в день.
```sql
SELECT ISNULL(SUM(inc), 0) - ISNULL(SUM(out), 0) AS Balance
  FROM (SELECT inc, out
          FROM Income_o
          FULL JOIN Outcome_o
            ON Income_o.point = Outcome_o.point
           AND Income_o.date = Outcome_o.date
        ) AS all_results
```

## 62
Посчитать остаток денежных средств на всех пунктах приема на начало дня 15/04/01 для базы данных с отчетностью не чаще одного раза в день.
```sql
SELECT SUM(inc) - SUM(out) AS coin_balance
  FROM (SELECT inc, out
          FROM Income_o
          FULL JOIN Outcome_o
            ON Income_o.point = Outcome_o.point
           AND Income_o.date = Outcome_o.date
         WHERE (Income_o.date < '2001-04-15'
            OR Income_o.date IS NULL)
           AND (Outcome_o.date < '2001-04-15'
            OR Outcome_o.date IS NULL)) AS all_results
```

## 63
Определить имена разных пассажиров, когда-либо летевших на одном и том же месте более одного раза.
```sql
SELECT name 
  FROM passenger
 WHERE ID_psg IN (SELECT ID_psg
                    FROM pass_in_trip
                   GROUP BY ID_psg, place
                  HAVING COUNT(place) >= 2)
```

## 64

## 65

## 66
Для всех дней в интервале с 01/04/2003 по 07/04/2003 определить число рейсов из Rostov с пассажирами на борту.  
Вывод: дата, количество рейсов.
```sql
SELECT dates, SUM(flight)
  FROM(
SELECT gen_date.dates, 
       CASE
       WHEN trip_no IS NOT NULL
       THEN 1
       ELSE 0
        END AS flight
  FROM (SELECT generate_series('2003-04-01 00:00:00'::timestamp, '2003-04-07 00:00:00', '1 day') AS dates) AS gen_date
  LEFT JOIN (SELECT pass_in_trip.date, pass_in_trip.trip_no
               FROM pass_in_trip
              INNER JOIN trip
                 ON pass_in_trip.trip_no = trip.trip_no
              WHERE trip.town_from = 'Rostov') AS sort_town
   ON gen_date.dates = sort_town.date
GROUP BY gen_date.dates, trip_no
             ) AS all_date
GROUP BY dates
ORDER BY dates
```

## 67
Найти количество маршрутов, которые обслуживаются наибольшим числом рейсов.  
Замечания.  
1) A - B и B - A считать РАЗНЫМИ маршрутами.  
2) Использовать только таблицу Trip
```sql
SELECT COUNT(*) AS qnt
FROM (SELECT COUNT(trip_no) AS cnt
        FROM trip
       GROUP BY town_from, town_to
      HAVING COUNT(trip_no) >= ALL(SELECT COUNT(trip_no) AS qnt_trip_no
                                     FROM trip
                                    GROUP BY town_to, town_from)
     ) AS cnt
```

## 68

## 69

## 70
Укажите сражения, в которых участвовало по меньшей мере три корабля одной и той же страны.
```sql
SELECT DISTINCT battle
  FROM (SELECT o.ship, o.battle, COALESCE(c.class, s.class) AS class
          FROM outcomes AS o
          LEFT JOIN ships AS s
            ON o.ship = s.name
          LEFT JOIN classes AS c
            ON o.ship = c.class) AS info_class
 INNER JOIN classes 
    ON info_class.class = classes.class
 GROUP BY battle, country
HAVING COUNT(country) >= 3
```

## 71
Найти тех производителей ПК, все модели ПК которых имеются в таблице PC.
```sql
SELECT Product.maker
  FROM Product
  LEFT JOIN PC
    ON Product.model=PC.model
 WHERE Product.type='PC'
 GROUP BY Product.maker
HAVING COUNT(PC.model)=COUNT(Product.model)
```

## 72


## 73
Для каждой страны определить сражения, в которых не участвовали корабли данной страны.  
Вывод: страна, сражение
```sql
SELECT country, name
  FROM classes
 CROSS JOIN battles

EXCEPT

SELECT country, battle
  FROM outcomes AS o
 INNER JOIN (SELECT class AS ship, country
               FROM classes
        
              UNION 

             SELECT name AS ship, country
               FROM ships
              INNER JOIN classes
                 ON ships.class = classes.class
             ) AS country_ship
    ON o.ship = country_ship.ship
```
## 74
Вывести все классы кораблей России (Russia). Если в базе данных нет классов кораблей России, вывести классы для всех имеющихся в БД стран.  
Вывод: страна, класс
```sql
SELECT country, class
  FROM classes
 WHERE country IN (SELECT CASE
                          WHEN 'Russia' = ANY(SELECT country
                                                FROM classes)
                          THEN 'Russia'
                          ELSE country
                           END
                     FROM classes AS cl2)
```

## 75
Для тех производителей, у которых есть продукты с известной ценой хотя бы в одной из таблиц Laptop, PC, Printer найти максимальные цены на каждый из типов продукции.  
Вывод: maker, максимальная цена на ноутбуки, максимальная цена на ПК, максимальная цена на принтеры.  
Для отсутствующих продуктов/цен использовать NULL.
```sql
SELECT pr.maker, MAX(l.price), MAX(pc.price), MAX(p.price)
  FROM product AS pr
  LEFT JOIN pc
    ON pr.model = pc.model
  LEFT JOIN laptop AS l
    ON pr.model = l.model
  LEFT JOIN printer AS p
    ON pr.model = p.model
 WHERE l.price IS NOT NULL OR pc.price IS NOT NULL OR p.price IS NOT NULL 
GROUP BY pr.maker
```

## 76

## 77
Определить дни, когда было выполнено максимальное число рейсов из  
Ростова ('Rostov'). Вывод: число рейсов, дата.
```sql
SELECT * 
  FROM (SELECT COUNT(t.trip_no) AS cnt, date
          FROM (SELECT DISTINCT date, trip_no
                  FROM pass_in_trip) AS t
         INNER JOIN trip
            ON trip.trip_no = t.trip_no
         WHERE town_from = 'Rostov'
         GROUP BY date
        ) AS t1
 WHERE t1.cnt = (SELECT MAX(c)
                   FROM (SELECT COUNT(t2.trip_no) AS c, date
                           FROM (SELECT DISTINCT date, trip_no
                                   FROM pass_in_trip
                                 ) AS t2
                          INNER JOIN trip AS trip1
                             ON trip1.trip_no = t2.trip_no
                          WHERE town_from = 'Rostov'
                          GROUP BY date
                         ) AS t3
                 )
```
## 78

## 79

## 80
Найти производителей любой компьютерной техники, у которых нет моделей ПК, не представленных в таблице PC.
```sql
SELECT maker
  FROM Product

EXCEPT

SELECT DISTINCT maker
  FROM Product
 WHERE type='PC' AND model NOT IN (SELECT model
                                     FROM PC)
```

## 81
Из таблицы Outcome получить все записи за тот месяц (месяцы), с учетом года, в котором суммарное значение расхода (out) было максимальным.
```sql
SELECT code, point, date, out
  FROM (SELECT *, SUBSTRING(CONVERT(VARCHAR(25), CONVERT(DATE, date)), 1, 7) AS yd
          FROM outcome
        ) AS t1
 
 INNER JOIN (SELECT SUBSTRING(CONVERT(VARCHAR(25), CONVERT(DATE, date)), 1, 7) AS yd
               FROM outcome
              GROUP BY SUBSTRING(CONVERT(VARCHAR(25), CONVERT(DATE, date)), 1, 7)
             HAVING SUM(out) = (SELECT TOP 1 SUM(out) AS out
                                  FROM outcome
                                 GROUP BY SUBSTRING(CONVERT(VARCHAR(25), CONVERT(DATE, date)), 1, 7)
                                 ORDER BY SUM(out) DESC
                               )
            ) AS t3
   ON t1.yd = t3.yd
```

## 82


## 83

## 84

## 85
Найти производителей, которые выпускают только принтеры или только PC.  
При этом искомые производители PC должны выпускать не менее 3 моделей.
```sql
SELECT maker
  FROM 
(SELECT COALESCE(p2.maker, p3.maker) AS maker
   FROM (SELECT maker, COUNT(model) AS model
           FROM product
          GROUP BY maker) AS p1
  LEFT JOIN (SELECT maker, COUNT(model) AS model
               FROM product
              WHERE type = 'Printer'
              GROUP BY maker) AS p2
    ON p1.maker = p2.maker
   AND p1.model = p2.model
  LEFT JOIN (SELECT maker, COUNT(model) AS model
               FROM product
              WHERE type = 'PC'
              GROUP BY maker
             HAVING COUNT(model) > 2) AS p3
    ON p1.maker = p3.maker
   AND p1.model = p3.model
) AS t
 WHERE maker IS NOT NULL
```

## 86

## 87

## 88

## 89
Найти производителей, у которых больше всего моделей в таблице Product, а также тех, у которых меньше всего моделей.  
Вывод: maker, число моделей
```sql
SELECT maker, COUNT(model)
  FROM Product
 GROUP BY maker
HAVING COUNT(model) IN (SELECT MAX(tab1.count_mod1)
                          FROM (SELECT COUNT(model) AS count_mod1
                                  FROM Product
                                 GROUP BY maker) AS tab1
                       )       
OR
       COUNT(model) IN (SELECT MIN(tab2.count_mod2)
                          FROM (SELECT COUNT(model) AS count_mod2
                                  FROM Product
                                 GROUP BY maker) AS tab2
                        )
```

## 90
Вывести все строки из таблицы Product, кроме трех строк с наименьшими номерами моделей и трех строк с наибольшими номерами моделей.
```sql
SELECT *
  FROM Product
 WHERE model NOT IN (SELECT TOP 3 model
                       FROM Product 
                      ORDER BY model DESC
                      
                      UNION

                     SELECT TOP 3 model
                       FROM Product 
                      ORDER BY model)
```

## 91

## 92
Выбрать все белые квадраты, которые окрашивались только из баллончиков, пустых к настоящему времени. Вывести имя квадрата 
```sql
SELECT Q_NAME
  FROM utQ
 INNER JOIN (SELECT B_Q_ID
               FROM utB
              WHERE B_V_ID IN (SELECT B_V_ID
                                 FROM utb
                                GROUP BY B_V_ID
                               HAVING SUM(B_VOL) = 255)
              GROUP BY B_Q_ID
             HAVING SUM(B_VOL) = 765
             ) AS t1
    ON utQ.Q_ID = t1.B_Q_ID
```

## 93
Для каждой компании, перевозившей пассажиров, подсчитать время, которое провели в полете самолеты с пассажирами.
Вывод: название компании, время в минутах. 
```sql
SELECT name, s_time
  FROM (SELECT ID_comp, SUM(time) AS s_time
          FROM (SELECT ID_comp,
                       CASE
                       WHEN DATEPART(hh, time_out) <= DATEPART(hh, time_in)
                       THEN DATEDIFF(mi, time_out, time_in)
                       ELSE DATEDIFF(mi, time_out, '1900-01-01 23:59:00') + 1 + DATEPART(hh, time_in) * 60 + DATEPART(mi, time_in)
                        END AS time
                 FROM trip
                INNER JOIN (SELECT trip_no
                              FROM pass_in_trip
                             GROUP BY trip_no, date) AS t
                   ON trip.trip_no = t.trip_no
              ) AS t1
        GROUP BY ID_comp
      ) AS t2
 INNER JOIN company AS c
    ON c.ID_comp = t2.ID_comp
```

## 94
Для семи последовательных дней, начиная от минимальной даты, когда из Ростова было совершено максимальное число рейсов, определить число рейсов из Ростова. Вывод: дата, количество рейсов 
##### рабтает в postgree
```sql
SELECT dates, COUNT(DISTINCT trip_no)
  FROM (SELECT generate_series(date, date + interval '6 day', '1 day') AS dates
          FROM (SELECT date, COUNT(DISTINCT trip_no) AS cnt
                  FROM pass_in_trip
                 WHERE trip_no IN (SELECT trip_no
                                     FROM trip
                                    WHERE town_from = 'Rostov')
                 GROUP BY date
                 ORDER BY cnt DESC, date ASC 
                 LIMIT 1
                ) AS t
       ) AS t1
  LEFT JOIN (SELECT date, pt.trip_no
               FROM pass_in_trip AS pt
              INNER JOIN trip
                 ON pt.trip_no = trip.trip_no
                AND town_from = 'Rostov'
             ) AS t2
    ON t1.dates = t2.date
 GROUP BY dates 
```
## 95
На основании информации из таблицы Pass_in_Trip, для каждой авиакомпании определить:
1) количество выполненных перелетов;
2) число использованных типов самолетов;
3) количество перевезенных различных пассажиров;
4) общее число перевезенных компанией пассажиров.
Вывод: Название компании, 1), 2), 3), 4).
```sql
SELECT name, cnt_all, cnt_plane, cnt_diff_psg, cnt_psg
  FROM company
 INNER JOIN (SELECT ID_comp, COUNT(pt.trip_no) AS cnt_all, COUNT(DISTINCT plane) AS cnt_plane, SUM(cnt) AS cnt_psg
               FROM trip
              INNER JOIN (SELECT trip_no, date, COUNT(ID_psg) AS cnt
                            FROM pass_in_trip
                           GROUP BY trip_no, date
                         ) AS pt
                 ON pt.trip_no = trip.trip_no
              GROUP BY ID_comp
             ) AS t1
     ON company.ID_comp = t1.ID_comp
 INNER JOIN (SELECT ID_comp, COUNT(DISTINCT ID_psg) AS cnt_diff_psg
               FROM trip
              INNER JOIN pass_in_trip AS pt
                 ON trip.trip_no = pt.trip_no
              GROUP BY ID_comp
            ) AS t2
   ON t1.ID_comp = t2.ID_comp
```

## 96
При условии, что баллончики с красной краской использовались более одного раза, выбрать из них такие, которыми окрашены квадраты, имеющие голубую компоненту.Вывести название баллончика 
```sql
SELECT V_NAME
  FROM utV
 INNER JOIN (SELECT B_V_ID
               FROM utV
              INNER JOIN utB
                 ON utB.B_V_ID = utV.V_ID
                AND utV.V_COLOR = 'R'
              GROUP BY B_V_ID
             HAVING COUNT(B_V_ID) > 1) AS redbal
    ON utV.V_ID = redbal.B_V_ID
   AND B_V_ID IN (SELECT DISTINCT B_V_ID
                    FROM utB
                   INNER JOIN (SELECT B_Q_ID
                                 FROM utB
                                INNER JOIN utV
                                   ON utB.B_V_ID = utV.V_ID
                                  AND (V_COLOR = 'R' OR V_COLOR = 'B')
                                GROUP BY B_Q_ID
                               HAVING MAX(V_COLOR) <> MIN(V_COLOR)) AS t1
                      ON utB.B_Q_ID = t1.B_Q_ID
                   )
```
