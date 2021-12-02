---
layout: post
title: "Отправка сообщений из Квик"
author: "nnh"
description: Отправка сообщений из Квик.
tags: [скрипт,lua,qlua,квик,quik]
---

Из терминала Квик можно отправлять оповещения на почту и в **Телеграм**. Для этого используется решение [telegramQuik](https://github.com/nick-nh/qlua/tree/master/telegramQuik).
Также можно получать сообщения из телеграм.

Частично данный скрипт был описан [скрипте transactionPocket](https://nick-nh.github.io/2021-01-10/transactionPocket-post)

Для отправки сообщений в Телеграм используется telegram bot api. Поэтому необходимо иметь зарегистрированный бот в Телеграм.

Важно: для корректной работы в Windows 7 необходимо:
- Windows 7 SP1
- [Установить дополнение](https://support.microsoft.com/en-us/topic/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-winhttp-in-windows-c4bd73d2-31d7-761e-0178-11268bb10392).

Допустим, файлы размещены в каталоге telegramServer. В нем размещены файлы запуска и настройки сервера отправки сообщений. Запуск сервера отправки сообщений происходит, посредством вызова файла startTeleServer.bat, расположенного в каталоге telegramServer. После запуска сервера отправки сообщений появится консольное окно, сигнализирующее о его готовности.

Одновременный запуск нескольких экземпляров сервера отправки сообщений MessagesQServer.exe с одинаковыми именами каналов (Pipes) нежелателен, т.к. они будут конфликтовать друг с другом.
Поэтому ручной старт сервера желательно производить через файл startTeleServer.bat. При этом будет проверен есть ли уже запущенный процесс MessagesQServer.exe и, если есть, то второй экземпляр уже не будет запущен.

![](/assets/images/trP/trP_10.PNG)

[Небольшое видео работы](https://youtu.be/14c4nQiDdfU)

## Настройки

Перед запуском необходимо провести настройку сервера отправки сообщений. Это производится через редактирование файла settings.ini в каталоге сервера отправки.

[Видео с описанием регистрации бота и настроек](https://youtu.be/Vy_m7qUwirY)

Содержимое данного файла:<br>
[TELEGRAM]<br>
START_TELEGRAM = ON<br>
TELEGRAM_PIPENAME = telegram_pipe<br>
TOKEN = Your TOKEN<br>
USE_ENCODING = windows-1251<br>
[EMAIL]<br>
START_EMAIL = ON<br>
EMAIL_PIPENAME = email_pipe<br>
SENDER = sender<br>
RECIPIENT = recipient<br>
COPY =<br>
EMAIL_SUBJECT = Message from Quik<br>
SMTPSERVER = smtp<br>
SERVERPORT = 587<br>
LOGIN = login<br>
PASSWORD = pass<br>

Раздел [TELEGRAM] - раздел настройки отправки сообщений в **Телеграм**. Если необходима отправка сообщений в **Телеграм**, то необходимо установить настройку START_TELEGRAM = ON, иначе OFF. Сообщения в **Телеграм** отправляются в зарегистрированный бот. О регистрации ботов в **Телеграм** можно найти подробную инструкцию на сайте сервиса **Телеграм** или воспользоваться поиском.<br>
- TELEGRAM_PIPENAME - это сервисная настройка имени канала передачи сообщений. Если планируется использовать только один сервер отправки сообщений, то данное поле необходимо оставить без изменения.<br>
- TOKEN - здесь вводится токен, полученный при регистрации бота в **Телеграм**. Если не введен токен, то отправка сообщений в телегам будет невозможна.<br>
- USE_ENCODING = windows-1251. Данная настройка определяет тип кодировки текста сообщений отправляемых из терминала. Т.к. Квик поддерживает только кодировку Win1251, то данную настройку необходимо оставить без изменения.

Раздел [EMAIL] - раздел настройки отправки сообщений на электронную почту. Если необходима отправка почтовых сообщений, то необходимо установить START_EMAIL = ON, иначе OFF.<br>
- EMAIL_PIPENAME - это сервисная настройка имени канала передачи сообщений. Если планируется использовать только один сервер отправки сообщений, то данное поле необходимо оставить без изменения.<br>
- SENDER - электронный адрес отправителя
- RECIPIENT - электронный адрес получателя
- COPY - список (через ;) электронных адресов получателей копии.
- EMAIL_SUBJECT - тема письма
- SMTPSERVER - адрес сервера отправки сообщений. Например smtp.yandex.ru
- SERVERPORT - номер порта отправки сообщений. Обычно это 465. Для сервисов Яндекса также используется 587
- LOGIN - ваш логин
- PASSWORD - ваш пароль
<br>

[Файл luaPipeTest.lua](https://github.com/nick-nh/qlua/blob/master/telegramQuik/luaPipeTest.lua) - это тест для библиотеки, пример использования отправки и приема сообщений.<br>
[Файл ansi2utf8.lua](https://github.com/nick-nh/qlua/blob/master/telegramQuik/ansi2utf8.lua) - Библиотека конвертации ansi(win1251)<->utf8. На данный момент не используется, т.к. сам сервер отправки производит конвертацию сообщения в utf8.

Для подключения необходимо в свои скрипты подключить библиотеку luaPipe.dll через require('luaPipe').

## Методы

Пример запуска сервера отправки<br>
os.execute('start cmd /c call "'..path..'\\telegramServer\\startTeleServer.bat'..'"')

#### SendMessage
Вызов
- #### luaPipe.SendMessage(str, 'telegram_pipe')
- #### luaPipe.SendMessage(str, 'email_pipe')<br>

где
- str - строка сообщения
- 'telegram_pipe' - имя канала отправки (TELEGRAM_PIPENAME) в телеграм, заданного в файле settings.ini
- 'email_pipe' - имя канала отправки (EMAIL_PIPENAME) на почту, заданного в файле settings.ini


#### GetIncomeMessages
Вызов
#### local str = luaPipe.GetIncomeMessages('telegram_pipe')

где
- 'telegram_pipe' - имя канала отправки (TELEGRAM_PIPENAME), заданного в файле settings.ini

Возвращается сериализованная таблица сообщений в виде строки.<br>
Для получения таблицы из строки представления можно воспользоваться методом:<br>
#### local t = assert(load('return '..str))()
Полученные сообщения будут сохранены в массиве t[1], t[2] т.д.

## Использование без библиотеки luaPipe

Язык Lua позволяет открывать именованные каналы через команду io.open. Поэтому возможна отправка используя такой синтаксис

    local function SendTeleMessage(msg, pipe_name)
        local tele_pipe = io.open("\\\\.\\PIPE\\"..pipe_name, "w+b") -- открываем именованный канал
        if not tele_pipe then
            return false
        end
        tele_pipe:write(msg) -- записываем сообщение в канал
        tele_pipe:close() -- закрываем канал
    end

А чтение из канала таким образом:

    -- Аналогично прошлому примеру записываем в канал команду GetTeleMessage, чтобы сервер отправки вернул нам накопленные сообщения
    local function GetTeleMessage(pipe_name)
        local tele_pipe = io.open("\\\\.\\PIPE\\"..pipe_name, "w+b")
        if not tele_pipe then
            return false
        end
        tele_pipe:write('GetIncomeMessages()') -- записываем команду в канал

        local rd = ''
        local ct = os.time()
        -- Т.к. время ожидания ответа может быть не мгновенным, то ожидаем 2 секунды, читая из канала ответ.
        while os.time() - ct < 2 and rd == '' do
            rd = tele_pipe:read('*a')
        end
        tele_pipe:close() -- закрываем канал

        --Формат возврата - сериализованная таблица сообщений в виде строки
        if type(rd) == 'string' then
            local t = assert(loadstring('return '..rd))() -- загружаем в таблицу
            if type(t) == 'table' then
                return t
            end
        end
    end


## Сборка

Если вы хотите сами собрать библиотеки, то чтобы собрать MessagesQServer необходимо установить через NuGet в проект пакет Telegram.Bot.<br>
Для сборки luaPipe достаточно ее выполнить VS.