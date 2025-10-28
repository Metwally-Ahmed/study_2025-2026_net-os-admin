---
## Front matter
title: "Отчёт по лабораторной работе 7"
subtitle: "Расширенные настройки межсетевого экрана"
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

Получить навыки настройки межсетевого экрана в Linux в части переадресации портов и настройки Masquerading.

# Выполнение

## Создание пользовательской службы firewalld

1. На виртуальной машине `server` создана пользовательская служба для **firewalld**, основанная на стандартной службе `ssh`.

2. На основе системного файла `/usr/lib/firewalld/services/ssh.xml` был создан новый файл **ssh-custom.xml** в каталоге `/etc/firewalld/services/`.

   ![Создание файла ssh-custom.xml и просмотр содержимого](Screenshot_2.png){ #fig:001 width=80% }

3. В исходном файле заданы основные элементы XML-описания службы:  
   - `<?xml version="1.0" encoding="utf-8"?>` — объявление версии и кодировки XML;  
   - `<service>` — корневой элемент, описывающий сетевую службу;  
   - `<short>` — краткое имя службы, отображаемое в списках firewalld;  
   - `<description>` — текстовое описание назначения службы и её особенностей;  
   - `<port protocol="tcp" port="22"/>` — определение используемого сетевого порта и протокола.  

   После редактирования порт был изменён с **22** на **2022**, а в описании указано, что это модифицированная служба.

   ![Редактирование файла ssh-custom.xml](Screenshot_1.png){ #fig:002 width=80% }

4. Для проверки доступных служб firewalld выполнена команда:

   `firewall-cmd --get-services`

   В списке отображаются все предустановленные службы, однако созданная служба `ssh-custom` на этом этапе ещё отсутствует.

   ![Список служб firewalld до перезагрузки](Screenshot_3.png){ #fig:003 width=80% }

5. После перезагрузки конфигурации firewalld командами:

   `firewall-cmd --reload`  
   `firewall-cmd --get-services`

   служба `ssh-custom` появилась в списке доступных.

   ![Служба ssh-custom после перезагрузки firewalld](Screenshot_4.png){ #fig:004 width=80% }

6. Далее выполнено добавление пользовательской службы в список активных:

   `firewall-cmd --add-service=ssh-custom`  
   `firewall-cmd --add-service=ssh-custom --permanent`  
   `firewall-cmd --reload`

   После этого служба **ssh-custom** успешно активирована и отображается в списке активных служб.

   ![Добавление и активация пользовательской службы](Screenshot_5.png){ #fig:005 width=80% }

# Выполнение

## Перенаправление портов

1. На сервере выполнена настройка переадресации входящих подключений с порта **2022** на порт **22** с помощью команды:

   `firewall-cmd --add-forward-port=port=2022:proto=tcp:toport=22`

2. На клиентской машине выполнено подключение к серверу по SSH через порт **2022**.  
   Подключение прошло успешно, что подтверждает корректную работу правила переадресации.

   ![Подключение к серверу по порту 2022](Screenshot_6.png){ #fig:006 width=80% }

## Настройка Port Forwarding и Masquerading

1. На сервере проверено текущее состояние параметра перенаправления IPv4-пакетов.  
   По выводу команды видно, что параметр `net.ipv4.ip_forward` установлен в значение **0**, то есть переадресация отключена.

   ![Проверка параметра ip_forward](Screenshot_7.png){ #fig:007 width=80% }

2. Для включения пересылки IPv4-пакетов создан файл `/etc/sysctl.d/90-forward.conf` с содержимым `net.ipv4.ip_forward = 1`.  
   После применения параметров командой `sysctl -p /etc/sysctl.d/90-forward.conf` пересылка пакетов успешно активировалась.

   ![Включение пересылки IPv4-пакетов](Screenshot_8.png){ #fig:008 width=80% }

3. Включён маскарадинг в зоне **public** для обеспечения NAT и маршрутизации.  
   После перезагрузки конфигурации (`firewall-cmd --reload`) система подтвердила успешное применение настроек.

4. С клиентской машины выполнена проверка выхода в Интернет — соединение установлено успешно, что подтверждает корректность настроек перенаправления и маскарадинга.

   ![Проверка доступа в Интернет с клиента](Screenshot_9.png){ #fig:009 width=80% }

## Внесение изменений во внутренние настройки виртуальной машины

1. На сервере создана структура каталогов для хранения конфигурационных файлов **FirewallD** и системных параметров:

   - `/vagrant/provision/server/firewall/etc/firewalld/services`  
   - `/vagrant/provision/server/firewall/etc/sysctl.d`

   В соответствующие каталоги скопированы файлы `ssh-custom.xml` и `90-forward.conf`.

2. Затем в каталоге `/vagrant/provision/server/` создан исполняемый файл **firewall.sh**, предназначенный для автоматического применения конфигурации при развертывании виртуальной машины.

   ![Создание структуры каталогов и скрипта firewall.sh](Screenshot_10.png){ #fig:010 width=80% }

# Заключение

Была выполнена настройка пользовательской службы **firewalld** с переназначением порта SSH на 2022.  
Проверена возможность подключения по новому порту, реализовано перенаправление и включён маскарадинг.  
Создана структура каталогов и подготовлены конфигурационные файлы для автоматического применения параметров при развёртывании виртуальной машины.

# Контрольные вопросы

1. **Где хранятся пользовательские файлы firewalld?**  
   В каталоге `/etc/firewalld/services/`, где можно размещать собственные XML-файлы описания служб.  

2. **Какую строку надо включить в пользовательский файл службы, чтобы указать порт TCP 2022?**  
   `<port protocol="tcp" port="2022"/>`  

3. **Какая команда позволяет вам перечислить все службы, доступные в настоящее время на вашем сервере?**  
   `firewall-cmd --get-services`  

4. **В чем разница между трансляцией сетевых адресов (NAT) и маскарадингом (masquerading)?**  
   NAT — общий механизм подмены адресов при прохождении пакетов через маршрутизатор.  
   Маскарадинг — частный случай NAT, при котором исходящий трафик получает динамический внешний IP-адрес интерфейса, через который осуществляется доступ в сеть.  

5. **Какая команда разрешает входящий трафик на порт 4404 и перенаправляет его в службу ssh по IP-адресу 10.0.0.10?**  
   `firewall-cmd --add-forward-port=port=4404:proto=tcp:toaddr=10.0.0.10:toport=22`  

6. **Какая команда используется для включения маскарадинга IP-пакетов для всех пакетов, выходящих в зону public?**  
   `firewall-cmd --zone=public --add-masquerade --permanent`
