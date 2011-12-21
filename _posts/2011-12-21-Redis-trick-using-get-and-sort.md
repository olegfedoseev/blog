---
layout: post
title: "Redis - хитрость использования GET и SORT"
published: true
permalink: redis-trick-using-get-and-sort
date: 2011-12-21
---

Когда нужно сохранить в редисе сложную структуру данных, обычно делают следующим образом - значения хранят в обычных
ключах, например:
<pre class="brush:plain">
redis> SET prefix-field-title "Title"
OK
redis> SET prefix-field-name "Name of the object"
OK
redis> SET prefix-field-desc "Description"
OK
redis> SET prefix-field-tags "Tags of the object"
OK
</pre>

При этом список нужных полей хранится в ["списке"](http://redis.io/topics/data-types#lists):
<pre class="brush:plain">
redis> LPUSH object "title"
(integer) 1
redis> LPUSH object "name"
(integer) 2
redis> LPUSH object "tags"
(integer) 3
redis> LRANGE object 0 -1
1) "tags"
2) "name"
3) "title"
</pre>

Вопрос в том как нам эффективнее эти значения получить? Да, можно сделать LRANGE для получения списка полей, и потом 
сделать MGET, но можно сделать ещё эффективнее, можно получить за один запрос, используя комманду [SORT](http://redis.io/commands/sort):
<pre class="brush:plain">
redis> SORT object GET prefix-field-*
1) "Tags of the object"
2) "Name of the object"
3) "Title"
</pre>