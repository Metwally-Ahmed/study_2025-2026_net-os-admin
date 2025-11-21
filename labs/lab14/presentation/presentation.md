---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка файловых служб Samba
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 19 ноября 2025

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

Приобретение навыков настройки доступа групп пользователей к общим ресурсам по протоколу **SMB**.

# Выполнение лабораторной работы

## Установка Samba на сервере

![Установка пакетов Samba](Screenshot_1.png){ #fig:001 width=80% }

## Изменение рабочей группы и добавление ресурса

![Файл smb.conf](Screenshot_2.png){ #fig:002 width=80% }

## Правила firewalld

![Файл samba.xml](Screenshot_4.png){ #fig:004 width=80% }

## Первичный SELinux-контекст

![Первичный контекст SELinux](Screenshot_5.png){ #fig:005 width=80% }


## Проверка групп пользователя

![Информация о пользователе](Screenshot_6.png){ #fig:006 width=80% }

## smbpasswd -a

![Добавление SMB-пользователя](Screenshot_7.png){ #fig:007 width=80% }

## Установка необходимых пакетов

![Установка пакетов на клиенте](Screenshot_9.png){ #fig:009 width=80% }

## Правила firewalld для клиента

![Файл samba-client.xml](Screenshot_8.png){ #fig:008 width=80% }

## smb.conf

![Файл smb.conf на клиенте](Screenshot_10.png){ #fig:010 width=80% }

## Анонимный доступ

![Анонимный доступ smbclient](Screenshot_11.png){ #fig:011 width=80% }

## Доступ под пользователем ahmedfarg

## Подключение ресурса

![Ручное монтирование](Screenshot_12.png){ #fig:012 width=80% }

## Создание файла

![Создание файла на общем ресурсе](Screenshot_13.png){ #fig:013 width=80% }

## Файл учётных данных

![Файл учетных данных smbusers](Screenshot_14.png){ #fig:014 width=80% }

## fstab

![Настройка fstab](Screenshot_15.png){ #fig:015 width=80% }

## Проверка

![Проверка монтирования через fstab](Screenshot_16.png){ #fig:016 width=80% }

# Выводы

## Итоги работы

- Настроены сервер и клиент Samba  
- Обеспечен общий доступ к каталогу  
- Настроены SELinux-контексты и межсетевой экран  
- Реализовано автоматическое монтирование ресурса  
- Созданы сценарии автоматизации настройки


