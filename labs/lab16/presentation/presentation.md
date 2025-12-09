---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Fail2ban — базовая защита от brute-force атак
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 2025

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

# Цели работы

## Основная цель

Получить навыки установки, настройки и проверки работы Fail2ban для защиты сервисов от атак типа «brute force».

# Выполнение

## Установка и запуск

![Установка и запуск Fail2ban](Screenshot_1.png){width=80%}

## Анализ логов

![Первичный журнал Fail2ban](Screenshot_2.png){width=80%}

## Локальный конфигурационный файл

![Конфигурация SSH](Screenshot_3.png){width=80%}

## Создание и запуск тюрем (jails)

![Запуск SSH-тюрем](Screenshot_4.png){width=80%}

## Активация Apache-тюрем

![HTTP-тюрьмы](Screenshot_5.png){width=80%}

## Проверка логов

![Запуск тюрем Apache](Screenshot_6.png){width=80%}

## Активация тюрем

![Почтовые тюрьмы](Screenshot_7.png){width=80%}

## Проверка запуска почтовых тюрем

![Работа тюрем почтовых сервисов](Screenshot_8.png){width=80%}

## Список активных тюрем

![Статус SSH-тюрьмы](Screenshot_9.png){width=80%}

## Умышленный ввод неправильного пароля

![Блокировка IP](Screenshot_10.png){width=80%}

## Удаление адреса из бана

![Разблокировка IP](Screenshot_12.png){width=80%}

## Исключение клиента из блокировки

![ignoreip](Screenshot_11.png){width=80%}

## Повторная проверка после ignoreip

![Нет блокировок](Screenshot_13.png){width=80%}

# Выводы

## Итог

- Fail2ban установлен и настроен.  
- Защита настроена для SSH, HTTP и почтовых сервисов.  
- Реализована проверка блокировок и их снятие.  
- Конфигурация автоматизирована с помощью Vagrant.
