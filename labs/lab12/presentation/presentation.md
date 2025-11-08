---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Синхронизация времени
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 28 октября 2025

## i18n babel
babel-lang: russian
babel-otherlangs: english

## Formatting pdf
toc: false
slide_level: 2
aspectratio: 169
section-titles: true
theme: metropolis
header-includes:
 - \metroset{progressbar=frametitle,sectionpage=progressbar,numbering=fraction}
---

# Цели и задачи работы

## Цель лабораторной работы

Получить навыки по управлению системным временем и настройке синхронизации времени между сервером и клиентом с использованием chrony.

# Выполнение лабораторной работы

## Настройка параметров времени

   ![Результат timedatectl на сервере](Screenshot_1.png){ #fig:001 width=80% }  
   
## Настройка параметров времени

   ![Результат timedatectl на клиенте](Screenshot_2.png){ #fig:002 width=80% }

## Настройка chrony на сервере

   ![Файл chrony.conf на сервере](Screenshot_5.png){ #fig:004 width=80% }

## Настройка chrony на клиенте

   ![Файл chrony.conf на клиенте](Screenshot_6.png){ #fig:005 width=80% }

## Проверка источников синхронизации

   ![Источники времени на сервере](Screenshot_3.png){ #fig:006 width=80% } 

## Проверка источников синхронизации
   
   ![Источники времени на клиенте](Screenshot_7.png){ #fig:007 width=80% }

# Выводы

## Итоги работы

В ходе лабораторной работы был установлен и настроен сервис **chrony**.  
Сервер синхронизируется с внешними NTP-источниками, а клиент — с внутренним сервером.  
Созданы скрипты автоматизации, обеспечивающие единообразие конфигурации и точную синхронизацию времени в сети.
