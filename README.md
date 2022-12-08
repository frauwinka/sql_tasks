# sql_tasks
## ДЗ 1
## ДЗ 2
### №1
>Попробуйте ввести в таблицу ```aircrafts``` строку с таким значением атрибута «Код самолета» (```aircraft_code```), которое вы уже вводили

Можно заметить, что в таблице ```aircrafts``` атрибут ```aircraft_code``` является ```PRIMARY_KEY```, а мы знаем, что при реализации ключа таблицы создается индекс, главное свойтсво которого *уникальность*.

```
demo=# insert into aircrafts (aircraft_code, model, range)
       values ('SU9', 'Sukhoi SuperJet-100', 3000);
ОШИБКА:  повторяющееся значение ключа нарушает ограничение уникальности "aircrafts_pkey"
ПОДРОБНОСТИ:  Ключ "(aircraft_code)=(SU9)" уже существует.
```
### №2
>Предложение ```ORDER BY``` команды ```SELECT``` позволяет отсортировать данные при выводе. По умолчанию сортировка выполняется по возрастанию значений атрибута, указанного в этом предложении. Но можно упорядочить строки и по убыванию значения атрибута. Для этого нужно после имени атрибута в предложении ```ORDER BY``` добавить ключевое слово ```DESC``` *(это сокращение от слова descendant — убывающий порядок)*. Самостоятельно напишите команду для выборки всех строк из таблицы ```aircrafts```, чтобы строки были упорядочены по убыванию значения атрибута «Максимальная дальность полета, км» (```range```).

```
SELECT * FROM aircrafts ORDER BY range DESC; 
```
Вывод:
```
 aircraft_code |        model        | range 
---------------+---------------------+-------
 773           | Boeing 777-300      | 11100
 763           | Boeing 767-300      |  7900
 319           | Airbus A319-100     |  6700
 320           | Airbus A320-200     |  5700
 321           | Airbus A321-200     |  5600
 733           | Boeing 737-300      |  4200
 SU9           | Sukhoi SuperJet-100 |  3000
 CR2           | Bombardier CRJ-200  |  2700
 CN1           | Cessna 208 Caravan  |  1200
```
### №3
>Команда ```UPDATE``` позволяет в процессе обновления выполнять арифметические действия над значениями, находящимися в строках таблицы. Представим себе, что двигатели самолета *Sukhoi SuperJet* стали в два раза экономичнее, вследствие чего дальность полета этого лайнера возросла ровно в два раза. Команда ```UPDATE``` позволяет увеличить значение атрибута ```range``` в строке, хранящей информацию об этом самолете, даже не выполняя предварительно выборку с целью выяснения текущего значения этого атрибута. При присваивании нового значения атрибуту ```range``` можно справа от знака ```«=»``` написать не только числовую константу, но и целое выражение. В нашем случае оно будет простым: ```range = range * 2```. Самостоятельно напишите команду ```UPDATE``` полностью, при этом не забудьте, что увеличить дальность полета нужно только у одной модели — *Sukhoi SuperJet*, поэтому необходимо использовать условие ```WHERE```. Затем с помощью команды ```SELECT``` проверьте полученный результат.

```
UPDATE aircrafts SET range = range * 2 WHERE model = ’Sukhoi SuperJet-100’;
SELECT * FROM aircrafts ORDER BY range DESC;
```
```
 aircraft_code |        model        | range 
---------------+---------------------+-------
 773           | Boeing 777-300      | 11100
 763           | Boeing 767-300      |  7900
 319           | Airbus A319-100     |  6700
 SU9           | Sukhoi SuperJet-100 |  6000
 320           | Airbus A320-200     |  5700
 321           | Airbus A321-200     |  5600
 733           | Boeing 737-300      |  4200
 CR2           | Bombardier CRJ-200  |  2700
 CN1           | Cessna 208 Caravan  |  1200
```
### №4
>Если в предложении ```WHERE``` команды ```DELETE``` вы укажете логически и синтаксически корректное условие, но строк, удовлетворяющих этому условию, в таблице не окажется, то в ответ СУБД выведет сообщение ```DELETE 0``` Такая ситуация не является ошибкой или сбоем в работе СУБД. Например, если после удаления какой-то строки вы повторно попытаетесь удалить ее же, то получите именно такое сообщение. Самостоятельно смоделируйте описанную ситуацию, подобрав условие, которому гарантированно не соответствует ни одна строка в таблице «Самолеты» (```aircrafts```).

```
INPUT : DELETE FROM aircrafts WHERE range > 100000000;

OUTPUT : DELETE 0

SELECT * FROM aircrafts ORDER BY range DESC;
```
Возвращает
```
 aircraft_code |        model        | range 
---------------+---------------------+-------
 773           | Boeing 777-300      | 11100
 763           | Boeing 767-300      |  7900
 319           | Airbus A319-100     |  6700
 SU9           | Sukhoi SuperJet-100 |  6000
 320           | Airbus A320-200     |  5700
 321           | Airbus A321-200     |  5600
 733           | Boeing 737-300      |  4200
 CR2           | Bombardier CRJ-200  |  2700
 CN1           | Cessna 208 Caravan  |  1200
```
Т.е. такую же таблицу без изменений.
## ДЗ 4
### №2
>Посмотрите, какие ограничения уже наложены на атрибуты таблицы «Успеваемость» (progress). Воспользуйтесь командой \d утилиты psql. А теперь предложите для этой таблицы ограничение уровня таблицы.
```
edu=# \d progress
                   Table "public.progress"
   Column    |     Type     | Collation | Nullable | Default 
-------------+--------------+-----------+----------+---------
 record_book | numeric(5,0) |           | not null | 
 subject     | text         |           | not null | 
 acad_year   | text         |           | not null | 
 term        | numeric(1,0) |           | not null | 
 mark        | numeric(1,0) |           | not null | 5
Check constraints:
    "progress_mark_check" CHECK (mark >= 3::numeric AND mark <= 5::numeric)
    "progress_term_check" CHECK (term = 1::numeric OR term = 2::numeric)
Foreign-key constraints:
    "progress_record_book_fkey" FOREIGN KEY (record_book) REFERENCES students(record_book) ON UPDATE CASCADE ON DELETE CASCADE
```
Предлагаемые ограничения:
```
ALTER TABLE progress
ADD CHECK (
( test_form = 'экзамен' AND mark IN ( 3, 4, 5 ) )
OR
( test_form = 'зачет' AND mark IN ( 0, 1 ) )
);
```
Два запроса:
```
edu=# INSERT INTO progress VALUES (1, 'course', 2022, 2, 1, 'экзамен');
ERROR:  new row for relation "progress" violates check constraint "progress_check"
DETAIL:  Failing row contains (1, course, 2022, 2, 1, экзамен).
edu=# INSERT INTO progress VALUES (1, 'course', 2022, 2, 5, 'экзамен');
INSERT 0 1
```
В таблице уже было ограничение на допустимые значения атрибута mark. Как вы думаете, не будет ли оно конфликтовать с новым ограничением? Проверьте эту гипотезу. Если ограничения конфликтуют, тогда удалите старое ограничение и снова попробуйте добавить строки в таблицу.

Да, колфликты будут происходить при запросе
```
edu=# INSERT INTO progress VALUES (1, 'course', 2022, 1, 1, 'зачет');
ERROR:  new row for relation "progress" violates check constraint "progress_mark_check"
DETAIL:  Failing row contains (1, course, 2022, 1, 1, зачет).
```
Т.к в нашем ограничении оценки за зачет - 1 и 0, а в ограничении на ```marks``` они 3, 4, 5.

Удалим ограничение:
```
edu=# 
    ALTER TABLE progress
        DROP CONSTRAINT progress_mark_check;
```
Теперь работает:

```
edu=# INSERT INTO progress VALUES (1, 'course', 2022, 1, 1, 'зачет');
INSERT 0 1
```
Подумайте, какое еще ограничение уровня таблицы можно предложить для этой таблицы?

Можно изменить ограничения на оценки на 100 бальную или 10 бальную систему, можно выставить ограничения на названия курсов, на академический год и т.д

### №9
>В разделе 9.4 документации «Строковые функции и операторы» есть функция trim. Попробуйте воспользоваться ею. Если вы еще не изучили команду ```ALTER TABLE```, то удалите таблицу ```students``` и создайте ее заново с учетом нового ограничения, а если уже познакомились с ней, то сделайте так: ```ALTER TABLE students ADD CHECK (...);```

Добавим верное органичение
```
edu=# ALTER TABLE students ADD CHECK (trim(name) <> '');
ALTER TABLE
```
Проверим работу ограничения с помощью команд:

```
edu=# INSERT INTO students VALUES (1, '', 1, 1);
ERROR:  new row for relation "students" violates check constraint "students_name_check"
DETAIL:  Failing row contains (1, , 1, 1).
edu=# INSERT INTO students VALUES (1, ' ', 1, 1);
ERROR:  new row for relation "students" violates check constraint "students_name_check"
DETAIL:  Failing row contains (1,  , 1, 1).
edu=# INSERT INTO students VALUES (1, '  ', 1, 1);
ERROR:  new row for relation "students" violates check constraint "students_name_check"
DETAIL:  Failing row contains (1,   , 1, 1).
edu=# INSERT INTO students VALUES (1, 'Artyom2', 1, 1);
ERROR:  duplicate key value violates unique constraint "students_pkey"
DETAIL:  Key (record_book)=(1) already exists.
edu=# INSERT INTO students VALUES (2, 'Artyom2', 1, 1);
INSERT 0 1
```
Есть ли подобные слабые места в таблице «Успеваемость» (progress)?

В таблице progress у столбцов subject, acad_year имеется такое же слабое место.

### №17
>Подумайте, какие представления было бы целесообразно создать для нашей базы данных «Авиаперевозки». Необходимо учесть наличие различных групп пользователей, например: пилоты, диспетчеры, пассажиры, кассиры. Создайте представления и проверьте их в работе.

Для примера, можно создать представление, в котором каждому аэропорту будет предоставляться его временной пояс:

```
CREATE VIEW airports_timezone AS
SELECT airport_name, city, timezone FROM bookings.airports;
SELECT * FROM airports_timezone;
CREATE VIEW
```
```
     airport_name     |           city           |      timezone      
----------------------+--------------------------+--------------------
 Мирный               | Мирный                   | Asia/Yakutsk
 Бегишево             | Нижнекамск               | Europe/Moscow
 Спиченково           | Новокузнецк              | Asia/Novokuznetsk
 Нальчик              | Нальчик                  | Europe/Moscow
 Беслан               | Владикавказ              | Europe/Moscow
 Чебоксары            | Чебоксары                | Europe/Moscow
 Надым                | Надым                    | Asia/Yekaterinburg
 Нягань               | Нягань                   | Asia/Yekaterinburg
 Курск-Восточный      | Курск                    | Europe/Moscow
 Саранск              | Саранск                  | Europe/Moscow
 Донское              | Тамбов                   | Europe/Moscow
 Советский            | Советский                | Asia/Yekaterinburg
 Ижевск               | Ижевск                   | Europe/Samara
 Салехард             | Салехард                 | Asia/Yekaterinburg
 Ханты-Мансийск       | Ханты-Мансийск           | Asia/Yekaterinburg
 Горно-Алтайск        | Горно-Алтайск            | Asia/Krasnoyarsk
 ... skipped for shorter look
```
Или создать представления со всеми названиями портов в одном городе:
```
CREATE VIEW msk_ports AS
SELECT * FROM.airports
WHERE city = 'Москва';
CREATE VIEW
demo=# SELECT * FROM msk_ports;
 airport_code | airport_name |  city  | longitude | latitude  |   timezone    
--------------+--------------+--------+-----------+-----------+---------------
 SVO          | Шереметьево  | Москва | 37.414589 | 55.972642 | Europe/Moscow
 VKO          | Внуково      | Москва | 37.261486 | 55.591531 | Europe/Moscow
 DME          | Домодедово   | Москва | 37.906111 | 55.408611 | Europe/Moscow
```
### №18
>Подумайте, какие еще таблицы было бы целесообразно дополнить столбцами типа ```json/jsonb```. Вспомните, что, например, в таблице «Билеты» (```tickets```) уже есть столбец такого типа — ```contact_data```. Выполните модификации таблиц и измените в них одну-две строки для проверки правильности ваших решений.

Можно добавить такое поле в таблицу аэрпортов, чтобы хранить данные о команде аэропорта, например.

Добавляем поле:

```
ALTER TABLE airports ADD COLUMN crew jsonb;
```
Вставляем значение:
```
 UPDATE airports SET crew = '{"pilots": 3, "dispetchers": 5, "type": {"gov": "2", "pers": "1"}}'::jsonb WHERE city = 'Москва';
```
Проверка:
```
SELECT * FROM bookings.airports WHERE city = 'Москва';
 airport_code | airport_name |  city  | longitude | latitude  |   timezone    |                                crew                                
--------------+--------------+--------+-----------+-----------+---------------+--------------------------------------------------------------------
 SVO          | Шереметьево  | Москва | 37.414589 | 55.972642 | Europe/Moscow | {"type": {"gov": "2", "pers": "1"}, "pilots": 3, "dispetchers": 5}
 VKO          | Внуково      | Москва | 37.261486 | 55.591531 | Europe/Moscow | {"type": {"gov": "2", "pers": "1"}, "pilots": 3, "dispetchers": 5}
 DME          | Домодедово   | Москва | 37.906111 | 55.408611 | Europe/Moscow | {"type": {"gov": "2", "pers": "1"}, "pilots": 3, "dispetchers": 5}
```
## ДЗ 5
### №2
>Предложите шаблон поиска в операторе LIKE для выбора из этой таблицы всех пассажиров с фамилиями, состоящими из пяти букв.
```
SELECT passenger_name FROM tickets WHERE passenger_name LIKE '% _____';
```
### №7
> К сожалению, в этой выборке информация дублируется. Пары городов приведены по два раза: для рейса «туда» и для рейса «обратно». Модифицируйте запрос таким образом, чтобы каждая пара городов была выведена только один раз:
```
INPUT:

SELECT DISTINCT least(departure_city, arrival_city), greatest(departure_city, arrival_city),
FROM routes r
JOIN aircrafts a ON r.aircraft_code = a.aircraft_code
WHERE a.model = 'Boeing 777-300'
ORDER BY 1;
```
### №9
>А с помощью какого запроса можно получить результат в таком виде?
```
departure_city | arrival_city    | count
---------------+-----------------+-------
Москва         | Санкт-Петербург | 12
```
С помощью
```
SELECT departure_city, arrival_city, count( * )
FROM routes
WHERE departure_city = 'Москва'
AND arrival_city = 'Санкт-Петербург';
```
### №13
>А как выявить те направления, на которые не было продано ни одного билета? Один из вариантов решения такой: если на рейсы, отправляющиеся по какомуто направлению, не было продано ни одного билета, то максимальная и минимальная цены будут равны NULL. Нужно получить выборку в таком виде:
```
departure_city | arrival_city | max | min
---------------------+---------------------+-----------+----------
Абакан | Архангельск | |
Абакан | Грозный | |
Абакан | Кызыл | |
Абакан | Москва | 101000.00 | 33700.00
Абакан | Новосибирск | 5800.00 | 5800.00
```
```
INPUT:

SELECT f.departure_city, f.arrival_city, max( tf.amount ), min( tf.amount )
  FROM flights_v f
  LEFT JOIN ticket_flights tf ON f.flight_id = tf.flight_id
  GROUP BY 1, 2
  ORDER BY 1, 2;
```
### №19
>В разделе 6.4 мы использовали рекурсивный алгоритм в общем табличном выражении. Изучите этот пример, чтобы лучше понять работу рекурсивного алгоритма:
```
WITH RECURSIVE ranges ( min_sum, max_sum )
AS (
    VALUES( 0, 100000 ),
        ( 100000, 200000 ),
        ( 200000, 300000 )
    UNION ALL
    SELECT min_sum + 100000, max_sum + 100000
    FROM ranges
    WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
)
SELECT * FROM ranges;
```
>Задание 1. Модифицируйте запрос, добавив в него столбец ```level``` (можно назвать его и ```iteration```). Этот столбец должен содержать номер текущей итерации, поэтому нужно увеличивать его значение на единицу на каждом шаге. Не забудьте задать начальное значение для добавленного столбца в предложении ```VALUES```. 
>Задание 2. Для завершения экспериментов замените UNION ALL на UNION и выполните запрос. Сравните этот результат с предыдущим, когда мы использовали ```UNION ALL```.

Модификация :
```
WITH RECURSIVE ranges ( min_sum, max_sum )
AS (
    VALUES( 0, 100000 ),
        ( 100000, 200000 ),
        ( 200000, 300000 )
    UNION ALL
    SELECT level + 1, min_sum + 100000, max_sum + 100000
    FROM ranges
    WHERE max_sum < ( SELECT max( total_amount ) FROM bookings )
)
SELECT * FROM ranges;
```
Для данных запросов ```UNION ALL``` на каждой итерации добавляла по 3 строки, а ```UNINON``` ровно по 1.

Вывод соотвественно: 
```
36 rows returned, 13 rows returned. 
```
Вывод таблиц опущен в связи с объемом

## ДЗ 6
### №1
>Добавьте в определение таблицы ```aircrafts_log``` значение по умолчанию ```current_timestamp``` и соответствующим образом измените команды ```INSERT```, приведенные в тексте главы.
```
CREATE TEMP TABLE aircrafts_log AS SELECT * FROM aircrafts WITH NO DATA;
```
```
ALTER TABLE aircrafts_log
ADD COLUMN when_add timestamp;
ALTER TABLE aircrafts_log
ALTER COLUMN when_add SET DEFAULT current_timestamp;
WITH add_row AS
    ( 
        INSERT INTO aircrafts_tmp
        VALUES ( 'TEST', 'TEST-3000', 3000 )
        ON CONFLICT DO NOTHING
        RETURNING *
    )
INSERT INTO aircrafts_log(aircraft_code, model, range, operation)
SELECT add_row.aircraft_code, add_row.model, add_row.range, 'INSERT'
FROM add_row;
```
### №2
>В предложении ```RETURNING``` можно указывать не только символ ```«∗»```, означающий выбор всех столбцов таблицы, но и более сложные выражения, сформированные на основе этих столбцов. В тексте главы мы копировали содержимое таблицы «Самолеты» в таблицу ```aircrafts_tmp```, используя в предложении ```RETURNING``` именно ```«∗»```. Однако возможен и другой вариант запроса:

```
WITH add_row AS
    ( INSERT INTO aircrafts_tmp
        SELECT * FROM aircrafts
        RETURNING aircraft_code, model, range,
        current_timestamp, 'INSERT'
    )
    INSERT INTO aircrafts_log
SELECT ? FROM add_row;
```
>Что нужно написать в этом запросе вместо вопросительного знака?

Можем честно указать все необходимые поля, которые нужно выписать, а можем просто выставить ```*```, так как в ```RETURNING``` мы уже указали необходимые поля.

### №4
>В тексте главы в предложениях ```ON CONFLICT``` команды ```INSERT``` мы использовали только выражения, состоящие из имени одного столбца. Однако в таблице «Места» (```seats```) первичный ключ является составным и включает два столбца. Напишите команду ```INSERT``` для вставки новой строки в эту таблицу и предусмотрите возможный конфликт добавляемой строки со строкой, уже имеющейся в таблице. Сделайте два варианта предложения ```ON CONFLICT```: первый — с использованием перечисления имен столбцов для проверки наличия дублирования, второй — с использованием предложения ```ON CONSTRAINT```. Для того чтобы не изменить содержимое таблицы «Места», создайте ее копию и выполняйте все эти эксперименты с таблицей-копией.
```
FIRST:

INSERT INTO seats2 VALUES (733, '2D', 'Business')
ON CONFLICT (aircraft_code, seat_no) DO UPDATE SET
fare_conditions=excluded.fare_conditions RETURNING 0;
```
```
SECOND:

INSERT INTO seats2 VALUES (733, '2D', 'Economy')
ON CONFLICT ON CONSTRAINT seats_pkey
DO UPDATE SET fare_conditions=excluded.fare_conditions RETURNING *;
```
## ДЗ 7
### №1
>Предположим, что для какой-то таблицы создан уникальный индекс по двум столбцам: ```column1``` и ```column2```. В таблице есть строка, у которой значение атрибута ```column1``` равно ```ABC```, а значение атрибута ```column2``` — ```NULL```. Мы решили добавить в таблицу еще одну строку с такими же значениями ключевых атрибутов, т. е. ```column1``` — ```ABC```, а ```column2``` — ```NULL```. Как вы думаете, будет ли операция вставки новой строки успешной или завершится с ошибкой? Объясните ваше решение

Предлагаю проверить экспериментально: Создаем таблицу 
```
CREATE TABLE TEST (c1 text, c2 text);
'''
Вносим значение 
```
INSERT INTO TEST (c1) VALUES ('ABC'); 
```
Добавляем индекс 
```
CREATE UNIQUE INDEX unique_rest ON TEST (c1, c2);
```
```
              Table "public.test"
 Column | Type | Collation | Nullable | Default 
--------+------+-----------+----------+---------
 c1     | text |           |          | 
 c2     | text |           |          | 
Indexes:
    "unique_rest" UNIQUE, btree (c1, c2)
```
Попробуем вставить второ значение: 
```
INSERT INTO TEST (c1) VALUES ('ABC');
```
```
SELECT * FROM TEST;
 c1  | c2 
-----+----
 ABC | 
 ABC | 
(2 rows)
```
Вероятно, ```NULL != NULL```, и проверки проходят успешно и значения вставляются.

### №3
Создайте индекс по столбцу ```fare_conditions```. Конечно, в реальной ситуации такой индекс вряд ли целесообразен, но нам он нужен для экспериментов. Проделайте те же эксперименты с таблицей ```ticket_flights```. Будет ли различаться среднее время выполнения запросов для различных значений атрибута ```fare_conditions```? Почему это имеет место?

Для начала запустим запросы без индекса и выпишем время:

Для
```
SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Comfort';
```
БЫло получено: ```Time: 51.552 ms, Time: 50.557 ms, Time: 48.756 ms```

Для :
```
SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Business';
```
Было получено: Time: ```48.756 ms, Time: 46.867 ms, Time: 45.864 ms```

Для:
```
SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Economy';
```
Было получено: ```Time: 53.440 ms, Time: 61.099 ms, Time: 61.233 ms```

Теперь добавим индекс: 
```
CREATE INDEX index ON ticket_flights (fare_conditions)
```
И выполним те же эксперименты с таблицей:

Для
```
SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Comfort';
```
Было получено: ```Time: 2.853 ms, Time: 2.100 ms, Time: 1.936 ms```

Для :
```
SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Business';
```
Было получено: ```Time: 14.124 ms, Time: 8.672 ms, Time: 8.303 ms```

Для:
```
SELECT count( * )
FROM ticket_flights
WHERE fare_conditions = 'Economy';
```
Было получено: ```Time: 29.632 ms, Time: 29.416 ms, Time: 29.428 ms```

Сделаем таблицу сравнения результатов:

Без индексов:
```
              | EXP1 | EXP2 | EXP3 | AVG |
______________|______|______|______|_____|
'Economy'     |53.440|61.099|61.233|58.59|
______________|___________________________
'Comfort'     |51.552|50.557|48.756|50.26|
______________|___________________________
'Business'    |48.756|46.867|45.864|47.16|     
```
C индексами:
```
              | EXP1 | EXP2 | EXP3 | AVG |
______________|______|______|______|_____|
'Economy'     |53.440|61.099|61.233|58.59|
______________|___________________________
'Comfort'     | 2.853| 2.100| 1.936| 2.29|
______________|___________________________
'Business'    |14.124| 8.672| 8.303|10.37|   
```
Очевидно, что с индексами скорость повышается.

## ДЗ 8
### №3 
>Можно ли говорить, что в такой ситуации имеет место потерянное обновление? Если оно имеет место, то что можно предпринять для его недопущения?

Здесь есть потерянное обновление:
```
BEGIN ISOLATION LEVEL READ COMMITTED;
UPDATE aircrafts_tmp
SET range = 6000
WHERE aircraft_code = 'CR2';

BEGIN ISOLATION LEVEL READ COMMITTED;
WARNING: there is already transaction in progress;
BEGIN
UPDATE aircrafts_tmp
SET range = 2000
WHERE aircraft_code = 'CR2';
UPDATE 1;

COMMIT;
COMMIT

COMMIT;
COMMIT

SELECT * FROM aircrafts_tmp WHERE aircraft_code = 'CR2';
aircraft_code | model               | range
--------------+---------------------+--------
 CR2          | Bombardier CRJ-200  | 2000
```
Можно выбрать другой уровень изоляции:
```
BEGIN ISOLATION LEVEL READ COMMITTED;
UPDATE aircrafts_tmp
SET range = range + 6000
WHERE aircraft_code = 'CR2';

BEGIN ISOLATION LEVEL READ COMMITTED;
WARNING: there is already transaction in progress;
BEGIN
UPDATE aircrafts_tmp
SET range = range + 2000
WHERE aircraft_code = 'CR2';
UPDATE 1;

COMMIT;
COMMIT

COMMIT;
COMMIT

SELECT * FROM aircrafts_tmp WHERE aircraft_code = 'CR2';
aircraft_code | model               | range
--------------+---------------------+--------
 CR2          | Bombardier CRJ-200  | 11250
```
## ДЗ 9
### №3
>Самостоятельно выполните команду ```EXPLAIN``` для запроса, содержащего общее табличное выражение (```CTE```). Посмотрите, на каком уровне находится узел плана, отвечающий за это выражение, как он оформляется. Учтите, что общие табличные выражения всегда материализуются, т. е. вычисляются однократно и результат их вычисления сохраняется в памяти, а затем все последующие обращения в рамках запроса направляются уже к этому материализованному результату.
```
EXPLAIN SELECT * FROM aircrafts_tmp;
                            QUERY PLAN                            
------------------------------------------------------------------
 Seq Scan on aircrafts_tmp  (cost=0.00..20.20 rows=1020 width=52)
(1 row)
EXPLAIN with add_row AS
( INSERT INTO aircrafts_tmp
SELECT * FROM aircrafts
RETURNING *
)
INSERT INTO aircrafts_log
SELECT add_row.aircraft_code, add_row.model, add_row.range,
current_timestamp, 'INSERT'
FROM add_row;
                               QUERY PLAN                               
------------------------------------------------------------------------
 Insert on aircrafts_log  (cost=1.09..1.31 rows=0 width=0)
   CTE add_row
     ->  Insert on aircrafts_tmp  (cost=0.00..1.09 rows=9 width=52)
           ->  Seq Scan on aircrafts  (cost=0.00..1.09 rows=9 width=52)
   ->  CTE Scan on add_row  (cost=0.00..0.22 rows=9 width=92)
(5 rows)
```
### №8
>Исследуйте планы выполнения обоих запросов. Попытайтесь найти объяснение различиям в эффективности их выполнения. Чтобы получить усредненную картину, выполните каждый запрос несколько раз. Поскольку таблицы, участвующие в запросах, небольшие, то различие по абсолютным затратам времени выполнения будет незначительным. Но если бы число строк в таблицах было большим, то экономия ресурсов сервера могла оказаться заметной

Первый запрос:
```
EXPLAIN ANALYZE
SELECT a.aircraft_code AS a_code,
a.model,
( SELECT count( r.aircraft_code )
FROM routes r
WHERE r.aircraft_code = a.aircraft_code
) AS num_routes
FROM aircrafts a
GROUP BY 1, 2
ORDER BY 3 DESC;
                                                       QUERY PLAN                                                        
-------------------------------------------------------------------------------------------------------------------------
 Sort  (cost=236.31..236.34 rows=9 width=56) (actual time=16.300..16.443 rows=9 loops=1)
   Sort Key: ((SubPlan 1)) DESC
   Sort Method: quicksort  Memory: 25kB
   ->  HashAggregate  (cost=1.11..236.17 rows=9 width=56) (actual time=4.043..15.689 rows=9 loops=1)
         Group Key: a.aircraft_code
         Batches: 1  Memory Usage: 24kB
         ->  Seq Scan on aircrafts a  (cost=0.00..1.09 rows=9 width=48) (actual time=0.011..0.095 rows=9 loops=1)
         SubPlan 1
           ->  Aggregate  (cost=26.10..26.11 rows=1 width=8) (actual time=1.677..1.688 rows=1 loops=9)
                 ->  Seq Scan on routes r  (cost=0.00..25.88 rows=89 width=4) (actual time=0.033..0.921 rows=79 loops=9)
                       Filter: (aircraft_code = a.aircraft_code)
                       Rows Removed by Filter: 631
 Planning Time: 1.724 ms
 Execution Time: 16.785 ms
(14 rows)
```
Второй запрос:
```
EXPLAIN ANALYZE
SELECT a.aircraft_code AS a_code,
a.model,

count( r.aircraft_code ) AS num_routes
FROM aircrafts a

LEFT OUTER JOIN routes r

ON r.aircraft_code = a.aircraft_code
GROUP BY 1, 2

ORDER BY 3 DESC;
                                                          QUERY PLAN                                                          
------------------------------------------------------------------------------------------------------------------------------
 Sort  (cost=31.83..31.85 rows=9 width=56) (actual time=29.264..29.504 rows=9 loops=1)
   Sort Key: (count(r.aircraft_code)) DESC
   Sort Method: quicksort  Memory: 25kB
   ->  HashAggregate  (cost=31.60..31.69 rows=9 width=56) (actual time=29.077..29.231 rows=9 loops=1)
         Group Key: a.aircraft_code
         Batches: 1  Memory Usage: 24kB
         ->  Hash Right Join  (cost=1.20..28.05 rows=710 width=52) (actual time=1.409..21.822 rows=711 loops=1)
               Hash Cond: (r.aircraft_code = a.aircraft_code)
               ->  Seq Scan on routes r  (cost=0.00..24.10 rows=710 width=4) (actual time=0.008..6.572 rows=710 loops=1)
               ->  Hash  (cost=1.09..1.09 rows=9 width=48) (actual time=0.271..0.296 rows=9 loops=1)
                     Buckets: 1024  Batches: 1  Memory Usage: 9kB
                     ->  Seq Scan on aircrafts a  (cost=0.00..1.09 rows=9 width=48) (actual time=0.019..0.107 rows=9 loops=1)
 Planning Time: 0.336 ms
 Execution Time: 29.694 ms
(14 rows)
```
>Предложите аналогичную пару запросов к базе данных «Авиаперевозки». Проведите необходимые эксперименты с вашими запросами.

Запросы:
```
SELECT a.city AS city,
a.timezone,
(SELECT count(r.arrival_city) FROM routes r WHERE r.arrival_city = a.city) AS count
FROM airports a
WHERE a.timezone = 'Europe/Samara';
   city    |   timezone    | count 
-----------+---------------+-------
 Ижевск    | Europe/Samara |     1
 Астрахань | Europe/Samara |     4
 Самара    | Europe/Samara |     3
 Ульяновск | Europe/Samara |    11
 Ульяновск | Europe/Samara |    11
(5 rows)
```
Второй запрос :
```
SELECT a.city AS city,
a.timezone, count (r.arrival_city) as ar
FROM airports a
LEFT OUTER JOIN routes r
ON r.arrival_city = a.city
GROUP BY 1, 2;

RETURNED (101 rows)
```Аналитика:
```
1:

EXPLAIN ANALYZE SELECT a.city AS city,
a.timezone,
(SELECT count(r.arrival_city) FROM routes r WHERE r.arrival_city = a.city) AS count
FROM airports a
WHERE a.timezone = 'Europe/Samara';
                                                    QUERY PLAN                                                    
------------------------------------------------------------------------------------------------------------------
 Seq Scan on airports a  (cost=0.00..132.81 rows=5 width=40) (actual time=0.368..1.631 rows=5 loops=1)
   Filter: (timezone = 'Europe/Samara'::text)
   Rows Removed by Filter: 99
   SubPlan 1
     ->  Aggregate  (cost=25.89..25.90 rows=1 width=8) (actual time=0.273..0.286 rows=1 loops=5)
           ->  Seq Scan on routes r  (cost=0.00..25.88 rows=7 width=17) (actual time=0.020..0.189 rows=6 loops=5)
                 Filter: (arrival_city = a.city)
                 Rows Removed by Filter: 704
 Planning Time: 0.089 ms
 Execution Time: 1.731 ms
(10 rows)
2:

EXPLAIN ANALYZE SELECT a.city AS city,
a.timezone, count (r.arrival_city) as ar
FROM airports a
LEFT OUTER JOIN routes r
ON r.arrival_city = a.city
GROUP BY 1, 2;
                                                        QUERY PLAN                                                         
---------------------------------------------------------------------------------------------------------------------------
 HashAggregate  (cost=50.89..51.90 rows=101 width=40) (actual time=35.871..36.915 rows=101 loops=1)
   Group Key: a.city, a.timezone
   Batches: 1  Memory Usage: 32kB
   ->  Hash Right Join  (cost=4.34..43.17 rows=1029 width=49) (actual time=2.195..25.475 rows=1029 loops=1)
         Hash Cond: (r.arrival_city = a.city)
         ->  Seq Scan on routes r  (cost=0.00..24.10 rows=710 width=17) (actual time=0.015..6.724 rows=710 loops=1)
         ->  Hash  (cost=3.04..3.04 rows=104 width=32) (actual time=2.133..2.157 rows=104 loops=1)
               Buckets: 1024  Batches: 1  Memory Usage: 15kB
               ->  Seq Scan on airports a  (cost=0.00..3.04 rows=104 width=32) (actual time=0.022..1.154 rows=104 loops=1)
 Planning Time: 0.428 ms
 Execution Time: 37.777 ms
(11 rows)```
