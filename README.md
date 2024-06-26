# Домашнее задание к занятию "`«MySQL"»`" 

---
### Задание 1

Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.  
Изучите бэкап БД и восстановитесь из него.  
Перейдите в управляющую консоль mysql внутри контейнера.  
Используя команду \h, получите список управляющих команд.  
Найдите команду для выдачи статуса БД и приведите в ответе из её вывода версию сервера БД.  

----

### Выполнения задания 1

1 **Создание Docker Volume**  
Создаю volume в Docker, чтобы сохранить данные MySQL за пределами контейнера.     
Это позволит вам сохранять данные даже после удаления или пересоздания контейнера.    

```bash
docker volume create mysql_data
```
2 **Запуск MySQL контейнера**      
Теперь запустим контейнер MySQL версии 8, используя созданный volume для хранения данных.   
Также установливаю пароль для пользователя root как Byzgaev123:

```bash
docker run --name mysql-instance -e MYSQL_ROOT_PASSWORD=Byzgaev123 -v mysql_data:/var/lib/mysql -d -p 3306:3306 mysql:8
```
3 **Скачивание бэкапа БД**     
Скачал бэкап test_dump.sql из указанного репозитория:

```bash
wget https://raw.githubusercontent.com/netology-code/virt-homeworks/virt-11/06-db-03-mysql/test_data/test_dump.sql
```

4 **Создание базы данных**   
Перед восстановлением из бэкапа создаю базу данных.  
  1. Подключаюсь к MySQL контейнеру: 
```bash
docker exec -it mysql-instance mysql -u root -pByzgaev123
```
  2. В интерактивном режиме MySQL выполняю следующую команду для создания базы данных:

```bash
CREATE DATABASE test_db;
```
5 **Восстановление из бэкапа**   

Теперь, когда у меня есть база данных, я могу восстановиться из бэкапа:

```bash
docker exec -i mysql-instance mysql -u root -pByzgaev123 test_db < test_dump.sql
```

5 **Подключение к MySQL и выполнение команд**  

  1. Подключаюсь снова к MySQL:  
```bash
docker exec -it mysql-instance mysql -u root -pByzgaev123
```
  2. Переключаюсь на базу данных:  
```bash
USE test_db;
```
  3. Чтобы найти команду для выдачи статуса БД, использую команду:  
```bash
status; или \s.
```
  4. Для получения списка таблиц в вашей базе данных используйте SQL-команду:  
```bash
SHOW TABLES;
```

![image.jpg](https://github.com/Byzgaev-I/MySQL/blob/main/1.png)

----

### Задание 2

Создайте пользователя test в БД c паролем test-pass, используя:  

- плагин авторизации mysql_native_password
- срок истечения пароля — 180 дней
- количество попыток авторизации — 3
- максимальное количество запросов в час — 100
- аттрибуты пользователя:
-    Фамилия "Pretty"
-    Имя "James".
Предоставьте привелегии пользователю test на операции SELECT базы test_db.

Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES, получите данные по пользователю test и приведите в ответе к задаче. 

----

### Выполнения задания 2  

Создаю пользователя с паролем:
```bash
CREATE USER 'test'@'localhost' IDENTIFIED BY 'test-pass';
```
Задаю атрибуты имя и фамилию:
```bash
ALTER USER 'test'@'localhost' ATTRIBUTE '{"fname":"James", "lname":"Pretty"}';
```
![image.jpg](https://github.com/Byzgaev-I/MySQL/blob/main/2.png)  

----

### Задание 3
Установите профилирование SET profiling = 1.   
Изучите вывод профилирования команд SHOW PROFILES;.    
Исследуйте, какой engine используется в таблице БД test_db и приведите в ответе.    
Измените engine и приведите время выполнения и запрос на изменения из профайлера в ответе:    
на MyISAM,
на InnoDB.

----

### Выполнения задания 3  

Подключаюсь к MySQL и активирую профилирование:

```bash
SET profiling = 1;
```

![image.jpg](https://github.com/Byzgaev-I/MySQL/blob/main/3.png)

![image.jpg](https://github.com/Byzgaev-I/MySQL/blob/main/3-1.png)

----

### Задание 4   

Изучите файл my.cnf в директории /etc/mysql.
Измените его согласно ТЗ (движок InnoDB):
-  скорость IO важнее сохранности данных;
-  нужна компрессия таблиц для экономии места на диске;
-  размер буффера с незакомиченными транзакциями 1 Мб;
-  буффер кеширования 30% от ОЗУ;
-  размер файла логов операций 100 Мб.
Приведите в ответе изменённый файл my.cnf.

----

### Выполнения задания 4  

**Общие настройки**
```bash
default_storage_engine = InnoDB
```
**Настройки InnoDB** 
```bash
innodb_flush_log_at_trx_commit = 2 # Скорость IO важнее сохранности данных  
innodb_file_per_table = 1 # Включить компрессию таблиц  
innodb_log_buffer_size = 1M # Размер буффера с незакомиченными транзакциями 1 Мб  
innodb_buffer_pool_size = 2457M # Буффер кеширования ~30% от ОЗУ, для 8 ГБ ОЗУ  
innodb_log_file_size = 100M # Размер файла логов операций 100 Мб  
```
**Компрессия таблиц InnoDB**  
```bash
innodb_file_format = Barracuda # Требуется для компрессии таблиц  
```



