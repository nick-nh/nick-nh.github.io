---
layout: post
title: "Реализация быстрой очереди на Lua"
author: "nnh"
description: Особенности работы со очередями в Lua
tags: [lua,qlua]
---

## Очереди на Lua.

Очень часто необходимо организовать очередь из объектов, величин, событий. И не важно что это, важно, что они помещаются в конец, а вынимаются из начала. Т.о. обеспечиваем обработки по FIFO.

Самая банальная реализация - это использовать два метода table.insert (или t[#t+1]) и table.remove.

Например:

    Создаем таблицу, хранящую очередь:

```lua
    queue = {}

    -- в разных участках кода добавляем элементы в очередь:
    queue[#queue + 1] = val1

    queue[#queue + 1] = val2

    queue[#queue + 1] = val3

    queue[#queue + 1] = val4

    queue[#queue + 1] = val5

    -- И потом разбираем очередь через такую банальную функцию:

    function queue_dispatch()
        --Если нет элементов, то сразу выходим
        if #queue == 0 then return end

        -- Пока есть элементы, получаем первый, обрабатываем его и удаляем из очереди
        while #queue ~= 0 do
            local value = queue[1]
            table.remove(queue, 1)
            -- Что-то делаем с элементом value
            -- ...
        end
    end
```

    т.о. при вызове queue_dispatch - очистится вся очередь. Можно организовать обработку не всех сразу, а одного или пачки элементов за один вызов. Но это уже нюансы.

    Что же плохого в этом подходе? Дело в том, что table.remove - это очень затратная операция. Она вынимает указанный элемент из массива (в нашем случае первый) и сдвигает все оставшиеся элементы на одну позицию вверх.
    Т.о. после удаления элемента, второй станет первым, третий - вторым и т.д. Это необходимо, чтобы в массиве queue не образовалась "дырка" по индексу 1.
    Поэтому такой подход, хоть и рабочий, но достаточно не эффективный. Впрочем, для массивов из нескольких элементов, на этом можно было бы и закончить.

    Если же у нас много элементов, или очень критично время обработки очереди, то необходим другой подход.
    Чаще всего предлагается такой подход (https://forum.quik.ru/messages/forum10/message82932/topic9604/#message82932) с использованием нумераторов первого и последнего элемента:

```lua
    ----   Функции работы с очередями --
    --- При использовании между потоком колбеков и потоком main очереди потокобезопасные.
    --- Создать очередь
    function QueueNew()
        return  {first = 1, last = 0 }
    end
    ---  Записать в очередь --
    function QueuePush(self, v)
        if v == nil then error('!!! Ошибка. Параметр v функции QueuePush nil') end
        local last = self.last + 1
        self[last] = v
        self.last = last
        return last - self.first + 1    -- количество элементов в очереди ---
    end
    ---  Читать очередь  --
    function QueuePop(self)
        local  first =  self.first
        if  first > self.last then return nil   end
        local v, v1  =  self[first], self[first + 1]
        self[first]  =  nil
        self.first   = first + 1
        v1 = type (v1) == 'string' and #v1 or 0
        return  v, v1   -- v1 -длина следующего элемента (если строка) ---
    end

    -- Текущий размер очереди ---
    function QueueSize(self)
        return self.last - self.first + 1
    end
```

    Идея в том, что управляем номерами первого и последнего элемента.
    При вызове QueuePush last увеличивается на 1, т.к. мы помещаем новый элемент в очередь.
    При вызове QueuePop, проверяем, что элементы есть. И если есть, то смещаем first на 1. Если был 1, то станет 2, был 2, станет 3 и т.д. И очищаем доставаемый элемент из очереди.
    При этом не происходит смещение элементов.

    Но какая проблема этого подхода? Давайте запустим код и посмотрим, что происходит:

```lua
    local q = QueueNew()
    -- Помещаем 10 элементов в очередь
    for i = 1, 10 do
        QueuePush(q, i)
    end

    -- Вынимаем элементы, пока очередь не опустеет.
    local x = QueuePop(q)
    local s = 1
    print(s, 'x: ', x, 'q['..s..']', q[s], 'size', QueueSize(q), 'first', q.first, 'last', q.last)
    while x do
        x = QueuePop(q)
        s = s + 1
        print(s, 'x: ', x, 'q['..s..']', q[s], 'size', QueueSize(q), 'first', q.first, 'last', q.last)
    end
```

    Смотрим вывод:

    1	x: 	1	q[1]	nil	size	9	first	2	last	10
    2	x: 	2	q[2]	nil	size	8	first	3	last	10
    3	x: 	3	q[3]	nil	size	7	first	4	last	10
    4	x: 	4	q[4]	nil	size	6	first	5	last	10
    5	x: 	5	q[5]	nil	size	5	first	6	last	10
    6	x: 	6	q[6]	nil	size	4	first	7	last	10
    7	x: 	7	q[7]	nil	size	3	first	8	last	10
    8	x: 	8	q[8]	nil	size	2	first	9	last	10
    9	x: 	9	q[9]	nil	size	1	first	10	last	10
    10	x: 	10	q[10]	nil	size	0	first	11	last	10

    Видно, что элементы корректно получается, размер уменьшается. Но специально добавленная переменная s показывает, что из таблицы происходит очистка элемента и образуется дырка  в таблице.

    А также видно как смещается first на 1. В итоге после этой итерации у нас first	= 11, last = 10.

    Добавим еще элементы и опять посмотрим на очередь:

```lua
    for i = 1, 10 do
        QueuePush(q, i)
    end

    s = q.first
    x = QueuePop(q)
    print(s, 'x: ', x, 'q['..s..']', q[s], 'size', QueueSize(q), 'first', q.first, 'last', q.last)
    while x do
        x = QueuePop(q)
        s = s + 1
        print(s, 'x: ', x, 'q['..s..']', q[s], 'size', QueueSize(q), 'first', q.first, 'last', q.last)
    end
```

    Видно, что first = 11, и далее увеличивается. И опять образуются дырки по месту вынутых элементов.

    11	x: 	1	q[11]	nil	size	9	first	12	last	20
    12	x: 	2	q[12]	nil	size	8	first	13	last	20
    13	x: 	3	q[13]	nil	size	7	first	14	last	20
    14	x: 	4	q[14]	nil	size	6	first	15	last	20
    15	x: 	5	q[15]	nil	size	5	first	16	last	20
    16	x: 	6	q[16]	nil	size	4	first	17	last	20
    17	x: 	7	q[17]	nil	size	3	first	18	last	20
    18	x: 	8	q[18]	nil	size	2	first	19	last	20
    19	x: 	9	q[19]	nil	size	1	first	20	last	20
    20	x: 	10	q[20]	nil	size	0	first	21	last	20

    Казалось бы - ничего страшного, главное - сдвига элементов нет. Но если таких итераций будет много, то first и last станут очень большими величинами.
    А также в самой таблице будет очень много пустого места, т.к. first будет каждый раз смещаться. Выход - рехешировать таблицу в какой-то момент.
    Т.е. пройтись по элементам и изменить их индексы, чтобы опять начиналось с индекса 1. Или при полной очистке очереди делать то же самое.
    Например, в методе QueuePop, при определении пустой очереди сбросить индексы first, last:

```lua
    if  first > self.last then
        self.first = 1
        self.last = 0
        return nil
    end
```

    Этот подход можно использовать если необходимо получать размер очереди и выемка элементов последовательная.

    Но представим, что размер очереди не важен. Все что требуется - это поместить элемент и достать элемент. И что еще важно, необходимо иметь возможность достать элемент из середины очереди, также без сдвига всех элементов.
    Например в очереди из 100 элементов, достать 48-ой. И чтобы элементы с 49 по 100, не пришлось смещать.

    И такой вариант возможен. Я его использую чаще всего, т.к. по факту, размер очереди не важен, а вот вынуть элемент из середины - намного важнее.

    Реализуется такой вариант на связном списке. Где каждый элемент очереди ссылается на следующий. Т.о. вынимая элемент из очереди, мы просто смещаем ссылку на следующий элемент.
    Но для реализации Remove из середины нам придется превратить односвязный список в двусвязный (doubly linked list). Да, это добавит один указатель (prev) в каждый узел, но это единственный способ "схлопывать" список за O(1), не обходя его целиком.

```lua
    -- Создаем класс для очереди
    local queue_class = {}
    queue_class.__index = queue_class
    -- Добавляем метамод __len, чтобы работал вариант добавления в очередь через q[#q+1] = value
    function queue_class.__len(self)
        return self.head == nil and 0 or 1
    end
    function queue_class.__newindex(self, _, value)
        self:push(value)
    end

    -- Создаем экземпляр очереди. Он хранит два важных элемента head - голова (первый элемент) очереди, tail - хвост (последний элемент) очереди.
    -- Использование rawset(table, key, value) для поддержки варианта добавления через q[#q+1] = value
    -- pool - это ссылка на прошлый удаленный узел, чтобы при добавлении нового элемента в очередь не выделять дополнительную память, а использовать уже выделенное ранее.
    function queue_class.new()
        local self = setmetatable({}, queue_class)
        rawset(self, 'head', nil)
        rawset(self, 'tail', nil)
        rawset(self, 'pool', nil)
        return self
    end

    -- Помещаем элемент в очередь.
    function queue_class:push(value)
        local node
        local pool = self.pool
        if pool then
            node = pool
            rawset(self, 'pool', pool.next)
            node.value = value
            node.next = nil
            node.prev = nil
        else
            node = {value = value, next = nil, prev = nil}
        end
        if self.tail then
            self.tail.next = node
            node.prev = self.tail
            rawset(self, 'tail', node)
        else
            rawset(self, 'head', node)
            rawset(self, 'tail', node)
        end
    end

    -- Извлекает элемент из начала очереди
    function queue_class:pop()
        if not self.head then return end
        local node = self.head
        rawset(self, 'head', node.next)
        if self.head then
            self.head.prev = nil
        else
            rawset(self, 'tail', nil)
        end
        local value = node.value
        node.value = nil
        node.next = self.pool
        node.prev = nil
        -- Возврат в пул
        rawset(self, 'pool', node)
        return value
    end

    -- Удаляет конкретный узел из любого места очереди за O(1)
    function queue_class:remove(node)
        if not node.next and not node.prev and self.head ~= node and self.tail ~= node then
            return
        end
        local next_node = node.next
        local prev_node = node.prev
        if prev_node then
            prev_node.next = next_node
        else
            rawset(self, 'head', next_node)
        end
        if next_node then
            next_node.prev = prev_node
        else
            rawset(self, 'tail', prev_node)
        end
        node.value = nil
        node.next = self.pool
        node.prev = nil
        rawset(self, 'pool', node)
    end

    -- Итератор по очереди
    -- Возвращает функцию, которую можно использовать в цикле for.
    -- Безопасен к вызову self:remove() внутри тела цикла.
    function queue_class:next()
        local next_node = self.head
        return function()
            local current_node = next_node
            if current_node then
                next_node = current_node.next
                return current_node, current_node.value
            end
            return nil
        end
    end

    -- Пример использования (как это работает на практике):
    -- Создаем очередь
    local q = Queue.new()

    q:push("Задача 1")
    q:push("Задача 2 (удалить)")
    q:push("Задача 3")
    q:push("Задача 4")

    q[#q+1] = "Задача 5" -- Демонстрация возможности добавления как в обычный массив.

    print("--- Итерация с удалением ---")
    for node, value in q:GetNext() do
        print("Обрабатываю:", value)

        if value == "Задача 2 (удалить)" then
            print("  -> Удаляю этот узел!")
            q:remove(node)
            -- Итератор не сломается, так как он уже запомнил "Задача 3" как next_node
        end
    end

    print("\n--- Проверяем что осталось ---")
    for node, value in q:GetNext() do
        print("Осталось:", value)
    end
```

Смотрим вывод:

    --- Итерация с удалением ---
    Обрабатываю:    Задача 1
    Обрабатываю:    Задача 2 (удалить)
    -> Удаляю этот узел!
    Обрабатываю:    Задача 3
    Обрабатываю:    Задача 4
    Обрабатываю:    Задача 5

    --- Проверяем что осталось ---
    Осталось:       Задача 1
    Осталось:       Задача 3
    Осталось:       Задача 4
    Осталось:       Задача 5

Т.о. такое решение позволяет быстро помещать и вынимать элементы из очереди. Имеет встроенный итератор для обхода элементов. И позволяет вынимать элемент из середины не нарушая список.
Но мы не знаем размер очереди, все что мы знаем - пустая она или нет. В очень многих задачах это вполне разумное допущение.
Наиболее частое применение - это организация последовательного выполнения задач, с проверкой выполнения и удаления выполненной из очереди.

Например, у нас есть в очереди 10 задач. При этом время их выполнения различное. Поэтому задача 2 может выполнится раньше первой.
И мы можем уже удалить её из очереди. А остальные задаи будет выполняться и мы будем проверять их состояние, постепенно очищая очередь.
В обычной реализации очереди, чтобы удалить элемент из середины списка пришлось бы все равно смещать элементы, т.к. решение с указателями на начало и конец очереди, не решит вопрос выемки из середины.
Двусвязный список позволят это сделать и сделать это быстро.






