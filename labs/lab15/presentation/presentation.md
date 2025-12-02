---
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка сетевого журналирования (rsyslog)
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 1 декабря 2025

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

Получить практические навыки по настройке сетевого журналирования с использованием rsyslog и интеграции с journald.

# Выполнение лабораторной работы

## Настройка сервера сетевого журнала

   ![Конфигурация netlog-server.conf](Screenshot_1.png){ width=80% }

## Проверка работы сервера

   ![Порты rsyslog и firewall](Screenshot_2.png){ width=80% }

## Настройка клиента сетевого журнала

   ![Конфигурация клиента netlog-client.conf](Screenshot_3.png){ width=80% }

## Просмотр системных журналов

   ![Просмотр журнала через tail](Screenshot_4.png){ width=80% }

## Мониторинг системных сообщений

   ![Графический просмотрщик журналов](Screenshot_5.png){ width=80% }

## Настройка provisioning (сервер)

   ![Provisioning серверной части](Screenshot_6.png){ width=80% }

## Настройка provisioning (клиент)

   ![Provisioning клиентской части](Screenshot_7.png){ width=80% }

# Итоги лабораторной работы

## Вывод

Система сетевого журналирования успешно настроена: сервер принимает сообщения по TCP, клиент перенаправляет системные логи, результаты подтверждены просмотром журналов. Подготовлены provisioning-скрипты, автоматизирующие развёртывание и конфигурацию обеих машин.
