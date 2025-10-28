---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка DNS-сервера
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 19 сентября 2025

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

Приобрести практические навыки по установке и конфигурации DNS-сервера, а также закрепить теоретические знания о принципах работы системы доменных имён.

# Выполнение лабораторной работы

## Развертывание DNS-сервера

   ![Запрос dig к www.yandex.ru](Screenshot_1.png){ #fig:001 width=80% }

## Анализ конфигурации

   ![Файл resolv.conf и named.conf](Screenshot_2.png){ #fig:002 width=80% }

## Конфигурация кэширующего DNS

   ![Сравнение dig-запросов](Screenshot_3.png){ #fig:003 width=80% }

## Настройка подключения

   ![Настройка подключения через nmcli](Screenshot_4.png){ #fig:004 width=80% }

## Изменения в конфигурации

   ![Изменения в named.conf](Screenshot_5.png){ #fig:005 width=80% }

## Проверка работы сервиса

   ![Проверка работы DNS через lsof](Screenshot_6.png){ #fig:006 width=80% }

## Настройка основного DNS-сервера

   ![Подключение файла зоны ahmedfarg.net](Screenshot_7.png){ #fig:007 width=80% }

## Файл прямой зоны

   ![Файл прямой зоны ahmedfarg.net](Screenshot_8.png){ #fig:008 width=80% }

## Файл обратной зоны

   ![Файл обратной зоны 192.168.1](Screenshot_9.png){ #fig:009 width=80% }

## Настройка SELinux и прав

   ![Настройка прав и SELinux](Screenshot_10.png){ #fig:010 width=80% }

## Проверка работоспособности

   ![Проверка зоны через dig](Screenshot_11.png){ #fig:011 width=80% }
   
## Проверка записей зоны

   ![Проверка зоны через host](Screenshot_12.png){ #fig:012 width=80% }

# Выводы по проделанной работе

## Вывод

В ходе работы был установлен и сконфигурирован DNS-сервер на базе **BIND**. Настроены прямая и обратная зоны, протестирована корректность работы резолвера и кэширующего режима. Создан скрипт для Vagrant, обеспечивший автоматизацию настройки и сокращение времени развертывания.
