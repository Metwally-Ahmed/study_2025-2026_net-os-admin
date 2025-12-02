---
## Front matter
title: "Отчёт по лабораторной работе 15"
subtitle: "Настройка сетевого журналирования"
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

Получение навыков по работе с журналами системных событий.

# Выполнение

## Настройка сервера сетевого журнала

### Создание конфигурации сервера

1. На сервере создан файл конфигурации сетевого приёма журналов:  
   переходим в каталог `/etc/rsyslog.d` и создаём файл `netlog-server.conf`.

2. В файл `/etc/rsyslog.d/netlog-server.conf` добавлены строки для включения TCP-приёма сообщений:  
   загрузка модуля imtcp и запуск TCP-сервера на порту 514.

   ![Конфигурация netlog-server.conf](Screenshot_1.png){ #fig:001 width=80% }

### Перезапуск службы и проверка портов

1. Служба rsyslog перезапущена.

2. Выполнена проверка прослушиваемых портов, связанных с rsyslog.  
   Видно, что процесс слушает TCP-порт 514.

   ![Порты rsyslog и вывод команд firewall-cmd](Screenshot_2.png){ #fig:002 width=80% }

### Настройка межсетевого экрана

3. На сервере открыт TCP-порт 514 для приёма сообщений.

## Настройка клиента сетевого журнала

1. На клиенте создан файл `/etc/rsyslog.d/netlog-client.conf`.

2. В конфигурацию добавлена строка перенаправления всех сообщений на сервер по TCP-порту 514:  
   `*.* @@server.ahmedfarg.net:514`

   ![Конфигурация клиента netlog-client.conf](Screenshot_3.png){ #fig:003 width=80% }

3. Служба rsyslog перезапущена.

## Просмотр журнала

1. На сервере просмотрен файл `/var/log/messages`.  
   В логах появляются сообщения от клиента.

   ![Вывод системных сообщений](Screenshot_4.png){ #fig:004 width=80% }

2. Запущена графическая программа для просмотра системных журналов.  
   Показаны загрузка CPU, память и сетевые показатели.

   ![Графическая утилита мониторинга](Screenshot_5.png){ #fig:005 width=80% }

3. На сервер установлен просмотрщик системных логов lnav.

## Внесение изменений в Vagrant provisioning

### Настройка provisioning для сервера

1. В каталоге `/vagrant/provision/server` создан каталог `netlog`, содержащий подкаталог  
   `etc/rsyslog.d`, куда помещён конфигурационный файл netlog-server.conf.

2. Создан исполняемый файл `netlog.sh`, содержащий:  
   копирование конфигурации, восстановление контекстов SELinux, настройку firewall и перезапуск rsyslog.

   ![Скрипт provisioning на сервере](Screenshot_6.png){ #fig:006 width=80% }

### Настройка provisioning для клиента

1. В каталоге `/vagrant/provision/client` создан каталог `netlog`, куда помещён файл  
   netlog-client.conf.

2. Создан исполняемый файл `netlog.sh`, обеспечивающий копирование конфигураций,  
   восстановление контекстов и перезапуск rsyslog.

   ![Скрипт provisioning на клиенте](Screenshot_7.png){ #fig:007 width=80% }


# Заключение

Сетевой журнал был успешно настроен: сервер принял конфигурацию для получения сообщений по TCP, клиент корректно перенаправляет логи, взаимодействие проверено просмотром системных сообщений. Дополнительно подготовлены provisioning-скрипты для автоматизации развёртывания обеих виртуальных машин.

# Контрольные вопросы

1. **Какой модуль rsyslog вы должны использовать для приёма сообщений от journald?**
   Для интеграции journald с rsyslog используется модуль **imjournal**.

2. **Как называется устаревший модуль, который можно использовать для включения приёма сообщений журнала в rsyslog?**
   Устаревший модуль — **imuxsock**.

3. **Чтобы убедиться, что устаревший метод приёма сообщений из journald в rsyslog не используется, какой дополнительный параметр следует применить?**
   Нужно добавить параметр:
   **`UseUxSock off`**

4. **В каком конфигурационном файле содержатся настройки, которые позволяют настраивать работу журнала?**
   Основные параметры работы journald находятся в файле
   **`/etc/systemd/journald.conf`**.

5. **Каким параметром управляется пересылка сообщений из journald в rsyslog?**
   За пересылку отвечает параметр journald:
   **`ForwardToSyslog=`**

6. **Какой модуль rsyslog можно использовать для включения сообщений из файла журнала, не созданного rsyslog?**
   Для чтения любых лог-файлов используется модуль
   **imfile**.

7. **Какой модуль rsyslog нужно использовать для пересылки сообщений в базу данных MariaDB?**
   Для записи сообщений в MariaDB применяют модуль
   **ommysql**.

8. **Какие две строки нужно включить в rsyslog.conf, чтобы позволить текущему журнальному серверу принимать сообщения через TCP?**

   ```
   $ModLoad imtcp
   $InputTCPServerRun 514
   ```

9. **Как настроить локальный брандмауэр, чтобы разрешить приём сообщений журнала через порт TCP 514?**
   Нужно открыть порт командой firewall-cmd:

   ```
   firewall-cmd --add-port=514/tcp
   firewall-cmd --add-port=514/tcp --permanent
   ```
