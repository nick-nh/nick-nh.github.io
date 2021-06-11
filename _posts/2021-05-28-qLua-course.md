---
layout: post
title: "Курс по написанию скриптов для терминала Квик, языку Lua"
author: "nnh"
description: qLua Курс
tags: [lua,qlua,квик,quik,course]
---

Для тех кто хочет изучить язык Lua и как писать скрипты для терминала Квик был записан видео-курс.
Курс состоит из двух частей:
- Язык Lua
- Специфика qLua, методика написания скриптов для терминала Квик.

Курс построен по принципу от простого к сложному.
Полная стоимость курса 20 000 руб. Без части по языку Lua 15 000 руб. Также возможны индивидуальные занятия.

Все видео в разрешении 1920*1080.
<br><br>

Часть по языку Lua построена по книге автора языка [Роберту Иерузалимски: Программирование на языке Lua](https://www.labirint.ru/books/433606/). Поэтому если Вы в состоянии сами изучить язык, то часть по языку Lua можно не брать.
Для каждого занятия по языку есть примеры решенных заданий из главы книги. Задача этой части научить основам языка Lua, принципам написания скриптов.


## Программа курса Lua (10 часов):

### Занятие 1
- Введение. Основы

### Занятие 2
- Типы и значения (Продолжительность 42 мин.)

### Занятие 3
- Выражения (Продолжительность 35 мин.)

### Занятие 4
- Операторы (Продолжительность 56 мин.)

### Занятие 5
- Функции (Продолжительность 45 мин.)

### Занятие 6  (Продолжительность 1 час 19 мин.)
- Еще раз о функциях
- Замыкания
- Неглобальные функции
- Корректные хвостовые вызовы

### Занятие 7  (Продолжительность 55 мин.)
- Итераторы и общий for
- Итераторы и замыкания
- Итераторы без состояния
- Итераторы со сложным состоянием

### Занятие 8 (Продолжительность 1 час 10 мин.)
- Компиляция, выполнение и ошибки

### Занятие 9 (Продолжительность 41 мин.)
- Структуры данных, Сериализация

### Занятие 10 (Продолжительность 1 час 3 мин.)
- Метатаблицы и метаметоды
- Таблицы со значениями по умолчанию

### Занятие 11 (Продолжительность 1 час)
- Модули и пакеты
- Использование окружений

### Занятие 12 (Продолжительность 1 час 2 мин.)
- Объектно-ориентированное программирование
- Классы
- Наследование
<br><br>

Часть по qLua (так называемая надстройка для языка Lua, которая используется в терминале Квик). Задача этой части объяснить специфику написания скриптов в терминале, какие методы необходимо использовать для решения разных задач.
В процессе занятий пишется скрипт с интерфейсом, торгующий по данным с графика, автостоп, автоучет позиции. Итоговый и промежуточные файлы скрипта поставляются для занятий.
Также поставляется набор библиотек, позволяющих строить скрипты. Для заданий поставляются файлы, разобранные на занятии. Проверка домашних заданий для занятий.

## Программа курса qLua (24 часа):

### Занятие 1
- Цели курса.
- Что такое Quik. История создания, архитектура.
- Установка, настройка.
- Дополнительные материалы, руководство пользователя.

### Занятие 2
- Язык Lua. Особенности.
- Материалы и ресурсы для изучения.
- Специфика qLua

### Занятие 3
- Что такое скрипт
- Запуск и остановка скриптов в Quik
- Индикаторы

### Занятие 4 (Продолжительность 38 мин.)
- Обзор инструментов для разработки скриптов.
- Обзор средств версионирования Git.

### Занятие 5 (Продолжительность 26 мин.)
- Структура скрипта Lua
- Событийная модель в терминале Квик

### Занятие 6 (Продолжительность 28 мин.)
- Индикаторы
- Структура индикатора
- Пример индикатора

### Занятие 7 (Продолжительность 34 мин.)
- Объяснение взаимодействия потоков Lua скрипта

### Занятие 8 (Продолжительность 50 мин.)
- Обзор сервисных функции терминала getInfoParam, OnConnected, OnDisconnected, getScriptPath и др.

### Занятие 9 (Продолжительность 1 час 7 мин.)
- Обзор таблиц терминала QUIK и методов получения данных из них.

### Занятие 10 (Продолжительность 1 час 28 мин.)
- Подписки на потоки данных по барам, данных стакана.
- Функции CreateDataSource, SetUpdateCallback, Subscribe_Level_II_Quotes, OnQuote, getQuoteLevel2.

### Занятие 11 (Продолжительность 1 час 36 мин.)
- Получение данных с графиков терминала.
- Управление метками на графике.
- Примеры использования.

### Занятие 12, 13 (Продолжительность 52 мин., 1 час 37 мин.)
- Транзакции. Подготовка параметров транзакции, корректное форматирование
представлений данных.
- Примеры отправки транзакций.

### Занятие 14 (Продолжительность 1 час 15 мин.)
- Таблицы (окна) скрипта. Обзор методов создания интерфейсных окон скрипта.
- Примеры создания окна, вывод информации.

### Занятие 15 (Продолжительность 1 час 16 мин.)
- Подготовка базы скрипта, торгующего по данным с графика.
- Логирование, функция main, проверка подключения к серверу, получение
информации по инструменту.

### Занятие 16, 17 (Продолжительность 1 час 21 мин., 1 час 58 мин.)
- Формирование методов получения и обработки данных с графика. Проверка торговых
сигналов.
- Подготовка и отправка транзакций по торговым сигналам.
- Контроль открытой позиции.

### Занятие 18, 19 (Продолжительность 2 час, 1 час 41 мин.)
- Установка стоп-ордера для открытой позиции.
- Поиск стоп-ордера при старте скрипта.
- Контроль соответствия стоп-ордера и открытой позиции (автостоп).

### Занятие 20 (Продолжительность 1 час 39 мин.)
- Контроль исполнения стоп-ордера.
- Обработка разворота позиции.
- Оптимизация методов.

### Занятие 21 (Продолжительность 1 час 31 мин.)
- Интерфейс скрипта. Создание окна скрипта, вывод информации.

### Занятие 22 (Продолжительность 1 час 39 мин.)
- Ввод данных в окне скрипта.
- Старт остановка торговли скрипта, без его выключения.
- Обработка команд через окно скрипта