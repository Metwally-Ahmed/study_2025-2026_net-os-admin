---
## Front matter
lang: ru-RU
title: Администрирование сетевых подсистем
subtitle: Настройка сервера NFS
author:
  - Метвалли Ахмед Фарг Набеех
institute:
  - Российский университет дружбы народов, Москва, Россия
date: 04 ноября 2025

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

# Цель работы

## Цель лабораторной работы

Получить практический опыт настройки сервера NFS и организации удалённого доступа к файловым ресурсам.

## Установка и подготовка каталога

![Установка nfs-utils](Screenshot_1.png){ width=80% }

## Экспорт каталога через NFS

![Содержимое /etc/exports](Screenshot_2.png){ width=80% }

## Запуск сервиса и firewall

![Запуск сервиса и настройка firewall](Screenshot_3.png){ width=80% }

## Проверка доступности ресурса

![Успешное подключение](Screenshot_4.png){ width=80% }

## Разрешение необходимых служб

![Добавление mountd и rpc-bind](Screenshot_5.png){ width=80% }

## Подключение каталога

![Монтирование каталога /srv/nfs](Screenshot_7.png){ width=80% }

## Автомонтирование при загрузке

![fstab NFS-запись](Screenshot_8.png){ width=80% }

## Экспорт дополнительного каталога

![Экспорт каталога](Screenshot_11.png){ width=80% }

## Подключение личного каталога

![Создание каталога common](Screenshot_14.png){ width=80% }

## Создание файлов с клиента

![Создание файла на клиенте](Screenshot_18.png){ width=80% }


# Итоги

## Вывод

- Развернут и настроен сервер NFS
- Настроена работа через firewall и SELinux
- Реализовано автомонтирование через `/etc/fstab`
- Добавлены пользовательские каталоги и автоматизация через скрипты
