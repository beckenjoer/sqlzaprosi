## Задание 1
**Знакомство с MySQL**\
 *use my_db* - сменить текущую БД на my_db\
 *show tables;* - посмотреть, какие таблицы есть в my_db\
 *desc user_private_message;* - посмотреть, какие типы полей есть в таблице user_private_message

## Задание 2 
**Написать запрос, который вернет названия дискуссионных групп, которые требуют подтверждение регистрации, т.е. таблица – discussion_group, поле approve_required равно 1 или true.**\
 *select group_id, name from discussion_group where approve_required = true;*

## Задание 3
**Написать запрос, который из таблицы user_private_message отберет записи:**\
1. **отправленные в ноябре 2020 года (поле send_time);**\
2. **текст сообщения начинается на ‘A’ (поле message_text);**\
3. **прочитанные не позже 10 дней от даты отправки (поле read_time).**\
 *select Greatest(message_id,user_from_id,user_to_id) ID, read_time "Read",send_time "Send", Left(message_text,10)*\
 *"Text" from user_private_message where send_time between '2020-11-01' and '2020-12-01' and message_text like "A%"*\
 *and (day(read_time)-day(send_time) <=10);*

## Задание 4
**Напишите запрос, который выберет из таблицы users_to_discussion_groups:\
- количество подтверждений присоединения к группам,\
- наиболее раннюю дату присоединения пользователя к группе,\
- дату наиболее позднего подтверждения участника в группе.**\
 *select count(approved) cnt,min(joined_time),max(joined_time) from users_to_discussion_groups where approved = true;*

## Задание 5
**Напишите SQL-запрос, который выбирает 20 последних зарегистрированных
пользователей. Поля в результатах выборки: user_id, registration_time.**\
 *select user_id, registration_time from user order by registration_time limit 190,10;*

## Задание 6
**Напишите SQL-запрос, который удовлетворяет следующим критериям:**
1) **В запросе в секции WITH указаны два подзапроса:**
- groups_with_approve — выбирает группы, в которых требуется подтверждение;
- new_groups — группы, созданные в 2020 году или позже, в которых требуется подтверждение.
2) **Между подзапросами groups_with_approve и new_groups есть зависимость.**
3) **В основном запросе происходит выборка всего из new_groups.**
```sql
*with*
*groups_with_approve as (select * from  discussion_group where approve_required=1),*
*new_groups as (select * from groups_with_approve where year(creation_time)>=2020)*
*select group_id,creation_time*
*from new_groups;*
```

## Задание 7
**Напишите SQL-запрос, который выбирает уникальные идентификаторы
пользователей среди администраторов групп и отправителей приватных
сообщений.**
*select user_from_id from user_private_message union select admin_user_id from discussion_group;.*


## Задание 8
**Напишите запрос, который выберет все даты, в которые были отправлены
какие-либо личные сообщения, и в которые любой из отправивших сообщения
сделал это только один раз в этот день.**

```sql
 _with cntsum as (select date(send_time) time ,  user_from_id, count(1) as cnt from 
 user_private_message group by date(send_time), user_from_id)
 select count(*)_ _user, sum(cnt) message, time from cntsum group by time having user=message;_
```
## Задание 9
**Знакомство с MongoDB**
*docker exec -it my-mongo mongosh*
*show dbs* - просмотр какие есть базы данных
*use my_db* - переключиться на использование какой-то базы данных

*show collections* - просмотр коллекций

## Задание 10
**Из коллекции постов выберите документы, в которых среди топиков
встречается ‘as’, идентификатор автора содержит example.ru, а score больше
100.**
```sql
 _db.posts.find({"score":{$gt:100},"topics":"as", "author":/.*example.ru.*/})_
```
## Задание 11
**Одним запросом добавьте два документа к коллекции posts:**
1) **creation_date — текущее время, автор — skbx@example. com, topics должен быть списком из одного элемента “mongodb”;**
2) **creation_date — 31 декабря 2021 года, автор — skbx@example. ru.**
*db.posts.insertMany([{"creation_date": new Date(), "author": "skbx@example. com", "topics": ["mongodb"]}, {"creation_date": new Date("2021-12-31"), "author": "skbx@example. ru"}]);*

## Задание 12
**Посчитайте сумму кармы по первым буквам имён пользователей для тех
пользователей, у которых больше 300 визитов.**
 ```sql
*db.users.aggregate([
	{$match: {visits: {$gt: 300}}},	
	{$group: { _id: 
		{ $substr: ["$first_name", 0, 1] }, 
		sum_karma: {$sum: "$karma"}
	}}
]);*
```


## Задание 13
**Знакомство с Redis**
*set index "index precalculated content"* - ключ index со значением “index precalculated content”\
*exists index* - проверка наличия ключа\
*ttl index* - проверка сколько осталось жить ключу\
*set index "index precalculated content" ex 120* - установка времени жизни ключа на 2 минуты\
*persist index* - отмена запланированного удаления ключа

## Задание 14
**Напишите последовательность команд для Redis:**
1. **Создайте в Redis структуру данных с ключом ratings для хранения
следующих значений рейтингов технологий: mysql — 10, postgresql — 20,
mongodb — 30, redis — 40.**
2. **По этому же ключу увеличьте значение рейтинга mysql на 15.**
3. **Удалите из структуры элемент с максимальным значением.**
4. **Выведите место в рейтинге для mysql.**
   
 1. *zadd ratings 10 mysql 20 postgresql 30 mongodb 40 redis*
 2. *zadd ratings 25 mysql*
 3. *zpopmax ratings*
 4. *zrank ratings mysql*

## Задание 15
**Напишите две команды для СУБД Redis:**
1. **Подпишитесь на все события, опубликованные на каналах, начинающихся с events.**
2. **Опубликуйте сообщение на канале events42 с текстом “Hello there”.**\
*SUBSCRIBE events*\
*pubsub channels*\
*SUBSCRIBE events42*\
*PUBLISH events42 "Hello there"*
