---
## Front matter
title: "Отчёт по лабораторной работе 12"
subtitle: "Синхронизация времени"
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

Получение навыков по управлению системным временем и настройке синхронизации времени.

# Выполнение

## Настройка параметров времени

1. На сервере и клиенте просмотрены параметры настройки даты и времени с помощью команды timedatectl.  
   В обоих случаях система использует временную зону UTC (UTC +0000), сетевой сервис синхронизации (NTP service) активен, а системные часы синхронизированы.  

   ![Результат timedatectl на сервере](Screenshot_1.png){ #fig:001 width=80% }  
   ![Результат timedatectl на клиенте](Screenshot_2.png){ #fig:002 width=80% }

2. Проверено текущее системное время командой date и аппаратное время командой hwclock.  
   Результаты показывают, что системные и аппаратные часы синхронизированы и отображают одинаковое время в формате UTC.  

## Управление синхронизацией времени

1. После перезапуска службы chronyd на клиенте и сервере проверены источники синхронизации времени.  
   На сервере видно, что он использует внешние NTP-серверы (ntppool.yandex, fiord.ru и др.),  
   а на клиенте — внутренний сервер mail.ahmedfarg.net.  

   ![Источники времени на сервере](Screenshot_3.png){ #fig:003 width=80% }  
   ![Источники времени на клиенте](Screenshot_4.png){ #fig:004 width=80% }


2. На сервере установлено и настроено программное обеспечение chrony.  
   В конфигурационном файле /etc/chrony.conf добавлена строка, разрешающая доступ к NTP-серверу клиентам из локальной сети.  

   ![Файл chrony.conf на сервере](Screenshot_5.png){ #fig:005 width=80% }

3. Служба chronyd перезапущена, а в межсетевом экране открыт доступ для NTP.  

4. На клиенте отредактирован файл /etc/chrony.conf: добавлена строка с указанием сервера server.ahmedfarg.net iburst и удалены другие серверные источники.  

   ![Файл chrony.conf на клиенте](Screenshot_6.png){ #fig:006 width=80% }

	На клиенте — внутренний сервер mail.ahmedfarg.net. 

   ![Источники времени на клиенте](Screenshot_7.png){ #fig:007 width=80% }


## Внесение изменений в настройки внутреннего окружения виртуальных машин

1. На виртуальной машине server выполнен переход в каталог для настройки окружения `/vagrant/provision/server/`.  
   Создана структура подкаталогов для размещения конфигурационных файлов NTP и скопирован файл конфигурации `chrony.conf`.  

   ![Создание структуры каталогов и копирование конфигурации на сервере](Screenshot_8.png){ #fig:008 width=80% }

2. В каталоге `/vagrant/provision/server` создан исполняемый файл `ntp.sh`.  
   Его содержимое включает установку пакета **chrony**, копирование конфигурации, настройку SELinux-контекста,  
   открытие доступа к службе NTP в межсетевом экране и перезапуск службы **chronyd**.  

   ![Содержимое скрипта ntp.sh на сервере](Screenshot_9.png){ #fig:009 width=80% }

3. На виртуальной машине client проделаны аналогичные действия: создан каталог `/vagrant/provision/client/ntp/etc/`,  
   в который скопирован файл `chrony.conf`, и создан исполняемый файл `ntp.sh`.  

   ![Создание структуры каталогов на клиенте](Screenshot_10.png){ #fig:010 width=80% }

4. Скрипт `ntp.sh` на клиенте выполняет копирование конфигурации NTP, восстановление контекста SELinux  
   и перезапуск службы **chronyd**.  

   ![Содержимое скрипта ntp.sh на клиенте](Screenshot_11.png){ #fig:0111 width=80% }

# Заключение

Служба синхронизации времени была успешно установлена и настроена с использованием chrony.  
Сервер настроен на получение времени от внешних NTP-источников, а клиент — на синхронизацию с локальным сервером.  
Созданные скрипты автоматизируют процесс настройки NTP для обеих виртуальных машин, обеспечивая единообразие параметров и точную синхронизацию времени в сети.

# Контрольные вопросы

1. **Почему важна точная синхронизация времени для служб баз данных?**  
   Потому что многие операции в базах данных зависят от меток времени. Несоответствие системных часов может привести к нарушению целостности данных, ошибкам при репликации и несогласованности транзакций.

2. **Почему служба проверки подлинности Kerberos сильно зависит от правильной синхронизации времени?**  
   Kerberos использует временные метки в билетах аутентификации. Если время на клиентах и сервере различается более чем на несколько минут, билеты считаются недействительными, и аутентификация не проходит.

3. **Какая служба используется по умолчанию для синхронизации времени на RHEL 7?**  
   По умолчанию используется служба **chronyd** (пакет chrony), заменяющая устаревший **ntpd**.

4. **Какова страта по умолчанию для локальных часов?**  
   Для локальных (несинхронизированных) часов страта по умолчанию равна **10**.

5. **Какой порт брандмауэра должен быть открыт, если вы настраиваете свой сервер как одноранговый узел NTP?**  
   Необходимо открыть **UDP-порт 123**, используемый протоколом NTP.

6. **Какую строку вам нужно включить в конфигурационный файл chrony, если вы хотите быть сервером времени, даже если внешние серверы NTP недоступны?**  
   Нужно раскомментировать строку:  
   **local stratum 10**

7. **Какую страту имеет хост, если нет текущей синхронизации времени NTP?**  
   В этом случае хост получает **страту 10**, что означает использование локальных часов.

8. **Какую команду вы бы использовали на сервере с chrony, чтобы узнать, с какими серверами он синхронизируется?**  
   Используется команда:  
   **chronyc sources**

9. **Как вы можете получить подробную статистику текущих настроек времени для процесса chrony вашего сервера?**  
   Для получения детальной информации используется команда:  
   **chronyc tracking**
