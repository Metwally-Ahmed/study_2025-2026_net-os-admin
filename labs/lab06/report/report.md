---
## Front matter
title: "Отчёт по лабораторной работе 6"
subtitle: "Установка и настройка системы управления базами данных MariaDB"
author: "Метвалли Ахмед Фарг Набеех"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---


# Цель работы

Приобретение практических навыков по установке и конфигурированию системы управления базами данных на примере программного обеспечения MariaDB.

# Выполнение

## Установка и базовая настройка MariaDB

1. На виртуальной машине `server` установлены пакеты **mariadb** и **mariadb-server**.  
   Для запуска и включения службы использовались команды:  
   systemctl start mariadb  
   systemctl enable mariadb  

   ![Установка и запуск службы mariadb](Screenshot_1.png){ #fig:001 width=80% }

2. Выполнен скрипт **mysql_secure_installation**, в ходе которого:  
   - установлен пароль для пользователя `root` БД,  
   - отключён удалённый вход под пользователем `root`,  
   - удалена тестовая база данных и анонимные пользователи,  
   - перезагружены таблицы привилегий.  

   ![Конфигурация безопасности MariaDB](Screenshot_2.png){ #fig:002 width=80% }

3. Вход в базу данных выполнен под пользователем `root`.  
   С помощью SQL-запроса `SHOW DATABASES;` выведен список доступных баз данных.  
   В системе доступны стандартные служебные БД:  
   - information_schema  
   - mysql  
   - performance_schema  
   - sys  

   ![Вход в MariaDB и просмотр баз данных](Screenshot_3.png){ #fig:003 width=80% }

4. Для ознакомления с доступными командами в интерактивной оболочке использована команда `\h`.  
   Отобразился список служебных команд клиента MariaDB.  

   ![Список команд MariaDB](Screenshot_4.png){ #fig:004 width=80% }
   

## Конфигурация кодировки символов

1. Вход в базу данных выполнен с правами администратора.  
   С помощью команды `status` получена информация о текущей конфигурации MariaDB:  
   - текущий пользователь: root@localhost  
   - SSL: не используется  
   - сервер: MariaDB, версия 10.11.11  
   - подключение: через UNIX socket  
   - кодировка сервера и базы данных: latin1  
   - кодировка клиента и соединения: utf8mb3  
   - сокет: /var/lib/mysql/mysql.sock  
   - текущее время работы: 5 мин 32 сек  

   ![Статус MariaDB до изменения конфигурации](Screenshot_5.png){ #fig:005 width=80% }

2. В каталоге `/etc/my.cnf.d` создан файл `utf8.cnf`

Это указывает на использование кодировки UTF-8 по умолчанию как для клиента, так и для сервера.  

![Файл utf8.cnf](Screenshot_6.png){ #fig:006 width=80% }

3. После перезапуска MariaDB и повторного входа в систему командой `status` отобразилось:  
- кодировка сервера: utf8mb3  
- кодировка базы данных: utf8mb3  
- кодировка клиента и соединения: utf8mb3  

![Статус MariaDB после изменения конфигурации](Screenshot_7.png){ #fig:007 width=80% }

## Создание базы данных

1. Вход в базу данных выполнен с правами администратора.  
   Создана база данных `addressbook` с кодировкой `utf8`.  

2. Переключение на созданную базу данных выполнено командой `USE addressbook;`.  
   На момент создания база была пустой, таблицы отсутствовали.  

   ![Создание базы данных и проверка таблиц](Screenshot_8.png){ #fig:008 width=80% }

3. Создана таблица `city` с двумя полями:  
   - name (тип VARCHAR(40))  
   - city (тип VARCHAR(40))  

   Таблица заполнена тремя строками:  
   - Иванов, Москва  
   - Петров, Сочи  
   - Сидоров, Дубна  

   Запрос `SELECT * FROM city;` отобразил введённые данные.  

   ![Создание таблицы и добавление данных](Screenshot_9.png){ #fig:009 width=80% }

4. Создан пользователь `faisalahmad@'%'` с паролем.  
   Ему предоставлены права на выполнение операций `SELECT`, `INSERT`, `UPDATE`, `DELETE` в базе `addressbook`.  
   Привилегии обновлены с помощью `FLUSH PRIVILEGES;`.  

   Для проверки структуры таблицы использована команда `DESCRIBE city;`, которая показала наличие двух полей: name и city, оба типа VARCHAR(40).  

   ![Создание пользователя и просмотр структуры таблицы](Screenshot_10.png){ #fig:010 width=80% }

5. После выхода из MariaDB выполнены команды `mysqlshow`, которые показали:  
   - в системе есть база данных `addressbook`,  
   - в этой базе содержится таблица `city`.  

## Резервные копии

1. На виртуальной машине `server` создан каталог для хранения резервных копий:  
   /var/backup  

2. Выполнено создание резервной копии базы данных `addressbook`:  
   - полный дамп сохранён в файл `/var/backup/addressbook.sql`,  
   - создана сжатая резервная копия `/var/backup/addressbook.sql.gz`,  
   - создана резервная копия с указанием даты и времени создания.  

   ![Создание резервных копий базы addressbook](Screenshot_11.png){ #fig:011 width=80% }

3. Выполнено восстановление базы данных `addressbook`:  
   - из обычного дампа `/var/backup/addressbook.sql`,  
   - из сжатого архива `/var/backup/addressbook.sql.gz`.  

## Внесение изменений в настройки внутреннего окружения

1. В каталоге `/vagrant/provision/server` создана структура подкаталогов:  
   - `/vagrant/provision/server/mysql/etc/my.cnf.d` для конфигурационных файлов,  
   - `/vagrant/provision/server/mysql/var/backup` для резервных копий.  

2. В указанные каталоги скопированы:  
   - файл конфигурации `utf8.cnf` из `/etc/my.cnf.d`,  
   - резервные копии базы данных из `/var/backup`.  

3. Создан исполняемый файл `mysql.sh` в каталоге `/vagrant/provision/server`.  

   ![Подготовка окружения и копирование файлов](Screenshot_12.png){ #fig:012 width=80% }

# Заключение

MariaDB была успешно установлена, выполнена её базовая настройка и конфигурация безопасности.  
Создана тестовая база данных `addressbook` с таблицей `city`, добавлены пользователи и предоставлены права.  
Реализовано резервное копирование и восстановление базы данных, а также подготовлено окружение для автоматизации.  

# Контрольные вопросы

1. **Какая команда отвечает за настройки безопасности в MariaDB?**  
   Скрипт `mysql_secure_installation`.

2. **Как настроить MariaDB для доступа через сеть?**  
   В конфигурационном файле `/etc/my.cnf.d/` или `/etc/my.cnf` изменить параметр `bind-address`, указав `0.0.0.0` или IP сервера, и настроить права доступа для пользователей с нужных хостов.

3. **Какая команда позволяет получить обзор доступных баз данных после входа в среду оболочки MariaDB?**  
   `SHOW DATABASES;`

4. **Какая команда позволяет узнать, какие таблицы доступны в базе данных?**  
   `SHOW TABLES;`

5. **Какая команда позволяет узнать, какие поля доступны в таблице?**  
   `DESCRIBE имя_таблицы;` или `SHOW COLUMNS FROM имя_таблицы;`

6. **Какая команда позволяет узнать, какие записи доступны в таблице?**  
   `SELECT * FROM имя_таблицы;`

7. **Как удалить запись из таблицы?**  
   `DELETE FROM имя_таблицы WHERE условие;`

8. **Где расположены файлы конфигурации MariaDB? Что можно настроить с их помощью?**  
   - `/etc/my.cnf`  
   - `/etc/my.cnf.d/`  
   В них задаются порты, пути к файлам, параметры кодировки, сетевые настройки, ограничения доступа.

9. **Где располагаются файлы с базами данных MariaDB?**  
   По умолчанию — в каталоге `/var/lib/mysql/`, где для каждой базы создаётся отдельная директория.

10. **Как сделать резервную копию базы данных и затем её восстановить?**  
   - Резервная копия:  
     `mysqldump -u root -p имя_базы > backup.sql`  
   - Восстановление:  
     `mysql -u root -p имя_базы < backup.sql`  
   Для сжатых копий можно использовать `gzip` и `zcat`.
