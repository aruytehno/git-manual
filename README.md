# Философия работы с git [![My Skills](https://skillicons.dev/icons?i=git)](https://skillicons.dev)

## Мотивация

В чём вообще смысл уделять внимание чистоте истории репы? Тут есть пара основных соображения, во многом повторяющих мотивацию заботы о чистоте кода.

### Чистую историю проще читать

По аналогии с тем как код во многом предназначен для чтения теми кто занимается его поддержкой и развитием, основной смысл существования истории в том, чтобы члены команды могли при необходимости разобраться в логике изменений. Основные сценарии, когда историю приходится читать:

1. Ревью pull request-а. На ревью основная задача коммитов ветки - максимально чётко доказать ревьюерам, что сделанные изменения одновременно и полностью решают поставленную задачу, и ничего при этом не ломают.
2. Blame. Регулярно случается, что спустя несколько месяцев или даже лет после написания кода возникает вопрос "почему это сделано именно так?". Зачастую при этом оригинальный автор либо уже не работает над проектом, либо недоступен, либо и сам уже не помнит чем он руководствовался, принимая те или иные решения. В подобных обстоятельствах log + blame являются важным источником информации, но только при условии, что коммиты были составлены достаточно аккуратно, чтобы по их истории можно было разобраться в логике автора.
3. Ввод в работу новых разработчиков. Коммиты коллег с более глубоким уровнем погружения в проект предоставляют новым разработчикам крайне ценную информацию о том как разные части кодовой базы связаны друг с другом, какие из них (и как) приходится затрагивать для решения тех или иных типовых задач. И чем история удобнее для восприятия, тем проще черпать из неё знания.

### Аккуратный подход к истории упрощает внесение изменений в кодовую базу

Слежение за чистотой истории вырабатывает ряд практик, заметно облегчающих сложные рефакторинги и внесение изменений в плохо знакомые части системы. Выражается это в том, что:

1. Гит становится неотъемлемой частью рабочего процесса, структурируя работу с кодом и предоставляя дополнительные возможности, выходящие за рамки того что даёт IDE.
2. Немного видоизменяется сам подход к модификации кода: **<TODO>**

## Как делать **не** стоит

Прежде чем приступить к конкретным рекомендациям что нужно делать для получения качественной истории, стоит сначала разобрать какие вещи не нужно делать, т.к. они мешают достижению озвученных ранее целей.

### Коммит не должен ломать билд, тесты или поведение системы

Как минимум, breaking commits мешают чисто технически, если нужно bisect-ом найти источник какой-либо проблемы. Но более критично, что они делают историю более запутанной и искажают её семантику: если коммит `abcdef` сломан, но к финальному коммиту ветки всё снова работает как надо, это означает, что коммит `abcdef` на самом деле неполный и какие-то фрагменты изменения спрятаны в других коммитах. Такую историю сложнее воспринимать, чем историю, где коммиты являются логически завершёнными.

### Коммит не должен смешивать в себе разные виды изменений (модификация поведения/рефакторинг/форматирование)

Если коммит, изменяющий поведение системы, помимо этого содержит ещё и требующийся для этого изменения рефакторинг, то на ревью становится сложно увидеть реальный scope изменения и верифицировать, что мимоходом не было задето что-нибудь лишнее. Намного нагляднее выделять рефакторинг в отдельный коммит: это и позволяет удостовериться, что рефакторинг произведён корректно (т.е. не меняет поведение), и освобождает коммит с изменением логики от нерелевантных диффов, так что по нему становится чётко видно что именно изменилось.

Подмешивание изменений форматирования в коммит, несущий смысловую нагрузку (будь то изменение логики или рефакторинг), хоть и менее критично, но тем не менее создаёт при чтении лишний шум. Лучше так не делать и коммитить изменения форматирования отдельно.

Ну и категорически плохо прятать изменения логики в рефакторингах или изменениях форматирования - это сильно повышает шанс, что изменение будет пропущено на ревью.

### Коммит не должен содержать сразу несколько логических изменений поведения

Добавление в комиит более одного логического изменения искажает восприятие того как части кодовой базы на самом деле связаны друг с другом, что усложняет жизнь людям, которые читают историю изменений с целью погрузиться в незнакомый проект или незнакомую часть системы. Сложнее становится и ревьюерам: смешивание нескольких изменений в один коммит размывает границы этих изменений, и на ревью становится тяжелее верифицировать, что изменения добиваются заявленного результата, не внося при этом неожиданных сайд-эффектов. И даже для самого автора объединение нескольких изменений в один коммит может стать источником дополнительного геморроя, если в процессе работы над фичей выяснится, что какое-то из этих изменений на самом деле было неправильным (или неполным, или должно сливаться отдельно от остальных изменений).

### Коммит не должен содержать сразу несколько разнородных рефакторингов

В ряде случаев смешивание в одном коммите нескольких не связанных очевидным образом рефакторингов усложняет верификацию, что этот коммит действительно не меняет поведение системы, таким образом усложняя ревью.

### **<TODO> душевные метания в новом функционале - надо или достаточно темы про брейки?**

## Как выглядит хорошая история

Каждый коммит должен преследовать конкретную цель, задекларированную в описании коммита. Изменения, вносимые коммитом, должны полностью этой цели добиваться, но при этом не содержать ничего лишнего, что к достижению этой цели напрямую не относится. Если по изменениям коммита непонятно **почему** эти изменения решают поставленную задачу, аргументацию стоит более подробно раскрыть в расширенном описании.

В подавляющем большинстве случаев оптимальная ментальная модель при работе над фичей - это своего рода "кодинг рефакторингами", при котором фичеветка выглядит примерно следующим образом:

- Сначала идёт последовательность коммитов с элементарными рефакторингами. Каждый из них не меняет поведение системы (и это должно быть очевидно при чтении коммита), но все вместе они постепенно приводят кодовую базу в состояние, в котором появляется возможность реализовать фичу достаточно простым образом.
- В конце ветки идёт коммит (или несколько коммитов), непосредственно реализующий фичу на основе созданной рефакторингами инфраструктуры.

## Как добиваться хорошей истории

**<TODO> коммититься сразу по мере выполнения каких-то действий**
**<TODO> при внесении изменения понимать в каком месте истории оно должно быть: новый коммит, часть старого коммита; типичный сценарий "что-то делал и обнаружил пререквизит"**

## Технические приёмы

**<TODO> амменд коммита**
**<TODO> построчный коммит**
**<TODO> суть веток, hard reset, mixed reset, cherry pick**
**<TODO> interactive rebase**
Основная цель Mermaid-помочь визуализировать документацию.  

[mermaid git](https://github.com/mermaid-js/mermaid/blob/develop/docs/README.md)  
[extension chrome](https://chrome.google.com/webstore/detail/github-%2B-mermaid/goiiopgdnkogdbjmncgedmgpoajilohe/related)  
[Mermaid Live Editor](https://mermaid-js.github.io/mermaid-live-editor)  
[Иконки для инфографиков](https://fontawesome.com/)
