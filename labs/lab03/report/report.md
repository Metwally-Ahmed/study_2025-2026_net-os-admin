---
## Front matter
title: "Отчёт по лабораторной работе 3"
subtitle: "Настройка DHCP-сервера"
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

Приобретение практических навыков по установке и конфигурированию DHCP-сервера.

# Выполнение

## Установка и настройка DHCP-сервера

1. На виртуальной машине `server` установлен пакет **kea-dhcp4** и необходимые зависимости.  
   После установки были загружены библиотеки `kea-libs`, `libpq`, `log4cplus` и `mariadb-connector`.  

   ![Установка пакета kea](Screenshot_1.png){ #fig:001 width=80% }

2. Для сохранения исходного конфигурационного файла выполнено копирование:  
   `cp /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf__$(date -I)`

3. В конфигурационном файле `/etc/kea/kea-dhcp4.conf` произведены изменения:  
   - Добавлен собственный домен `ahmedfarg.net`.  
   - Настроен адрес DNS-сервера `192.168.1.1`.  
   - Определена подсеть `192.168.1.0/24` с диапазоном выдачи адресов `192.168.1.30–192.168.1.199`.  
   - Указан маршрутизатор `192.168.1.1`.

   ![Фрагмент конфигурации DHCP – domain-name-servers](Screenshot_2.png){ #fig:002 width=80% }  
   ![Фрагмент конфигурации DHCP – subnet4](Screenshot_3.png){ #fig:003 width=80% }

4. Проверка конфигурационного файла выполнилась успешно:  
   `kea-dhcp4 -t /etc/kea/kea-dhcp4.conf`

   ![Проверка конфигурации DHCP](Screenshot_4.png){ #fig:004 width=80% }

5. В DNS-зоне `ahmedfarg.net` добавлена запись для DHCP-сервера:  
   `dhcp    A   192.168.1.1`

   ![Файл прямой зоны ahmedfarg.net](Screenshot_5.png){ #fig:005 width=80% }

6. В обратной зоне `192.168.1` внесена запись:  
   `1   PTR dhcp.ahmedfarg.net.`

   ![Файл обратной зоны 192.168.1](Screenshot_6.png){ #fig:006 width=80% }

7. После перезапуска службы `named` проверена доступность DHCP-сервера по имени:  
   `ping dhcp.ahmedfarg.net`

   Ответ получен, что подтверждает корректную работу DNS.  

   ![Проверка доступности DHCP-сервера по имени](Screenshot_7.png){ #fig:007 width=80% }

8. Настроен межсетевой экран — разрешён сервис DHCP:  
   `firewall-cmd --add-service=dhcp`  
   `firewall-cmd --add-service=dhcp --permanent`

   Восстановлен SELinux-контекст для каталогов `/etc`, `/var/named`, `/var/lib/kea/`.  

   ![Настройка межсетевого экрана и SELinux](Screenshot_8.png){ #fig:008 width=80% }

9. DHCP-сервер запущен и добавлен в автозагрузку:  
   `systemctl start kea-dhcp4.service`  
   `systemctl enable kea-dhcp4.service`

## Анализ работы DHCP-сервера

1. В каталоге `vagrant/provision/client` создан файл `01-routing.sh`, который изменяет настройки NetworkManager так, чтобы трафик виртуальной машины `client` шёл через интерфейс `eth1`.

   ![Скрипт настройки маршрутизации на клиенте](Screenshot_9.png){ #fig:009 width=80% }

2. После запуска клиента видно, что интерфейсы получили настройки сети.  
   - `eth0` имеет служебный адрес `10.0.2.15`, используемый для подключения Vagrant.  
   - `eth1` получил адрес из диапазона DHCP `192.168.1.30` с маской `255.255.255.0` и широковещательным адресом `192.168.1.255`.  
   Это подтверждает, что клиент получил IP-адрес от DHCP-сервера.

   ![Сетевые интерфейсы клиента](Screenshot_10.png){ #fig:010 width=80% }

3. На сервере в файле `/var/lib/kea/kea-leases4.csv` отображаются сведения о выданных арендах адресов:  

- `192.168.1.30` — выданный клиенту IP-адрес.  
- `08:00:27:56:5a:98` — MAC-адрес сетевой карты клиента.  
- `01:08:00:27:56:5a:98` — идентификатор клиента DHCP.  
- `3600` — время аренды в секундах (1 час).  
- `1758104403` / `1758104408` — время окончания аренды в формате Unix timestamp.  
- `1` — ID подсети, к которой относится клиент.  
- `fqdn_fwd=0`, `fqdn_rev=0` — обновление прямых и обратных записей DNS отключено.  
- `client` — имя клиента.  
- `state=0` — аренда активна.  
- `pool_id=0` — адрес был выдан из первого пула подсети.

![Список выданных адресов на сервере](Screenshot_11.png){ #fig:011 width=80% }

## Настройка обновления DNS-зоны

1. На сервере с **Bind9** был создан ключ для авторизации динамических обновлений:  
   `tsig-keygen -a HMAC-SHA512 DHCP_UPDATER > /etc/named/keys/dhcp_updater.key`  

2. В конфигурации DNS-зон были внесены изменения. Для зоны `ahmedfarg.net` и обратной зоны `1.168.192.in-addr.arpa` разрешены обновления от DHCP-сервера.  

   ![Разрешение обновления зоны в named.conf](Screenshot_12.png){ #fig:12 width=80% }

3. В каталоге `/etc/kea/` создан файл `tsig-keys.json`, содержащий ключ в формате JSON.  

   ![Файл tsig-keys.json](Screenshot_13.png){ #fig:13 width=80% }

4. В конфигурации **kea-dhcp-ddns.conf** определены параметры подключения:  
   - ключ авторизации `DHCP_UPDATER`;  
   - forward-зона `ahmedfarg.net.`;  
   - reverse-зона `1.168.192.in-addr.arpa.`;  
   - DNS-сервер `192.168.1.1`.  

   ![Конфигурация kea-dhcp-ddns.conf](Screenshot_14.png){ #fig:14 width=80% }

5. Служба **kea-dhcp-ddns** была успешно запущена и работает в активном состоянии.  

   ![Запуск и проверка kea-dhcp-ddns](Screenshot_15.png){ #fig:15 width=80% }

6. В основном конфигурационном файле `/etc/kea/kea-dhcp4.conf` активировано обновление DNS-записей:  
   - `"enable-updates": true`  
   - `"ddns-qualifying-suffix": "ahmedfarg.net"`  
   - `"ddns-override-client-update": true`  

   ![Изменения в kea-dhcp4.conf для обновления DNS](Screenshot_16.png){ #fig:16 width=80% }

7. Конфигурация прошла проверку и DHCP-сервер был перезапущен. Статус сервиса показывает успешный запуск.  

   ![Перезапуск kea-dhcp4 и проверка статуса](Screenshot_17.png){ #fig:17 width=80% }

## Анализ работы DHCP-сервера после настройки обновления DNS-зоны

1. На виртуальной машине `client` выполнена проверка наличия DNS-записи о клиенте в прямой зоне с помощью команды `dig @192.168.1.1 client.ahmedfarg.net`.  

   ![Результат dig client.ahmedfarg.net](Screenshot_18.png){ #fig:18 width=80% }

2. Построчный разбор результата:  
   - `; <<>> DiG 9.18.33 <<>> @192.168.1.1 client.ahmedfarg.net` — запуск запроса утилитой `dig`, сервер DNS — `192.168.1.1`, искомое имя — `client.ahmedfarg.net`.  
   - `;; Got answer:` — получен ответ от DNS-сервера.  
   - `;; ->>HEADER<<- opcode: QUERY, status: NOERROR` — запрос выполнен успешно, ошибок нет.  
   - `;; QUESTION SECTION:` — формулировка запроса: A-запись для `client.ahmedfarg.net`.  
   - `;; ANSWER SECTION:` — найден ответ:  
     - `client.ahmedfarg.net. 1200 IN A 192.168.1.30` — хост `client` зарегистрирован в зоне `ahmedfarg.net`, ему соответствует IP-адрес `192.168.1.30`.  
   - `;; Query time: 1 msec` — время выполнения запроса.  
   - `;; SERVER: 192.168.1.1#53` — адрес и порт DNS-сервера, обработавшего запрос.  
   - `;; WHEN: ...` — время выполнения запроса.  
   - `;; MSG SIZE rcvd: 93` — размер ответа.  
## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. На виртуальной машине `server` в каталоге `/vagrant/provision/server/` создан каталог `dhcp` и в него скопированы конфигурационные файлы DHCP.  

2. Аналогично, в каталог `/vagrant/provision/server/dns/` скопированы файлы конфигурации DNS:  
   - содержимое каталога `/var/named/`;  
   - содержимое каталога `/etc/named/`.  

3. В каталоге `/vagrant/provision/server/` создан исполняемый файл `dhcp.sh`, в котором реализованы:  
   - установка пакета `kea`;  
   - копирование конфигурационных файлов;  
   - назначение владельцев и прав;  
   - восстановление SELinux-контекста;  
   - настройка файрвола;  
   - запуск и включение сервисов `kea-dhcp4` и `kea-dhcp-ddns`.  

   ![Скрипт dhcp.sh](Screenshot_19.png){ #fig:19 width=80% }

# Заключение

Настройка и проверка работы DHCP-сервера с динамическим обновлением DNS-зон подтвердили его корректное функционирование и автоматическую регистрацию клиентов в доменной зоне.

# Контрольные вопросы

1. **В каких файлах хранятся настройки сетевых подключений?**  
   В современных системах на базе NetworkManager настройки хранятся в `/etc/sysconfig/network-scripts/ifcfg-*` (в CentOS/RHEL) или в `/etc/NetworkManager/system-connections/` (в новых дистрибутивах).  

2. **За что отвечает протокол DHCP?**  
   DHCP отвечает за автоматическую выдачу IP-адресов и сетевых параметров клиентам (шлюз, маска, DNS).  

3. **Поясните принцип работы протокола DHCP. Какими сообщениями обмениваются клиент и сервер, используя протокол DHCP?**  
   Протокол работает по схеме DORA:  
   - **Discover** — клиент ищет сервер DHCP.  
   - **Offer** — сервер предлагает свободный адрес.  
   - **Request** — клиент запрашивает предложенный адрес.  
   - **Acknowledge** — сервер подтверждает аренду адреса.  

4. **В каких файлах обычно находятся настройки DHCP-сервера? За что отвечает каждый из файлов?**  
   - `/etc/kea/kea-dhcp4.conf` — основная конфигурация DHCP-сервера Kea.  
   - `/var/lib/kea/kea-leases4.csv` — база данных с информацией об аренде адресов.  
   - `/etc/kea/kea-dhcp-ddns.conf` — конфигурация динамических обновлений DNS.  
   - `/etc/kea/tsig-keys.json` — ключи для аутентификации при DDNS.  

5. **Что такое DDNS? Для чего применяется DDNS?**  
   DDNS — динамическое обновление DNS-зон, позволяющее автоматически вносить в DNS записи о клиентах, получивших адреса через DHCP.  

6. **Какую информацию можно получить, используя утилиту ifconfig? Приведите примеры с использованием различных опций.**  
   `ifconfig` показывает IP-адреса, маски, MAC-адреса, состояние интерфейсов и статистику пакетов.  
   - `ifconfig` — список всех активных интерфейсов.  
   - `ifconfig eth0` — параметры конкретного интерфейса.  
   - `ifconfig -a` — информация обо всех интерфейсах, включая неактивные.  

7. **Какую информацию можно получить, используя утилиту ping? Приведите примеры с использованием различных опций.**  
   `ping` используется для проверки доступности узлов и времени отклика.  
   - `ping 192.168.1.1` — проверка доступности по IP.  
   - `ping -c 5 ya.ru` — отправка фиксированного числа пакетов.  
   - `ping -i 2 host` — изменение интервала между запросами.  
