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
Также установлю пароль для пользователя root как Byzgaev123.

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

  1. Подключаюсь снова к MySQL  
```bash
docker exec -it mysql-instance mysql -u root -pByzgaev123
```
  2. Переключаюсь на базу данных:  
```bash
USE test_db;
```
  3. Чтобы найти команду для выдачи статуса БД, использую команду 
```bash
status; или \s.
```
  4. Для получения списка таблиц в вашей базе данных используйте SQL-команду:  
```bash
SHOW TABLES;
```







