---
layout: post
title: 'Дополнительный модуль "Вход трейлинг-лимитным ордером" к скрипту "Автостоп" (multiAutoStop)'
author: "nnh"
description: Дополнительный модуль "ВХОД ТРЕЙЛИНГ ЛИМИТНЫМ ОРДЕРОМ" к скрипту "Автостоп" (multiAutoStop).
tags: [скрипт,lua,qlua,квик,quik,автостоп,multiAutoStop]
---

[Видео работы](https://www.youtube.com/watch?v=xxkrizsB46o&t=1306s)

[rutube](https://rutube.ru/video/3f74e972b6fd09eefe84b5fcb3f31a3a/?t=576&r=plwd)

##	Основные возможности


При подключении модуля (скопировать поставляемый файл в папку plugins) в окне настроек появится разделы.

![](/assets/images/plugins/delay_open_pos.PNG)


### Раздел “ОТЛОЖЕННЫЙ ВХОД”:

 - Отложенный вход - переключатель активации режима. Возможные значения:
⦁	OFF - выключено
⦁	BUY - сделка на покупку
⦁	SELL - сделка на продажу
 - Условие активации входа - выбор условия активации входа. Возможные значения:
⦁	>=
⦁	<=
 - Уровень активации входа - цена активации сделки.
 - Тип цены отступа от уровня входа. Возможные значения:
⦁	% (процент) - процент
⦁	Steps – шаги цены инструмента
⦁	Pips - пункты цены инструмента
⦁	RUR - рубли. Здесь и далее имеется ввиду сумма в рублях относительно позиции по инструменту. Допустим 100 рублей на всю позицию.
 - Отступ от уровня входа - При активации условия будет установлен ордер по цене уровень + отступ в направлении сделки. Т.о. если необходимо войти по рынку, то необходимо задать большой положительный отступ.

Отрицательный отступ смещает цену ордера против направления входа. Т.о. можно задать фиксированный уровень постановки лимитного ордера при активации условия.
Параметр разрешено изменять при запущенном алгоритме. Изменение доступно через клавиши "стрелка влево" или "Стрелка вправо"


### Раздел "ВХОД ТРЕЙЛИНГ ЛИМИТНЫМ ОРДЕРОМ"

![](/assets/images/plugins/trail_open_order.PNG)


 - Вход трейлинг-лимитным ордером - При подаче торговой команды сделка осуществляется через установку лимитного ордера у спреда с заданным отступом. Если спред сдвигается на заданную величину (для покупок лучшее предложение, для продаж лучший спрос), то лимитный ордер переставляется на новый уровень от текущего спреда. Если задано ожидание для сделки по рынку, то после периода ожидания остаток ордера будет выполнен по рынку. Возможные варианты:
     - Spread - Отступ от спреда
     - Spread+ - Отступ от спреда противоположной стороны. Позволит устанавливать ордера внутрь спреда, если есть разрыв более шага цены.
     - Max. DOM - Отступ от макс. объема в стакане
 - Разбивать торговый объём по частям - Если торговый объём достаточно большой, то можно разбить его по частям, исполняя каждую часть отдельным трейлинг-ордером. Вход в сделку будет считаться выполненным когда будет набран полный объём сделки. Размер порции задаётся как процент от полного торгвого объема.
 - Торговый объём порции (%)
 - Тип цены отступа от уровня входа - Тип цены отступа от спреда. Возможные значения:
     - % (процент) - процент.
     - Steps – шаги цены инструмента.
     - Pips - пункты цены инструмента.
     - DOM Steps - уровни стакана. Заявка будет установлена на уровень стакана от границы спреда. Граница спреда - это 0-ой уровень. Следующий непустой уровень - 1-ый., и т.д.
 - Отступ - При подаче торговой команды сделка осуществляется не сразу, а установкой лимитного ордера у спреда с заданным отступом.
 - Размер макс. объема в стакане - Если выбран режим "Отступ от макс. объема в стакане", то можно указать минимальный размер объема, от которого будет установлен ордер. Если в момент подачи команды такого объема нет, то алгоритм перейдет в режим ожидания. В момент когда такой объем появится будет установлен ордер.
 - Контролировать макс. объема в стакане - Если выбран режим "Отступ от макс. объема в стакане" и такой объем обнаружен, то устанавливается ордер от него. В случае исчезноваения этого объема можно также снять ордер входа. Т.о. вход будет осуществен если обнаружен максимальный объем и он остался до момента исполнения ордера входа.
 - Тип цены движения спреда - Тип цены отступа от спреда. Возможные значения:
     - % (процент) - процент.
     - Steps – шаги цены инструмента.
     - Pips - пункты цены инструмента.
 -	Движение спреда для перестановки - Если спред сдвигается на заданную величину, то лимитный ордер переставляется на новый уровень от текущего спреда.
 -	Ожидание для сделки по рынку (в сек.) - Если задано ожидание для сделки по рынку, то после периода ожидания остаток ордера будет выполнен по рынку. Если укзано значение 0, то вход по рынку не производится.
 -	Попыток установки лимитного ордера - Если при установке лимитного ордера входа в позицию возникла ошибка, то будет предпринято не более попыток установки.
 -	Использовать для команды закрытия позиции - При включении режима команда закрытия позиции в интерфейсе также будет обрабатыватся через трейлинг лимитный ордер.
 -	Игнорировать при реверсивном стоп-ордере - Игнорировать при реверсивном стоп-ордере.
 -	Игнорировать при реверсивном профит-ордере - Игнорировать при реверсивном профит-ордере.

При включении режима торговые команды осуществляются не по рынку, а с помощью лимитных ордеров. Ордер устанавливается с отступом от спреда или (если включен режим Max. DOM) от максимального объема в стакане.
В режиме отсупа от спреда ордер устанавливается сразу при подаче команды. И если задано время ожидания исполнения "Ожидание для сделки по рынку", запускается таймер, по истечении которого сделка осуществляется по рынку. Если задан параметр "Движение спреда для перестановки", то после установки ордера начинает контрлироваться текущая граница спреда. Если она сдвигается на заданный размер в сторону сделки (цена убегает), то ордер переставляется на новый уровень от нового знаяения спреда. Т.о. ордер будет постоянно находится от спреда.

В режиме "Max. DOM" ордер не устнавливается сразу, а только тогда, когда в стакане появится объем больше или равный заданного значения "Размер макс. объема в стакане". Как только такой объем будет найден, то с заданным отступом от него в сторону спреда будет установлен ордер входа в позицию. Т.о. ордер будет находится перед максимальным объемом в ожидании исполнения. Если включен параметр "Контролировать макс. объема в стакане", то найденный  максимальный объем будет контролироваться на предмент исчезновения. Т.е. если этот объём сняли, то и ордер входа тоже снимается. В данном режиме можно задавать отрицательный отступ и т.о. ордер будет установлен за макс. объемом, а не перед ним.
Если включены режимы игнорирования реверсивных стоп, профит ордерах, то данный режим не будет активирован при исполнении стоп, профит ордера. Разворот будет осуществлен по рынку. Если же эти режимы выключены, то при исполнении стоп, профит ордера будет запущен вход в проивоположну.ю позицию в соотвтетсвии с настройками.
Т.к. данный режим видоизменяет поведение торговых команд, то с помощью него можно также закрывать позицию. Например, есть позиция ЛОНГ, при подаче команды SELL будет запущена задача на продажу с помощью лимитного ордера. И если заднный объем равен позции, то она закроется.

Команда xL в главном окне скрипта снимает уставновленный лмитный ордер и завершает запущенную задачу торговой команды.

Если включен режим "Использовать для команды закрытия позиции", то команда закрытия позиции в интерфейсе также будет обрабатыватся через трейлинг лимитный ордер, иначе по рынку.
