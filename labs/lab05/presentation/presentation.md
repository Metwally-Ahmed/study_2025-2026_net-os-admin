---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Расширенная настройка HTTP-сервера Apache
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 27 сентября 2025

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

Приобрести практические навыки по расширенному конфигурированию HTTP-сервера Apache в части безопасности и возможности использования PHP.

# Выполнение лабораторной работы

## Конфигурирование HTTPS

   ![Генерация ключа и сертификата](Screenshot_1.png){ #fig:001 width=80% }

## Настройка виртуальных хостов

   ![Конфигурация виртуальных хостов](Screenshot_2.png){ #fig:002 width=80% }

## Проверка запуска Apache

   ![Перезапуск Apache и проверка статуса](Screenshot_3.png){ #fig:003 width=80% }

## Проверка доступа к сайту

   ![Доступ к сайту через HTTPS](Screenshot_4.png){ #fig:004 width=80% }

## Содержимое сертификата

   ![Информация о сертификате](Screenshot_5.png){ #fig:005 width=80% }

## Конфигурация PHP

   ![Файл index.php](Screenshot_6.png){ #fig:006 width=80% }

## Перезапуск Apache и SELinux

   ![Настройка прав доступа и перезапуск Apache](Screenshot_7.png){ #fig:007 width=80% }

## Проверка работы PHP

   ![Проверка работы PHP в браузере](Screenshot_8.png){ #fig:008 width=80% }

## Сохранение конфигурации

   ![Копирование конфигурационных файлов](Screenshot_9.png){ #fig:009 width=80% }

## Обновление скрипта http.sh

   ![Обновлённый скрипт http.sh](Screenshot_10.png){ #fig:010 width=80% }

# Выводы по проделанной работе

## Вывод

HTTP-сервер был успешно сконфигурирован для работы по протоколу HTTPS, сгенерирован самоподписанный SSL-сертификат и настроено перенаправление с порта 80 на 443.  
Также сервер был дополнен поддержкой PHP и проверен вывод информации через `phpinfo()`.  
Конфигурация и сертификаты сохранены в каталоге Vagrant для последующего автоматического развёртывания.
