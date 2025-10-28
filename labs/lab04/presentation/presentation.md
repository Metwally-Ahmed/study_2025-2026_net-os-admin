---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Базовая настройка HTTP-сервера Apache
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 21 сентября 2025

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

Приобретение практических навыков по установке и базовому конфигурированию HTTP-сервера Apache и настройке виртуального хостинга.

# Выполнение лабораторной работы

## Установка HTTP-сервера

![Результат yum grouplist](Screenshot_1.png){ #fig:001 width=70% }

## Установка HTTP-сервера

![Установка httpd](Screenshot_2.png){ #fig:002 width=70% }

## Базовое конфигурирование

![firewall-cmd настройка](Screenshot_3.png){ #fig:003 width=70% }

## Проверка запуска

![Тестовая страница Apache](Screenshot_4.png){ #fig:004 width=70% }

## Проверка запуска

![Просмотр логов httpd](Screenshot_5.png){ #fig:005 width=70% }

## Настройка виртуального хостинга

![Остановка службы named](Screenshot_6.png){ #fig:006 width=70% }

## Настройка виртуального хостинга

![Файл прямой зоны](Screenshot_7.png){ #fig:007 width=70% }

## Настройка виртуального хостинга

![Файл обратной зоны](Screenshot_8.png){ #fig:008 width=70% }

## Конфигурация виртуальных хостов

![Конфигурация www.ahmedfarg.net](Screenshot_9.png){ #fig:009 width=70% }

## Конфигурация виртуальных хостов

![Конфигурация server.ahmedfarg.net](Screenshot_10.png){ #fig:010 width=70% }

## Тестовые страницы

![Создание каталогов и файлов](Screenshot_11.png){ #fig:011 width=70% }

## Тестовые страницы

![Статус Apache](Screenshot_12.png){ #fig:012 width=70% }

## Проверка доступа

![Тестовая страница server.ahmedfarg.net](Screenshot_13.png){ #fig:013 width=70% }

## Проверка доступа

![Тестовая страница www.ahmedfarg.net](Screenshot_14.png){ #fig:014 width=70% }

## Автоматизация конфигурации

![Копирование конфигурации в Vagrant](Screenshot_15.png){ #fig:015 width=70% }

# Выводы по проделанной работе

## Вывод

HTTP-сервер Apache был успешно установлен и сконфигурирован.  
Настроен виртуальный хостинг для доменных имён **server.ahmedfarg.net** и **www.ahmedfarg.net**, созданы тестовые страницы и проверена доступность в браузере.  
Конфигурация автоматизирована с помощью скрипта *http.sh* и Vagrant.
