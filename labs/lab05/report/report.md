---
## Front matter
title: "Отчёт по лабораторной работе 5"
subtitle: "Расширенная настройка HTTP-сервера Apache"
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

Приобретение практических навыков по расширенному конфигурированию HTTPсервера Apache в части безопасности и возможности использования PHP.

# Выполнение

## Конфигурирование HTTP-сервера для работы через протокол HTTPS

1. На сервере создан каталог для хранения закрытых ключей и сертификатов, а также выполнена генерация самоподписанного сертификата и ключа для домена `www.ahmedfarg.net`:  
   - ключ `www.ahmedfarg.net.key` сохранён в `/etc/ssl/private`,  
   - сертификат `www.ahmedfarg.net.crt` помещён в каталог `/etc/ssl/certs`.  

   ![Генерация ключа и сертификата](Screenshot_1.png){ #fig:001 width=80% }

2. В каталоге `/etc/httpd/conf.d` создан конфигурационный файл `www.ahmedfarg.net.conf`, где определены два виртуальных хоста:  
   - для порта **80** — выполняется перенаправление всех HTTP-запросов на HTTPS,  
   - для порта **443** — включено использование SSL с указанием сертификата и закрытого ключа.  

   ![Конфигурация виртуальных хостов](Screenshot_2.png){ #fig:002 width=80% }

3. После внесения изменений веб-сервер Apache был перезапущен. Его статус показывает успешный запуск и работу на портах 80 и 443.  

   ![Перезапуск Apache и проверка статуса](Screenshot_3.png){ #fig:003 width=80% }

4. На клиентской машине в браузере выполнена проверка доступа к сайту `https://www.ahmedfarg.net`. Отобразилась стартовая страница веб-сервера с сообщением:  

   **Welcome to www.ahmedfarg.net**  

   ![Доступ к сайту через HTTPS](Screenshot_4.png){ #fig:004 width=80% }

5. Содержание сертификата подтверждает корректное заполнение полей:  
   - страна — RU,  
   - регион — Russia,  
   - город — Moscow,  
   - организация и подразделение — `ahmedfarg`,  
   - CN (Common Name) — `www.ahmedfarg.net`,  
   - email — `ahmedfarg@ahmedfarg.net`.  

   ![Информация о сертификате](Screenshot_5.png){ #fig:005 width=80% }

## Конфигурирование HTTP-сервера для работы с PHP

1. На сервер были установлены необходимые пакеты для поддержки PHP.  

2. В каталоге `/var/www/html/www.ahmedfarg.net` файл `index.html` был заменён на `index.php`, содержащий вызов функции `phpinfo()`.  

   ![Файл index.php](Screenshot_6.png){ #fig:006 width=80% }

3. Для корректной работы веб-сервера были изменены права доступа на каталог `/var/www` и восстановлен контекст безопасности SELinux. После этого HTTP-сервер был перезапущен.  

   ![Настройка прав доступа и перезапуск Apache](Screenshot_7.png){ #fig:007 width=80% }

4. На клиентской машине при обращении к сайту `https://www.ahmedfarg.net` отобразилась информация о конфигурации PHP, что подтверждает успешное подключение модуля.  

   ![Проверка работы PHP в браузере](Screenshot_8.png){ #fig:008 width=80% }

## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. Конфигурационные файлы HTTP-сервера и веб-контента были скопированы в каталог `/vagrant/provision/server/http` для дальнейшего использования при автоматическом развёртывании.  

   ![Копирование конфигурационных файлов](Screenshot_9.png){ #fig:009 width=80% }

2. В скрипт `/vagrant/provision/server/http.sh` были внесены изменения:  
   - добавлена установка PHP,  
   - произведена настройка межсетевого экрана с разрешением работы по протоколам HTTP и HTTPS,  
   - реализован запуск службы Apache при старте системы.  

   ![Обновлённый скрипт http.sh](Screenshot_10.png){ #fig:010 width=80% }


# Заключение

HTTP-сервер был успешно сконфигурирован для работы по протоколу HTTPS, сгенерирован самоподписанный SSL-сертификат и настроено автоматическое перенаправление с порта 80 на 443. Далее сервер был дополнительно настроен для обработки PHP-скриптов, что было подтверждено выводом страницы `phpinfo()`. Конфигурация и сертификаты сохранены в каталоге Vagrant для последующего автоматического развёртывания.

# Контрольные вопросы

1. **В чём отличие HTTP от HTTPS?**  
   HTTP передаёт данные в открытом виде, а HTTPS использует SSL/TLS для их шифрования, что обеспечивает конфиденциальность и защищает от перехвата.  

2. **Каким образом обеспечивается безопасность контента веб-сервера при работе через HTTPS?**  
   Безопасность достигается с помощью шифрования данных, проверки подлинности сервера с использованием SSL-сертификата и защиты от атак типа «man-in-the-middle».  

3. **Что такое сертификационный центр? Приведите пример.**  
   Сертификационный центр (CA) — доверенная организация, выпускающая цифровые сертификаты для подтверждения подлинности сайтов и шифрования соединений. Примеры: DigiCert, GlobalSign, Let’s Encrypt.  
