---
## Front matter
title: "Отчёт по лабораторной работе 10"
subtitle: "Расширенные настройки SMTP-сервера"
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

Приобретение практических навыков по конфигурированию SMTP-сервера в части настройки аутентификации.

# Выполнение

## Настройка LMTP в Dovecot

1. На виртуальной машине `server` выполнен вход под собственным пользователем и выполнен переход в режим суперпользователя с помощью команды `sudo -i`.

2. В дополнительном терминале запущен мониторинг почтовой службы командой `tail -f /var/log/maillog`.

3. В файле `/etc/dovecot/dovecot.conf` добавлен протокол **LMTP** для поддержки локальной доставки почты.  
   В результате параметр **protocols** имеет вид:  
   `protocols = imap pop3 lmtp`

   ![Добавление LMTP в конфигурацию Dovecot](Screenshot_1.png){ #fig:001 width=80% }

4. Для взаимодействия Dovecot с Postfix изменён блок `service lmtp` в файле `/etc/dovecot/conf.d/10-master.conf`.  
   Новый фрагмент конфигурации задаёт путь к Unix-сокету, права доступа и владельца:

![Настройка сервиса lmtp в 10-master.conf](Screenshot_2.png){ #fig:002 width=80% }

5. В файле `/etc/dovecot/conf.d/10-auth.conf` определён формат имени пользователя для аутентификации без доменной части:  
`auth_username_format = %Ln`

![Формат имени пользователя для аутентификации](Screenshot_3.png){ #fig:003 width=80% }

6. В Postfix переопределена доставка сообщений через LMTP-сокет Dovecot:  
`postconf -e 'mailbox_transport = lmtp:unix:private/dovecot-lmtp'`

7. После изменения конфигурации службы **Postfix** и **Dovecot** были перезапущены:  
`systemctl restart postfix`  
`systemctl restart dovecot`

8. С клиентской машины отправлено тестовое письмо пользователю:  
`echo . | mail -s "LMTP test" ahmedfarg@ahmedfarg.net`

![Отправка тестового письма через LMTP](Screenshot_4.png){ #fig:004 width=80% }

9. В логе почтовой службы зафиксирована успешная передача письма от Postfix к Dovecot по протоколу LMTP.  
Видно, что сообщение сохранено в почтовом ящике пользователя.

![Фрагмент лога доставки письма](Screenshot_5.png){ #fig:005 width=80% }

10. На сервере проверено наличие доставленного письма в каталоге `Maildir`.  
 Сообщение с темой **"LMTP test"** присутствует в списке писем пользователя.

 ![Просмотр почтового ящика пользователя](Screenshot_6.png){ #fig:006 width=80% }

## Настройка SMTP-аутентификации

1. В файле `/etc/dovecot/conf.d/10-master.conf` определена служба аутентификации пользователей **auth**.  
   Этот блок конфигурации обеспечивает взаимодействие между Postfix и Dovecot через Unix-сокеты.

   **Пояснение:**  
   `service auth` — начало определения службы аутентификации Dovecot.  
   `unix_listener /var/spool/postfix/private/auth` — создаёт Unix-сокет для связи Postfix с Dovecot.  
   `group = postfix` — задаёт группу владельца сокета.  
   `user = postfix` — определяет пользователя-владельца сокета.  
   `mode = 0660` — разрешает чтение и запись для владельца и группы.  
   `unix_listener auth-userdb` — внутренний сокет для взаимодействия Dovecot с базой данных аутентификации.  
   `mode = 0600` — разрешение только для владельца.  
   `user = dovecot` — владелец внутреннего сокета — сервис Dovecot.

   ![Конфигурация сервиса аутентификации Dovecot](Screenshot_7.png){ #fig:007 width=80% }

2. Для Postfix задан тип аутентификации SASL и путь к Unix-сокету, через который осуществляется проверка логинов.  
   Указано использование Dovecot в качестве механизма SASL и путь `private/auth`.

   ![Настройка SASL в Postfix](Screenshot_8.png){ #fig:008 width=80% }

3. В Postfix настроены ограничения для приёма почты, предотвращающие использование сервера как открытого релея.  
   Опция `smtpd_recipient_restrictions` определяет последовательность проверок:  
   - `reject_unknown_recipient_domain` — отклоняет письма к несуществующим доменам.  
   - `permit_mynetworks` — разрешает приём писем от доверенных хостов.  
   - `reject_non_fqdn_recipient` — блокирует адреса без полного доменного имени.  
   - `reject_unauth_destination` — запрещает пересылку на внешние домены.  
   - `reject_unverified_recipient` — проверяет существование получателя.  
   - `permit` — разрешает приём после успешного прохождения проверок.

4. Для ограничения доступа к серверу почтовые запросы разрешены только с локальной сети.  
   В параметре `mynetworks` задан диапазон `127.0.0.0/8`.

5. В файле `/etc/postfix/master.cf` активирована возможность SMTP-аутентификации на порту 25.  
   В секции `smtp inet` добавлены параметры включения SASL-аутентификации и ограничений для проверки получателей.

   ![Настройки аутентификации в master.cf](Screenshot_9.png){ #fig:009 width=80% }

6. После внесённых изменений службы **Postfix** и **Dovecot** были перезапущены.

7. На клиенте установлена утилита **telnet** и сгенерирована строка для аутентификации в формате Base64.  
   Строка создаётся из логина и пароля пользователя в виде `username username password`.

8. С клиента выполнено подключение к SMTP-серверу по порту 25.  
   После ввода команды `AUTH PLAIN <строка>` сервер ответил сообщением  
   **235 2.7.0 Authentication successful**, что подтверждает успешную SMTP-аутентификацию.

   ![Успешная SMTP-аутентификация через Telnet](Screenshot_10.png){ #fig:010 width=80% }

## Настройка SMTP over TLS

1. На сервере настроен TLS с использованием временного сертификата Dovecot.  
   Файлы сертификата и ключа скопированы из каталога `/etc/pki/dovecot` в `/etc/pki/tls/` в соответствующие подкаталоги, чтобы избежать проблем с SELinux.  
   Далее в конфигурации Postfix указаны пути к файлам сертификата, ключа и каталогу для хранения TLS-сессий, а также определён уровень безопасности для шифрованных соединений.

   ![Настройка параметров TLS в Postfix](Screenshot_11.png){ #fig:011 width=80% }

2. Для запуска SMTP-сервера на порту 587 в файле `/etc/postfix/master.cf` добавлена секция **submission**.  
   Она обеспечивает поддержку TLS и SASL-аутентификации, а также задаёт ограничения для проверки получателей.  
   Настройка секции выглядит следующим образом:  
   - `submission inet n - n - - smtpd` — активация службы SMTP Submission.  
   - `-o smtpd_tls_security_level=encrypt` — обязательное шифрование соединений.  
   - `-o smtpd_sasl_auth_enable=yes` — включение аутентификации.  
   - `-o smtpd_recipient_restrictions=...` — правила приёма сообщений.

   ![Добавление секции submission в master.cf](Screenshot_12.png){ #fig:012 width=80% }

3. В межсетевом экране разрешена работа службы **smtp-submission**.  
   Это обеспечивает доступ к порту 587 как временно, так и на постоянной основе после перезапуска системы.

4. После внесения изменений служба **Postfix** была перезапущена для применения новых параметров конфигурации.

5. С клиентской машины выполнено подключение к SMTP-серверу через порт 587 с использованием `openssl`.  
   Проверка показала корректную работу TLS-соединения и успешную SMTP-аутентификацию пользователя.  
   Сервер ответил сообщением **235 2.7.0 Authentication successful**, что подтверждает защищённое подключение и успешную проверку учётных данных.

   ![Проверка TLS-подключения и успешная аутентификация](Screenshot_13.png){ #fig:013 width=80% }

6. В почтовом клиенте **Evolution** внесены изменения в настройки отправки почты.  
   Для SMTP-сервера указан порт **587**, метод шифрования **STARTTLS after connecting** и тип аутентификации **PLAIN**.  
   Пользователь вводит логин для проверки подлинности при отправке писем.

   ![Настройка SMTP с STARTTLS в почтовом клиенте](Screenshot_14.png){ #fig:014 width=80% }

7. Отправка почтовых сообщений с клиента успешно проверена.  
   Входящие письма отображаются в папке **Inbox**, что подтверждает корректную работу SMTP over TLS и шифрованную доставку сообщений.

   ![Проверка доставки почты через TLS](Screenshot_15.png){ #fig:015 width=80% }

# Выполнение

## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. На виртуальной машине `server` выполнен переход в каталог `/vagrant/provision/server/` для внесения изменений в настройки окружения.  
   В соответствующие подкаталоги помещены конфигурационные файлы **Dovecot** и **Postfix**.  

   ![Копирование конфигурационных файлов Dovecot и Postfix](Screenshot_16.png){ #fig:016 width=80% }

2. В файл `/vagrant/provision/server/mail.sh` внесены изменения для расширенной конфигурации SMTP-сервера.  

   ![Обновлённый скрипт конфигурации сервера mail.sh](Screenshot_17.png){ #fig:017 width=80% }

3. В файл `/vagrant/provision/client/mail.sh` добавлена установка пакета `telnet` и почтового клиента `evolution`, необходимых для тестирования и проверки работы SMTP-сервера.  
 
   ![Изменённый скрипт клиента mail.sh](Screenshot_18.png){ #fig:018 width=80% }

# Заключение

SMTP-сервер был успешно настроен для работы с безопасной аутентификацией и шифрованием с использованием TLS.  
Dovecot и Postfix интегрированы через LMTP и SASL, что обеспечивает корректную доставку и защищённую передачу почты.  

# Контрольные вопросы

1. **Приведите пример задания формата аутентификации пользователя в Dovecot в форме логина с указанием домена.**  
   Формат указывается в параметре `auth_username_format`, например:  
   `auth_username_format = %n@%d`  
   где `%n` — имя пользователя, а `%d` — домен (например, `user@example.com`).

2. **Какие функции выполняет почтовый Relay-сервер?**  
   Relay-сервер (посредник) принимает почту от одного почтового узла и пересылает её другому серверу, выступая промежуточным звеном между отправителем и конечным получателем.  
   Основные функции — маршрутизация сообщений, передача писем между доменами и поддержка внешних соединений при отправке почты.

3. **Какие угрозы безопасности могут возникнуть в случае настройки почтового сервера как Relay-сервера?**  
   Если почтовый сервер настроен как открытый релей (Open Relay), он может быть использован злоумышленниками для рассылки спама и фишинговых писем.  
   Это приведёт к блокировке IP-адреса сервера, попаданию в «чёрные списки» и снижению доверия к почтовому домену.  
   Также возможно увеличение нагрузки и утечка конфиденциальных данных при отсутствии аутентификации и шифрования.
