---
## Front matter
title: "Отчёт по лабораторной работе 2"
subtitle: "Настройка DNS-сервера"
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

Приобрести практические навыки по установке и конфигурации DNS-сервера, а также закрепить теоретические знания о принципах работы системы доменных имен.

# Выполнение

## Установка и первичная проверка DNS-сервера

1. На виртуальной машине `server` установлен пакет **bind** и утилиты для работы с DNS.

2. С помощью команды **dig** выполнен тестовый запрос к DNS-адресу `www.yandex.ru`.  
   На экране отобразилась структура ответа: заголовок, вопрос, секция ответа с IP-адресами.  

   ![Результат dig www.yandex.ru](Screenshot_1.png){ #fig:001 width=80% }

## Конфигурация DNS-сервера

1. Содержимое файла `/etc/resolv.conf` показывает, что ранее использовался внешний DNS (10.0.2.3).  

   ![Файл resolv.conf](Screenshot_2.png){ #fig:002 width=80% }

2. В файле `/etc/named.conf` заданы параметры работы DNS-сервера:  
   - слушает порт 53 на `127.0.0.1`,  
   - указывает путь для хранения кэша и статистики,  
   - разрешает запросы только с `localhost`.  

   ![Файл named.conf (начальная конфигурация)](Screenshot_2.png){ #fig:003 width=80% }

## Запуск и тестирование работы

1. Сервис `named` добавлен в автозагрузку и запущен.

2. Проверка через `dig` показала отличие в результатах при запросе напрямую (`dig www.yandex.ru`) и через локальный сервер (`dig @127.0.0.1 www.yandex.ru`).  
   Ответ от локального DNS имеет увеличенное время запроса, так как данные кэшируются впервые.  

   ![Результаты dig с локальным сервером](Screenshot_3.png){ #fig:004 width=80% }

## Настройка DNS по умолчанию для хоста

1. Через `nmcli` выполнены изменения конфигурации соединения `eth0`:  
   - отключён авто-DNS,  
   - в качестве сервера указан `127.0.0.1`.  

   ![Настройка DNS в NetworkManager](Screenshot_4.png){ #fig:005 width=80% }

2. После перезапуска `NetworkManager` в файле `/etc/resolv.conf` появился новый nameserver — `127.0.0.1`.  

## Разрешение запросов от внутренних узлов

1. В файле `/etc/named.conf` внесены изменения:  
   - `listen-on port 53 { 127.0.0.1; any; };` — разрешено прослушивание всех интерфейсов,  
   - `allow-query { localhost; 192.168.0.0/16; };` — разрешены запросы от внутренней сети.  

   ![Файл named.conf (доработанный)](Screenshot_5.png){ #fig:006 width=80% }

## Настройка firewall и проверка

1. Внесены изменения в правила брандмауэра для разрешения работы службы DNS.

2. Проверка через `lsof` подтвердила, что процесс **named** прослушивает порт 53 по протоколу UDP.  

   ![Проверка через lsof](Screenshot_6.png){ #fig:007 width=80% }

## Конфигурирование первичного DNS-сервера

1. На основе шаблона `named.rfc1912.zones` создан новый файл описания зон `user.net`.  
   В конфигурационном файле `/etc/named.conf` подключён данный файл с помощью директивы `include`.  

   ![Файл user.net с описанием зон](Screenshot_7.png){ #fig:008 width=80% }

2. Для прямой зоны `ahmedfarg.net` создан файл `/var/named/master/fz/ahmedfarg.net`.  
   В нём определены:  
   - SOA-запись с главным сервером `server.ahmedfarg.net`,  
   - NS-запись,  
   - A-записи для узлов `server` и `ns`, указывающие на `192.168.1.1`.  

   ![Прямая зона ahmedfarg.net](Screenshot_8.png){ #fig:009 width=80% }

3. Для обратной зоны `1.168.192.in-addr.arpa` создан файл `/var/named/master/rz/192.168.1`.  
   В нём заданы:  
   - SOA-запись,  
   - NS-запись,  
   - PTR-записи, сопоставляющие IP-адрес `192.168.1.1` с именами `server.ahmedfarg.net` и `ns.ahmedfarg.net`.  

   ![Обратная зона 192.168.1](Screenshot_9.png){ #fig:010 width=80% }

4. Для корректной работы демона **named** были исправлены права доступа и SELinux-контексты:  
   - рекурсивно изменены владельцы каталогов `/etc/named` и `/var/named` на `named:named`,  
   - выполнено восстановление контекстов командой `restorecon`,  
   - включён параметр SELinux `named_write_master_zones`.  

   ![Настройка прав и SELinux для named](Screenshot_10.png){ #fig:011 width=80% }

5. После внесённых изменений сервис `named` был перезапущен. Ошибки при проверке логов отсутствуют, зоны были загружены успешно.

## Анализ работы DNS-сервера

1. С помощью утилиты `dig` был выполнен запрос к серверу `ns.ahmedfarg.net`.  
   Ответ показал, что доменное имя `ns.ahmedfarg.net` корректно сопоставляется с адресом `192.168.1.1`.  

   ![Результат dig ns.ahmedfarg.net](Screenshot_11.png){ #fig:012 width=80% }

2. С помощью утилиты `host` проведён анализ зоны `ahmedfarg.net`:  
   - перечислены все записи зоны (A, NS, SOA),  
   - подтверждено наличие привязки имени `ahmedfarg.net` к адресу `192.168.1.1`,  
   - проверены обратные PTR-записи для IP-адреса `192.168.1.1`.  

   ![Анализ зоны через host](Screenshot_12.png){ #fig:013 width=80% }

Результаты подтверждают корректную работу как прямой, так и обратной зон.


## Внесение изменений в настройки окружения

1. В каталоге `/vagrant/provision/server/` создан подкаталог `dns`, куда были помещены конфигурационные файлы DNS.  

   ![Подготовка каталогов для DNS](Screenshot_13.png){ #fig:014 width=80% }

2. В каталоге `/vagrant/provision/server/` создан исполняемый скрипт `dns.sh`.  
   В нём описаны:  
   - установка необходимых пакетов (`bind`, `bind-utils`),  
   - копирование конфигурации,  
   - настройка владельцев и контекстов SELinux,  
   - настройка firewall,  
   - правка DNS-параметров в сетевом соединении,  
   - запуск и автозапуск сервиса `named`.  

   ![Скрипт автоматической настройки dns.sh](Screenshot_14.png){ #fig:015 width=80% }

3. В файле `Vagrantfile` добавлен вызов скрипта `dns.sh` для автоматической настройки DNS при развёртывании виртуальной машины.  

   ![Vagrantfile с настройкой DNS](Screenshot_15.png){ #fig:016 width=80% }

# Заключение

DNS-сервер был успешно установлен, настроен для прямой и обратной зон и проверен, после чего его конфигурация автоматизирована с помощью скрипта и Vagrant.

# Контрольные вопросы

1. **Что такое DNS?**  
   DNS — система доменных имён, преобразующая доменные адреса в IP и обратно.  

2. **Каково назначение кэширующего DNS-сервера?**  
   Хранит результаты запросов для ускорения доступа и снижения нагрузки на внешние DNS.  

3. **Чем отличается прямая DNS-зона от обратной?**  
   Прямая зона сопоставляет имена с IP (A-записи), обратная — IP с именами (PTR-записи).  

4. **В каких каталогах и файлах располагаются настройки DNS-сервера?**  
   - `/etc/named.conf` — основной конфиг;  
   - `/etc/named/*.zones` — описание зон;  
   - `/var/named/` — файлы прямых и обратных зон;  
   - `/etc/resolv.conf` — DNS-серверы по умолчанию.  

5. **Что указывается в файле resolv.conf?**  
   Адреса DNS-серверов, домен поиска и суффиксы поиска.  

6. **Какие типы записи описания ресурсов есть в DNS?**  
   - A — IPv4-адрес  
   - AAAA — IPv6-адрес  
   - NS — серверы имён  
   - SOA — начальная запись зоны  
   - PTR — обратное разрешение  
   - MX — почтовые сервера  
   - CNAME — псевдоним  
   - TXT — произвольный текст  

7. **Для чего используется домен in-addr.arpa?**  
   Для обратного разрешения IP → имя.  

8. **Для чего нужен демон named?**  
   Это служба (BIND), реализующая работу DNS-сервера.  

9. **Функции slave-сервера и master-сервера?**  
   Master хранит оригинальные зоны, slave получает копии через zone transfer.  

10. **Какие параметры отвечают за время обновления зоны?**  
    В SOA-записи: serial, refresh, retry, expire, minimum.  

11. **Как обеспечить защиту зоны от скачивания?**  
    Настроить `allow-transfer` и ACL для ограничения zone transfer.  

12. **Какая запись применяется для почтовых серверов?**  
    MX (Mail Exchanger).  

13. **Как протестировать работу DNS-сервера?**  
    Утилитами `dig`, `host`, `nslookup`.  

14. **Как управлять службой?**  
    `systemctl start|stop|restart|status <служба>`.  

15. **Как посмотреть отладочную информацию службы?**  
    `journalctl -xe` или запуск с повышенным логированием.  

16. **Где хранится отладочная информация?**  
    В `journalctl` и в `/var/log/` (например, `/var/log/messages`).  

17. **Как посмотреть файлы процесса?**  
    `lsof -p <PID>`, `lsof -i :53`, `cat /proc/<PID>/fd/`.  

18. **Примеры nmcli:**  
    - `nmcli con show` — список соединений  
    - `nmcli con mod eth0 ipv4.addresses 192.168.1.10/24` — задать IP  
    - `nmcli con mod eth0 ipv4.gateway 192.168.1.1` — задать шлюз  
    - `nmcli con mod eth0 ipv4.dns 8.8.8.8` — задать DNS  

19. **Что такое SELinux?**  
    Система обязательного контроля доступа в Linux.  

20. **Что такое контекст SELinux?**  
    Метка безопасности, определяющая доступ к объекту.  

21. **Как восстановить контекст SELinux?**  
    `restorecon -Rv /путь`.  

22. **Как создать правила из логов SELinux?**  
    С помощью `audit2allow` на основе `/var/log/audit/audit.log`.  

23. **Что такое булевый переключатель SELinux?**  
    Параметр, включающий или отключающий определённые функции политики.  

24. **Как посмотреть переключатели SELinux?**  
    `getsebool -a`.  

25. **Как изменить значение переключателя SELinux?**  
    `setsebool имя on/off` или `setsebool -P имя on/off` для сохранения.  

