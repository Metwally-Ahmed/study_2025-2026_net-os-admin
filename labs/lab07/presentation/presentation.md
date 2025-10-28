---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка и расширенные возможности firewalld
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 6 октября 2025

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

Получить навыки настройки межсетевого экрана **firewalld** в Linux, включая создание пользовательских служб, переадресацию портов и настройку маскарадинга.

# Выполнение лабораторной работы

## Создание пользовательской службы firewalld

   ![Создание и редактирование ssh-custom.xml](Screenshot_1.png){ #fig:001 width=80% }

## Проверка и активация службы

   ![Добавление пользовательской службы и проверка списка](Screenshot_4.png){ #fig:002 width=80% }

## Перенаправление портов

   ![Настройка переадресации порта 2022 на 22](Screenshot_6.png){ #fig:003 width=80% }

## Проверка подключения по новому порту

   ![SSH-подключение через порт 2022](Screenshot_6.png){ #fig:004 width=80% }

## Настройка Port Forwarding и Masquerading

   ![Проверка и включение ip_forward](Screenshot_8.png){ #fig:005 width=80% }

## Включение маскарадинга

   ![Активация masquerade в зоне public](Screenshot_9.png){ #fig:006 width=80% }

## Проверка доступа в Интернет

   ![Тест соединения после настройки masquerading](Screenshot_9.png){ #fig:007 width=80% }

## Создание структуры конфигурационных файлов

   ![Создание каталогов и скрипта firewall.sh](Screenshot_10.png){ #fig:008 width=80% }

# Контрольные вопросы

# Выводы по проделанной работе

## Итог

Были выполнены:  
- создание пользовательской службы **firewalld**;  
- настройка переадресации портов и маскарадинга;  
- проверка работы межсетевого экрана;  
- автоматизация конфигурации с помощью скрипта **firewall.sh**.  

