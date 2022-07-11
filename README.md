# Задание 15

При импорте базы возникали ошибки превышения максимального размера загружаемого файла и превышения времени выполнения скрипта.

## Превышение времени выполнения скрипта
Проблема решается редактированием параметра max_execution_time в файле конфигурации php.

## Превышение размера загружаемого файла
Проблема комплексная.
- на уровне PHP необходимо отредактировать параметры post_max_size и upload_max_filesize.
- на уровне сервера необходимо добавить или отредактировать параметр client_max_body_size в конфиге Nginx.
В данном частном случае проблему можно решить, запаковав файл перед загрузкой. В этом случае его размер уменьшается до 30 Мбайт и он проходит в стандартные лимиты.

## Выбрать всех сотрудников с серией паспорта 1015 и номером 422269
В базе перепутаны столбцы series и number. Запросы составлены с учётом фактического расположения данных в столбцах.
### Текст запроса
SELECT * FROM `workers` WHERE `number` = 1015 AND `series` = 422269;
### Время выполнения в исходной базе
0,8533 сек.
### Время выполнения в индексированной базе
Создан индекс по полю number (9056 уникальных элементов):
CREATE INDEX number_index ON workers(number); (Запрос занял 2,8059 сек.)
Запрос на поиск занял 0,0022 с

Создан индекс по полю series (597281 уникальных элементов):
CREATE INDEX series_index ON workers(series); (Запрос занял 2,7657 сек.) 
Запрос занял 0,0007 сек.

При наличии обоих индексов запрос на поиск занял 0,0017 с.
## Найти всех сотрудников с именем Алексей c датой рождения после 1 июля 1999 года
### Текст запроса
SELECT * FROM `workers` WHERE first_name = 'Алексей' AND `birthday` > '1999-07-07'; 
### Время выполнения в исходной базе
0,9319 сек.
### Время выполнения в индексированной базе
Создан индекс по полю first_name (380 уникальных элементов):
CREATE INDEX first_name_index ON workers(first_name); (Запрос занял 4,4663 сек.)
Запрос на поиск занял 0,0196 сек.

Создан индекс по полю birthday (8708 уникальных элементов):
CREATE INDEX birthday_index ON workers(birthday); (Запрос занял 2,9529 сек.) 
Запрос на поиск занял 0,0231 сек.

При наличии обоих индексов запрос на поиск занял 0,0224 сек.
Если таблица проиндексирована по столбцам number и series, запрос на поиск занимает 1,0299 сек.
## Подсчитать какое количество сотрудников работает в компании 'Cеверсталь'
### Текст запроса
SELECT COUNT(*) FROM `workers` WHERE `company` = 'Северсталь'; 
## Создание индекса по полю company
CREATE INDEX company_index ON workers(company); (Запрос занял 6,2791 сек.)

PHPMyAdmin не отображает время выполнения запроса COUNT, поэтому объективно сравнить время поиска по исходной и индексированной базам не получилось. Субъективно поиск по индексированной базе быстрее в несколько раз.

## Выбрать всех инженеров с датой рождения между 1 декабря 1979 и 1 февраля 1980, отсортированных по имени и фамилии
### Текст запроса
SELECT * FROM `workers` WHERE `role`='Инженер' AND `birthday`>='1979-12-01' AND `birthday`<='1980-02-01' ORDER BY first_name ASC, last_name ASC
### Время выполнения в исходной базе
0,9101 сек.
### Время выполнения в базе с индексом по birthday
0,0687 сек.
### Время выполнения в базе с индексом по role
0,0508 сек.
## Выбрать всех сотрудников с должностями Химик-технолог, Химик и Биохимик, которые работают в X5 Retail Group отсортированных по дате рождения - от молодых к старым
### Текст запроса
SELECT * FROM `workers` WHERE (`role`='Химик-технолог' OR `role`='Химик' OR `role`= 'Биохимик') AND `company`='X5 Retail Group' ORDER BY birthday DESC
### Время выполнения в исходной базе
1,1788 сек.
### Время выполнения в базе с индексом по role
0,1677 сек.
### Время выполнения в базе с индексом по company
0,0403 сек.
### Время выполнения в базе с индексом по role и company
0,0474 сек.
### Текст запроса
SELECT * FROM `workers` WHERE `company`='X5 Retail Group' AND (`role`='Химик-технолог' OR `role`='Химик' OR `role`= 'Биохимик') ORDER BY birthday DESC
### Время выполнения в исходной базе
0,8874 сек.
При указании первого условия по полю company выборка происходит быстрее. Дальнейшие эксперименты проводились со вторым запросом.
### Время выполнения в базе с индексом по role
0,1997 сек.
### Время выполнения в базе с индексом по company
0,0578 сек.
### Время выполнения в базе с индексом по role и company
0,0348 сек.
