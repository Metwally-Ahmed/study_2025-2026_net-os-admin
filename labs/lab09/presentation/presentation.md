---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка POP3/IMAP-сервера (Dovecot)
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 18 октября 2025

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
toc-title: Содержание
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цели и задачи работы

## Цель лабораторной работы

Приобретение практических навыков по установке и конфигурированию почтового сервера **Dovecot** для работы с протоколами **POP3** и **IMAP**.

# Выполнение лабораторной работы

## Установка Dovecot

   ![Установка пакетов Dovecot и Telnet](Screenshot_1.png){ #fig:001 width=80% }

## Конфигурация dovecot.conf

   ![Настройка разрешённых протоколов IMAP и POP3](Screenshot_2.png){ #fig:002 width=80% }

## Настройка аутентификации

   ![Параметр auth_mechanisms = plain](Screenshot_3.png){ #fig:003 width=80% }

## Конфигурация системных баз пользователей

   ![Настройки passdb и userdb](Screenshot_4.png){ #fig:004 width=80% }

## Настройка каталога Maildir

   ![Путь к почтовым ящикам Maildir](Screenshot_5.png){ #fig:005 width=80% }

## Настройка межсетевого экрана

   ![Добавление служб POP3 и IMAP](Screenshot_6.png){ #fig:006 width=80% }

## Проверка работы Dovecot

   ![Мониторинг службы maillog](Screenshot_7.png){ #fig:007 width=80% }

## Настройка почтового клиента

   ![Конфигурация клиента Evolution](Screenshot_8.png){ #fig:008 width=80% }

## Отправка и получение писем

   ![Отправка и получение тестового письма](Screenshot_9.png){ #fig:009 width=80% }

## Проверка POP3 через Telnet

   ![Соединение и работа по POP3-протоколу](Screenshot_13.png){ #fig:010 width=80% }

# Выводы по проделанной работе

## Вывод

В ходе работы был установлен и настроен почтовый сервер **Dovecot** с поддержкой протоколов **IMAP** и **POP3**.  
Для обработки исходящей корреспонденции использован **Postfix**.  
Работа сервиса успешно проверена через клиент **Evolution** и консольные команды **telnet** и **doveadm**.  
Конфигурация сохранена в систему **Vagrant**, что позволило автоматизировать установку и развёртывание сервера.
