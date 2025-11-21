---
## Front matter
title: "Отчёт по лабораторной работе 14"
subtitle: "Настройка файловых служб Samba"
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

Приобретение навыков настройки доступа групп пользователей к общим ресурсам по протоколу SMB.

# Выполнение

## Установка и подготовка Samba-сервера

1. На сервер были установлены необходимые пакеты Samba: `samba`, `samba-client`, `cifs-utils`.

   ![Установка пакетов Samba](Screenshot_1.png){ #fig:001 width=80% }

2. Создана группа `sambagroup` с идентификатором 1010. Пользователь добавлен в эту группу.

3. Создан каталог общего доступа `/srv/sambashare`, предназначенный для размещения файлов Samba.

## Настройка Samba

1. В конфигурационном файле `/etc/samba/smb.conf` изменён параметр рабочей группы

2. В конец файла добавлен раздел ресурса `sambashare`


![Файл smb.conf](Screenshot_2.png){ #fig:002 width=80% }

3. Конфигурация проверена на наличие синтаксических ошибок с помощью `testparm`.

## Запуск Samba и проверка работы

1. Служба Samba была запущена и добавлена в автозагрузку.

2. Статус демона подтверждает корректный запуск.

![Статус службы SMB](Screenshot_3.png){ #fig:003 width=80% }

3. Проверено наличие общего доступа с помощью `smbclient -L //server`.  
Анонимное подключение успешно отображает список доступных ресурсов.

## Настройка межсетевого экрана

1. Просмотрено описание службы Samba в конфигурации firewalld:

![Файл samba.xml](Screenshot_4.png){ #fig:004 width=80% }

2. Служба Samba была добавлена в правила межсетевого экрана и закреплена в постоянной конфигурации. После этого параметры перезагружены.

## Настройка прав и SELinux

1. Каталог `/srv/sambashare` передан группе `sambagroup`, предоставлены права чтения, записи и выполнения для группы.

2. Проверен текущий SELinux-контекст каталога:

![Первичный контекст SELinux](Screenshot_5.png){ #fig:005 width=80% }

3. Установлен корректный SELinux-контекст `samba_share_t` для каталога. Контекст успешно применён.

4. Разрешён экспорт Samba-ресурсов для чтения и записи через SELinux.

## Проверка доступа

1. Выведена информация о пользователе и его группах.

![Информация о пользователе](Screenshot_6.png){ #fig:006 width=80% }

2. На общем ресурсе создан тестовый файл, что подтверждает корректные права доступа.

## Добавление пользователя в базу Samba

1. Пользователь добавлен в локальную базу учётных записей Samba и ему назначен SMB-пароль.

![Добавление SMB-пользователя](Screenshot_7.png){ #fig:007 width=80% }

## Установка и подготовка клиента Samba

1. На клиентской машине установлены пакеты `samba-client` и `cifs-utils`, необходимые для подключения к SMB-ресурсам.

   ![Установка пакетов на клиенте](Screenshot_9.png){ #fig:009 width=80% }

2. Просмотрено описание службы `samba-client` в конфигурации firewalld.

   ![Файл samba-client.xml](Screenshot_8.png){ #fig:008 width=80% }

3. Межсетевой экран настроен для разрешения Samba-клиента. Конфигурация добавлена как временно, так и на постоянной основе.

## Настройка пользователя и рабочей группы

1. На клиенте создана группа `sambagroup`, и пользователь добавлен в неё.

2. В файле `/etc/samba/smb.conf` изменён параметр рабочей группы:

![Файл smb.conf на клиенте](Screenshot_10.png){ #fig:010 width=80% }

## Проверка доступа к Samba-серверу

1. Выполнена проверка общих ресурсов с клиента в режиме анонимного доступа.  
Просмотр ресурсов выполняется под учётной записью **анонимного пользователя**.

![Анонимный доступ smbclient](Screenshot_11.png){ #fig:011 width=80% }

2. Выполнена проверка ресурсов под учётной записью пользователя `ahmedfarg`.  
В этом случае доступ осуществляется под **пользователем ahmedfarg**.

## Монтирование SMB-ресурса вручную

1. На клиенте создан каталог для монтирования:

2. Общий ресурс смонтирован с использованием имени пользователя `ahmedfarg`.

![Ручное монтирование](Screenshot_12.png){ #fig:012 width=80% }

3. Проверена возможность записи на общий ресурс.  
На ресурсе создан файл `ahmedfarg@client.txt`.

![Создание файла на общем ресурсе](Screenshot_13.png){ #fig:013 width=80% }

4. Ресурс был размонтирован.

## Автоматическое монтирование с использованием файла учётных данных

1. Создан файл `/etc/samba/smbusers` с учётными данными:

![Файл учетных данных smbusers](Screenshot_14.png){ #fig:014 width=80% }

2. В файл `/etc/fstab` добавлена строка для автоматического монтирования:

![Настройка fstab](Screenshot_15.png){ #fig:015 width=80% }

3. Выполнена проверка автоматического монтирования командой `mount -a`.  
Ресурс успешно подмонтирован.

![Проверка монтирования через fstab](Screenshot_16.png){ #fig:016 width=80% }

4. Подтверждено, что клиент может работать с SMB-ресурсом, и файлы успешно доступны после монтирования.

## Подготовка конфигурации

1. В каталоге `/vagrant/provision/server/` создан исполняемый файл `smb.sh`:

![Создание файла smb.sh](Screenshot_17.png){ #fig:017 width=80% }

2. В каталоге `/vagrant/provision/client/` создан исполняемый файл `smb.sh`:

![Создание файла smb.sh](Screenshot_18.png){ #fig:018 width=80% }

# Заключение

Клиент и сервер Samba были настроены таким образом, чтобы обеспечивать совместный доступ к ресурсам, управлять правами пользователей и безопасностью, а также поддерживать автоматическое монтирование и корректные политики SELinux.


# Контрольные вопросы

1. **Какова минимальная конфигурация для smb.conf для создания общего ресурса /data?**

   ```
   [share]
   path = /data
   read only = no
   ```

   Этого достаточно для предоставления базового доступа.

2. **Как настроить ресурс, дающий право записи всем пользователям, имеющим соответствующие права в Linux?**
   Убедиться, что в файловой системе у пользователей есть права на запись, и указать:

   ```
   read only = no
   ```

3. **Как ограничить доступ на запись только членам определённой группы?**

   ```
   write list = @groupname
   ```

4. **Какой переключатель SELinux позволяет доступ к домашним каталогам через SMB?**

   ```
   setsebool -P samba_enable_home_dirs on
   ```

5. **Как ограничить доступ к ресурсу клиентам из сети 192.168.10.0/24?**

   ```
   hosts allow = 192.168.10.
   ```

6. **Команда для отображения всех пользователей Samba:**

   ```
   pdbedit -L
   ```

7. **Что нужно сделать пользователю для доступа к многопользовательскому ресурсу?**
   Иметь учётную запись в системе и отдельную учётную запись в Samba через `smbpasswd -a user`.

8. **Как настроить ресурс с минимальной учётной записью alice?**

   ```
   force user = alice
   ```

   Все операции будут выполняться от имени alice.

9. **Как скрыть учётные данные Samba в /etc/fstab?**
   Использовать файл credentials:

   ```
   credentials=/etc/samba/smbusers
   ```

   И установить для него права 600.

10. **Команда для перечисления всех ресурсов Samba на сервере:**

```
smbclient -L //server
```