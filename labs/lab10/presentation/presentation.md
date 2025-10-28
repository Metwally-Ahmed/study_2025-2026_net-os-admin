---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Расширенные настройки SMTP-сервера
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 21 октября 2025

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

Приобретение практических навыков по конфигурированию SMTP-сервера с поддержкой аутентификации, LMTP и шифрования TLS.

# Выполнение лабораторной работы

## Настройка LMTP в Dovecot

![Настройка LMTP](Screenshot_1.png){ #fig:001 width=80% }

## Проверка работы LMTP

![Результат проверки LMTP](Screenshot_5.png){ #fig:002 width=80% }

## Настройка SMTP-аутентификации

![SMTP-аутентификация в Dovecot](Screenshot_7.png){ #fig:003 width=80% }

## Проверка SMTP-аутентификации

![Успешная SMTP-аутентификация](Screenshot_10.png){ #fig:004 width=80% }

## Настройка SMTP over TLS

![TLS-конфигурация](Screenshot_11.png){ #fig:005 width=80% }

## Проверка SMTP over TLS

![Проверка TLS-соединения](Screenshot_15.png){ #fig:006 width=80% }

# Выводы по проделанной работе

## Вывод

В ходе лабораторной работы выполнена настройка SMTP-сервера с аутентификацией и TLS.  
Интеграция Postfix и Dovecot обеспечила надёжную доставку и безопасность обмена почтой.  
Конфигурация автоматизирована средствами **Vagrant**, что упростило развёртывание.
