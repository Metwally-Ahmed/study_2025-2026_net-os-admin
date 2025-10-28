---
## Front matter
title: "Отчёт по лабораторной работе 9"
subtitle: "Настройка POP3/IMAP сервера"
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

Приобретение практических навыков по установке и простейшему конфигурированию POP3/IMAP-сервера.

# Выполнение

## Установка и настройка Dovecot

1. На виртуальной машине **server** выполнен вход под пользователем и произведён переход в режим суперпользователя с помощью команды **sudo -i**.

2. Установлены необходимые пакеты для работы почтового сервера: **dovecot** и **telnet**.

3. В файле * /etc/dovecot/dovecot.conf * разрешены почтовые протоколы **IMAP** и **POP3**.  
   Убедились, что строка конфигурации имеет вид: *protocols = imap pop3*.  

   ![Конфигурация dovecot.conf](Screenshot_1.png){ #fig:001 width=80% }

4. В файле * /etc/dovecot/conf.d/10-auth.conf * проверен метод аутентификации.  
   Убедились, что указано: *auth_mechanisms = plain*.  

   ![Параметр аутентификации в 10-auth.conf](Screenshot_2.png){ #fig:002 width=80% }

5. В файле * /etc/dovecot/conf.d/auth-system.conf.ext * настроено использование системных источников пользователей:  
   *passdb { driver = pam }*  
   *userdb { driver = passwd }*  

   ![Настройка passdb и userdb](Screenshot_3.png){ #fig:003 width=80% }

6. В файле * /etc/dovecot/conf.d/10-mail.conf * указано месторасположение почтовых ящиков:  
   *mail_location = maildir:~/Maildir*  

   ![Путь к почтовым ящикам Maildir](Screenshot_4.png){ #fig:004 width=80% }

7. Для корректной доставки почты в **Postfix** задан каталог: *home_mailbox = Maildir/*.

8. Настроен межсетевой экран — добавлены и активированы службы протоколов **POP3** и **IMAP**.  
   Проверено, что службы **pop3**, **pop3s**, **imap** и **imaps** присутствуют в списке разрешённых.  

   ![Настройка межсетевого экрана](Screenshot_5.png){ #fig:005 width=80% }

9. Восстановлен контекст безопасности **SELinux** и выполнен перезапуск почтовых служб **Postfix** и **Dovecot**.  
   Сервис **Dovecot** добавлен в автозагрузку и успешно запущен.

## Проверка работы Dovecot

1. На дополнительном терминале виртуальной машины **server** запущен мониторинг работы почтовой службы с помощью просмотра журнала *maillog*.  
   В логах отображается запуск сервисов **Postfix** и **Dovecot**, что подтверждает корректную работу почтового сервера.  

   ![Журнал maillog при запуске служб](Screenshot_6.png){ #fig:006 width=80% }

2. На виртуальной машине **client** установлен почтовый клиент **Evolution** и произведена настройка учётной записи.  
   В поле имени указано **ahmedfarg**, адрес электронной почты — **ahmedfarg@ahmedfarg.net**.  

   ![Настройка учётной записи в Evolution](Screenshot_7.png){ #fig:007 width=80% }

3. В параметрах приёма почты выбран тип сервера **IMAP**, указано имя сервера **mail.ahmedfarg.net**, порт **143**, метод шифрования — **STARTTLS**, аутентификация — **Password**.  

   ![Настройка приёма почты (IMAP)](Screenshot_8.png){ #fig:008 width=80% }

4. Для исходящей почты настроен сервер **SMTP** с адресом **mail.ahmedfarg.net**, портом **25**, без шифрования и без аутентификации.  

   ![Настройка отправки почты (SMTP)](Screenshot_9.png){ #fig:009 width=80% }

5. Из почтового клиента отправлено тестовое письмо на собственный адрес.  

   ![Отправка тестового письма](Screenshot_10.png){ #fig:010 width=80% }

6. В почтовом клиенте получено письмо с темой **test**, что подтверждает успешную доставку через сервер **Dovecot/Postfix**.  

   ![Получение тестового письма](Screenshot_11.png){ #fig:011 width=80% }

7. В процессе передачи сообщений наблюдались системные логи почтового сервера.  
   Видно, что письмо было принято, передано через **Postfix**, сохранено в **Maildir** и обработано службой **Dovecot**.  

   ![Записи maillog при передаче письма](Screenshot_12.png){ #fig:012 width=80% }

8. Проверка соединения по протоколу **POP3** выполнена через **telnet**.  
   Пользователь успешно вошёл, получил список сообщений, прочитал содержимое письма и удалил одно из них.  
   Это подтверждает корректную работу сервера при подключении по POP3.  

   ![Проверка POP3 через Telnet](Screenshot_13.png){ #fig:013 width=80% }

## Внесение изменений в настройки внутреннего окружения виртуальной машины

1. На виртуальной машине **server** произведён переход в каталог, предназначенный для хранения конфигурационных файлов внутреннего окружения:  
   */vagrant/provision/server/*.

2. Создана структура подкаталогов для размещения файлов почтового сервера **Dovecot**:  
   */vagrant/provision/server/mail/etc/dovecot/conf.d/*.

3. В созданные каталоги скопированы актуальные конфигурационные файлы **Dovecot**:  
   *dovecot.conf*, *10-auth.conf*, *auth-system.conf.ext* и *10-mail.conf*.  
   Данные файлы были перенесены из системного каталога */etc/dovecot/* в директорию *Vagrant-проvision*.  

   ![Копирование конфигурационных файлов Dovecot](Screenshot_14.png){ #fig:014 width=80% }

# Заключение

Почтовый сервер **Dovecot** был успешно установлен и настроен для работы с протоколами **IMAP** и **POP3**, обеспечивающими получение почты пользователями.  
Для обработки исходящей корреспонденции использован **Postfix**.  
Функциональность сервера подтверждена при тестировании через почтовый клиент **Evolution** и при работе с протоколом **POP3** с помощью **telnet**.  
Конфигурация была сохранена и интегрирована во внутреннюю систему развёртывания Vagrant для последующей автоматизации установки.

# Контрольные вопросы

1. **За что отвечает протокол SMTP?**  
   SMTP (Simple Mail Transfer Protocol) — протокол, используемый для передачи электронной почты между почтовыми серверами, а также от клиента к серверу при отправке сообщений.

2. **За что отвечает протокол IMAP?**  
   IMAP (Internet Message Access Protocol) позволяет пользователю получать доступ к сообщениям, хранящимся на сервере, и работать с ними удалённо — читать, сортировать и синхронизировать между разными устройствами.

3. **За что отвечает протокол POP3?**  
   POP3 (Post Office Protocol v3) используется для загрузки сообщений с почтового сервера на локальный компьютер. После загрузки письма обычно удаляются с сервера (если не указано иное).

4. **В чём назначение Dovecot?**  
   Dovecot — это почтовый сервер (IMAP/POP3-сервер), отвечающий за получение и хранение почты пользователей, а также за аутентификацию при доступе к почтовым ящикам.

5. **В каких файлах обычно находятся настройки работы Dovecot? За что отвечает каждый из файлов?**  
   - */etc/dovecot/dovecot.conf* — основной файл конфигурации, где задаются общие параметры и активируемые протоколы.  
   - */etc/dovecot/conf.d/10-auth.conf* — настройки аутентификации пользователей.  
   - */etc/dovecot/conf.d/auth-system.conf.ext* — определение источников данных пользователей (например, PAM, passwd).  
   - */etc/dovecot/conf.d/10-mail.conf* — параметры местоположения почтовых ящиков и формата хранения писем.

6. **В чём назначение Postfix?**  
   Postfix — это агент передачи почты (MTA), который принимает, маршрутизирует и доставляет электронные сообщения по протоколу SMTP.

7. **Какие методы аутентификации пользователей можно использовать в Dovecot и в чём их отличие?**  
   Dovecot поддерживает методы:  
   - *plain* — передача логина и пароля в открытом виде;  
   - *login* — аналогично plain, но с отдельными запросами имени и пароля;  
   - *cram-md5*, *digest-md5* — используют хэширование и не передают пароль напрямую;  
   - *gssapi*, *anonymous* и другие.  
   Основное различие — в уровне безопасности и типе используемого шифрования.

8. **Приведите пример заголовка письма с пояснениями его полей.**  

```
From: user@example.com ← адрес отправителя
To: admin@example.com ← адрес получателя
Subject: Test message ← тема письма
Date: Sat, 11 Oct 2025 13:27:52 +0000 ← дата и время отправки
Message-ID: uniqueid@mailserver ← уникальный идентификатор письма
Content-Type: text/plain; charset=UTF-8 ← тип содержимого письма
```


9. **Приведите примеры использования команд для работы с почтовыми протоколами через терминал.**  
- Подключение к POP3-серверу: *telnet mail.server.net 110*  
- Авторизация:  
  *user имя_пользователя*  
  *pass пароль*  
- Получение списка писем: *list*  
- Чтение письма: *retr 1*  
- Удаление письма: *dele 2*  
- Завершение сеанса: *quit*

10. **Приведите примеры с пояснениями по работе с doveadm.**  
 - *doveadm user '*'* — выводит список всех пользователей Dovecot.  
 - *doveadm mailbox list -u user* — показывает список почтовых папок конкретного пользователя.  
 - *doveadm fetch -u user text mailbox INBOX* — выводит содержимое писем из папки INBOX.  
 - *doveadm log find* — показывает расположение логов Dovecot.  
 Эти команды применяются для диагностики, проверки учётных записей и управления почтовыми данными.
