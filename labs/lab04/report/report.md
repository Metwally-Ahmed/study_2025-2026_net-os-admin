---
## Front matter
title: "Отчёт по лабораторной работе 4"
subtitle: "Базовая настройка HTTP-сервера Apache"
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

Приобретение практических навыков по установке и базовому конфигурированию HTTP-сервера Apache.

# Выполнение

## Установка HTTP-сервера

1. На виртуальной машине `server` просмотрен список доступных групп пакетов.  
   Команда **LANG=C yum grouplist** отобразила установленные и доступные группы, включая *Server with GUI*, *Development Tools* и группу *Basic Web Server*.  

   ![Результат yum grouplist](Screenshot_1.png){ #fig:001 width=80% }

2. С помощью команды **dnf -y groupinstall "Basic Web Server"** установлен стандартный веб-сервер Apache HTTPD и сопутствующие модули (apr, mod_ssl, mod_lua и др.).  

   ![Установка httpd](Screenshot_2.png){ #fig:002 width=80% }

## Базовое конфигурирование HTTP-сервера

1. Настроен межсетевой экран: разрешён доступ к сервису **http** как временно, так и на постоянной основе.  

   ![firewall-cmd настройка](Screenshot_3.png){ #fig:003 width=80% }

2. Веб-сервер Apache добавлен в автозагрузку и запущен с помощью команд:  
   - **systemctl enable httpd**  
   - **systemctl start httpd**

   Проверка логов показала успешный запуск службы.

## Анализ работы HTTP-сервера

1. В браузере на клиентской машине при обращении по адресу `192.168.1.1` отображается тестовая страница Apache, что подтверждает корректную работу сервера.  

   ![Тестовая страница Apache](Screenshot_4.png){ #fig:004 width=80% }

2. На сервере просмотр логов показал:  
   - в **/var/log/httpd/error_log** – отсутствие критических ошибок;  
   - в **/var/log/httpd/access_log** – корректную обработку запросов от клиента (код ответа 200).  

   ![Просмотр логов httpd](Screenshot_5.png){ #fig:005 width=80% }

## Настройка виртуального хостинга для HTTP-сервера

1. Для внесения изменений в DNS-зоны работа службы `named` была остановлена.  

   ![Остановка службы named](Screenshot_6.png){ #fig:006 width=80% }

2. В файл прямой зоны `/var/named/master/fz/ahmedfarg.net` добавлена A-запись для узла **www**.  

   ![Файл прямой зоны](Screenshot_7.png){ #fig:007 width=80% }

3. В файл обратной зоны `/var/named/master/rz/192.168.1` добавлена PTR-запись для домена **www.ahmedfarg.net**.  

   ![Файл обратной зоны](Screenshot_8.png){ #fig:008 width=80% }

4. После внесённых изменений служба `named` была запущена повторно.  

5. В каталоге `/etc/httpd/conf.d/` созданы конфигурационные файлы для виртуальных хостов:  
   - `server.ahmedfarg.net.conf`  
   - `www.ahmedfarg.net.conf`  

   ![Конфигурация www.ahmedfarg.net](Screenshot_9.png){ #fig:009 width=80% }  
   ![Конфигурация server.ahmedfarg.net](Screenshot_10.png){ #fig:010 width=80% }

6. В каталоге `/var/www/html/` созданы директории и тестовые страницы:  
   - `/var/www/html/server.ahmedfarg.net/index.html`  
   - `/var/www/html/www.ahmedfarg.net/index.html`  

   ![Создание каталогов и файлов](Screenshot_11.png){ #fig:011 width=80% }

7. После внесённых изменений HTTP-сервер был перезапущен. Статус службы подтвердил активное состояние.  

   ![Статус Apache](Screenshot_12.png){ #fig:012 width=80% }

8. Проверка работы в браузере:  
   - При обращении к **http://server.ahmedfarg.net/** отобразилась тестовая страница с сообщением *Welcome to the server.ahmedfarg.net server.*  
   - При обращении к **http://www.ahmedfarg.net/** отобразилась страница *Welcome to www.ahmedfarg.net*.  

   ![Тестовая страница server.ahmedfarg.net](Screenshot_13.png){ #fig:013 width=80% }  
   ![Тестовая страница www.ahmedfarg.net](Screenshot_14.png){ #fig:014 width=80% }

## Внесение изменений в настройки внутреннего окружения

1. Конфигурационные файлы веб-сервера и содержимое директорий скопированы в каталог `/vagrant/provision/server/http/`.  

   ![Копирование конфигурации в Vagrant](Screenshot_15.png){ #fig:015 width=80% }

2. Создан скрипт **http.sh** для автоматической настройки веб-сервера. В нём прописаны шаги:  
   - установка группы пакетов *Basic Web Server*  
   - копирование конфигурации  
   - настройка прав доступа и SELinux-контекста  
   - настройка правил файрвола  
   - запуск и включение `httpd`  

# Заключение

HTTP-сервер Apache был успешно установлен и настроен.  
Реализован виртуальный хостинг для доменных имён **server.ahmedfarg.net** и **www.ahmedfarg.net**, для которых были внесены изменения в конфигурацию DNS-прямой и обратной зон.  
Созданы отдельные конфигурационные файлы виртуальных хостов и тестовые веб-страницы.  
Работоспособность веб-сервера подтверждена при обращении к доменам через браузер.  
Дополнительно выполнена автоматизация конфигурации с помощью скрипта *http.sh* и Vagrant.

# Контрольные вопросы

1. **Через какой порт по умолчанию работает Apache?**  
   По умолчанию Apache использует порт **80** для HTTP и порт **443** для HTTPS.  

2. **Под каким пользователем запускается Apache и к какой группе относится этот пользователь?**  
   В дистрибутивах семейства RHEL (включая Rocky Linux) Apache работает от имени пользователя **apache**, который относится к одноимённой группе **apache**.  

3. **Где располагаются лог-файлы веб-сервера? Что можно по ним отслеживать?**  
   Логи Apache находятся в каталоге **/var/log/httpd/**.  
   - *error_log* — фиксирует ошибки работы веб-сервера.  
   - *access_log* — журналирует все запросы клиентов.  
   По этим файлам можно отслеживать ошибки конфигурации, попытки несанкционированного доступа, статистику обращений и состояние работы сервера.  

4. **Где по умолчанию содержится контент веб-серверов?**  
   По умолчанию веб-контент хранится в каталоге **/var/www/html/**.  

5. **Каким образом реализуется виртуальный хостинг? Что он даёт?**  
   Виртуальный хостинг настраивается через отдельные конфигурационные файлы в каталоге **/etc/httpd/conf.d/** с использованием директивы `<VirtualHost>`.  
   Он позволяет обслуживать несколько веб-сайтов на одном сервере с разными доменными именами и директориями, экономя ресурсы и упрощая администрирование.
