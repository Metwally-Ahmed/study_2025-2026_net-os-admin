---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка DHCP-сервера
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

Приобрести практические навыки по установке и конфигурации DHCP-сервера, а также закрепить знания по работе протокола DHCP и динамическому обновлению DNS-зон.

# Выполнение лабораторной работы

## Установка DHCP-сервера

   ![Установка пакета kea](Screenshot_1.png){ #fig:001 width=80% }

## Настройка конфигурации

   ![Фрагмент конфигурации DHCP](Screenshot_2.png){ #fig:002 width=80% }

## Настройка конфигурации

   ![Фрагмент subnet4](Screenshot_3.png){ #fig:003 width=80% }

## Проверка конфигурации

   ![Проверка kea-dhcp4.conf](Screenshot_4.png){ #fig:004 width=80% }

## Настройка DNS-зон

   ![Файл прямой зоны](Screenshot_5.png){ #fig:005 width=80% }

## Настройка DNS-зон

   ![Файл обратной зоны](Screenshot_6.png){ #fig:006 width=80% }

## Проверка доступности DHCP

   ![Проверка ping dhcp.ahmedfarg.net](Screenshot_7.png){ #fig:007 width=80% }

## Настройка файрвола и SELinux

   ![firewalld и restorecon](Screenshot_8.png){ #fig:008 width=80% }

## Анализ работы DHCP-сервера

   ![Скрипт маршрутизации на клиенте](Screenshot_9.png){ #fig:009 width=80% }

## Анализ работы DHCP-сервера

   ![Сетевые интерфейсы клиента](Screenshot_10.png){ #fig:010 width=80% }

## Анализ работы DHCP-сервера

   ![Выданные адреса в leases4.csv](Screenshot_11.png){ #fig:011 width=80% }

## Настройка обновления DNS-зоны

   ![named.conf с update-policy](Screenshot_12.png){ #fig:012 width=80% }

## Настройка обновления DNS-зоны

   ![Файл tsig-keys.json](Screenshot_13.png){ #fig:013 width=80% }

## Настройка обновления DNS-зоны

   ![kea-dhcp-ddns.conf](Screenshot_14.png){ #fig:014 width=80% }

## Настройка обновления DNS-зоны

   ![Запуск kea-dhcp-ddns](Screenshot_15.png){ #fig:015 width=80% }

## Настройка обновления DNS-зоны

   ![Изменения в kea-dhcp4.conf](Screenshot_16.png){ #fig:016 width=80% }

## Настройка обновления DNS-зоны

   ![Перезапуск kea-dhcp4](Screenshot_17.png){ #fig:017 width=80% }

## Проверка обновлений зоны

   ![dig client.ahmedfarg.net](Screenshot_18.png){ #fig:018 width=80% }

## Подготовка окружения Vagrant

   ![Скрипт dhcp.sh](Screenshot_19.png){ #fig:019 width=80% }

# Выводы по проделанной работе

## Вывод

В ходе работы был установлен и сконфигурирован DHCP-сервер на базе **Kea**, настроено динамическое обновление DNS-зон через Bind9, проверена корректность автоматической регистрации клиентов, а также подготовлены скрипты для автоматизации в Vagrant.
