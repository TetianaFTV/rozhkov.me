---
title: "Jelastic"
date: 2020-03-15T10:18:10+02:00
lastmod: 2020-03-15T10:18:10+02:00
tags: ["tech"]
---

>Это технический пост. PaaS и инфраструктура для людей — это одна из немногих технических тем, которые меня действительно заботят. Я убежден что работа с инфраструктурой должна быть простой и понятной большинству разработчиков, а современное состояние дел просто ужасно, невероятно переусложнено и абсолютно не для людей. Jelastic пытается в некотором смысле решить эту проблему.

На просторах интернетов довольно мало не-марекетинговых материалов о Jelastic и я, как пользователь со стажем, хочу заполнить этот вакуум.

Первый раз о [Jelastic](https://jelastic.com/) я услышал на конференции JavaDay в 2014 году. Тогда эта компания была спонсором ивента, CEO Руслан Синицкий выступал с keynote на открытии, а в эндорсеры контора подписала одного одного из авторов Java — товарища Гослинга. На самой конфе я не особо понял что это вообще такое, в память врезалось только сравнение с AWS, утверждение “вы не платите за неиспользуемые ресурсы” а так же “мы начали поддерживать Docker, когда это еще не было мейнстримом”. Я тогда еще не сильно соображал в инфраструктуре, контейнерах и облаках, поэтому не проникся. Однако название запомнил.

Спустя пару лет, когда я уже стал относительно хорошо разбираться в облачных вещах, у меня возникла задача где-то хостить свой свежеразрабатываемый стартап. AWS хотелось, но было дорого, я перебирал другие провайдеры и вдруг вспомнил о Jelastic. Быстрый поиск нашел мне одного из провайдеров (в Нидерландах кажется), я подключился и мы поехали. Спустя некоторое время я перенес значительную часть инфраструктуры одного из моих заказчиков тоже на Jelastic, но уже у другого провайдера. В сумме в продакшене оно крутится уже года 4.

### Итак, что же такое Jelastic, простыми словами и без маркетингового буллшита?

Это система управления виртуальными машинами с большим количеством предконфигурированных приложений.

Вы заходите в систему и создаете “окружение”. В его рамках можно добавлять множество виртуальных машин + балансировщик над ними. Каждая виртуальная машина может быть как “пустой”, то есть обычной VPS с CentOS или Ubuntu на борту, так и созданной из определенного шаблона, например базой MariaDB. UI предполагает несколько “типов” виртуальных машин — балансировщик, сервер приложений, база данных, nosql база данных, кэш. Для каждой из “коробочек” можно выбрать нужный тип софта. Так же есть возможность просто построить окружение из любых Docker-образов. Все виртуальные машины естественно находятся в одной подсети и видят друг друга.

{{< figure src="/images/jelastic/1.jpg" title="Создаем окружение" >}}

Каждую виртуальную машину внутри окружения можно независимо масштабировать как вертикально — добавлять больше памяти или процессора, так и горизонтально — добавлять больше нод. Предусмотрен настраеваемый автоскейлинг по событиям поедания памяти или CPU. Деньги вы платите только за те ресурсы которые фактически использовали ваши приложения. В этом смысле ценовая политика отличается от традиционных облачных провайдеров, которые лупят деньги за весь инстанс сразу, пока он был включён.

Сама эта платформа возникла во время работы Руслана Синицкого на каком-то производстве (если не ошибаюсь то связанном с аэрокосмическими делами) где нужно было управлять инфраструктурой. В то далёкое время еще не придумали ни докеров ни кубернетесов и ни даже анзиблов с паппетами и ребята разработали свой софт который потом коммерциализировали.

Jelastic не продается как SaaS, то есть это не совсем аналог AWS, Azure или DigitalOcean. Сам управляющий софт может разворачиваться на любой инфраструктуре. Собственно, основное их коммерческое предложение — это построение приватного облака. Для обычных людей вроде нас с вами у Jelastic на сайте есть [список провайдеров-партнёров](https://jelastic.cloud/), к которым вы можете подключиться.

Когда контейнеры были еще не модными а приложения нужно было деплоить через залив варника на FTP и последующий рестарт, Jelastic давал возможность триггерить деплой из svn. Сейчас я понимаю что в то время это был полный космос, по сравнению с тем, как деплоил остальной мир.

Потом появились контейнеры и Jelastic начал поддерживать и их.

Как я уже говорил — в платформе есть большое количество предконфигурированных приложений — базы данных, аппликейшен сервера, эластиксёрч и тд, сотни их. Это позволяет в один клик поднять например инстанс базы, то есть такой себе аналог RDS. Так же есть маркетплейс где есть куча уже прикладного софта типа Jenkins, GitLab, Wordpress, PHPBB (хаха), Docker Container Registry и так далее. Я этим всем не пользовался, поэтому не могу сказать как оно.

{{< figure src="/images/jelastic/2.jpg" title="Куча предконфигурированных приложений" >}}

Каждое окружение можно склонировать одной кнопкой, при этом будут сохранены данные и конфигурация — удобно для бекапа и для разворачивания тестовых окружений.

### Теперь о том, как этим всем пользуюсь конкретно я:

Итак, у меня есть проект, традиционно он состоит из серверов приложений и базы данных. Сервера приложений — это stateless докер контейнеры. У меня создано идентичных окружения, в каждом из которых есть по одной ноде с приложением. Перед ними установлен балансировщик (это встроенная штука которая есть в Jelastic) который работает по round-robin и делает SSL терминацию. Сбоку крутится еще одно окружение с MariaDB. Такая же конфигурация настроена для dev окружения.

Как это все деплоится? База данных и балансировщик создаются вручную и далее не меняются, сервера приложений разворачиваются из Docker образов которые собирает GitLab CI (раньше Jenkins). В панели можно указать путь к приватному хранилищу, подсунуть ему пароли и Jelastic сам будет подтягивать доступные версии образов. Редеплой можно делать вручную через кнопочку или через API. Раньше у меня был настроен автоматический редеплой но потом что-то сломалось и сейчас я, к своему стыду, деплою кнопочкой. Благо, это надо делать нечасто, но каждый раз у меня никак не хватает мотивации написать скрипт. Shame on me.

Важная штука — в отличие от, например, ECS или K8S, ваши контейнеры это не машины на которых запущен Docker и какой-то оркестрационный софт. Насколько я понял, Jelastic берет Docker образ и буквально физически разворачивает его в физическую файловую систему, то есть ваш контейнер — не контейнер а живая машина. С этим связано несколько особенностей. Во-первых, при редеплое не теряется конфигурация и данные (и не перетираются), во-вторых, если вы поменяли базовый образ для своего приложения (заменили FROM) то редеплой сломается. По крайней мере так было раньше, сейчас не знаю как. То есть это не иммутатебельная инфраструктура где редеплой контейнера сносит все начисто. Меня это здорово сбило с толку вначале.

На каждую машину можно заsshиться и посмотреть что там внутри, поделать какие-то команды или подкрутить конфигурацию.

Из UI так же можно походить по файловой системе, посмотреть статистику использования ресурсов и логи (которые автодополняются). Логи довольно удобная вещь если что-то сломалось.

### С какими проблемами я столкнулся за годы эксплуатации?

Самая частая проблема — отвал DNS. Иногда, примерно раз в пару месяцев, внутренний DNS перестает работать и в логах я вижу сообщения типа `unknown host: sqs.eu-west-1.amazonaws.com`. Обращения в саппорт, дебаг и тд ничего не дали, “все работает”. Я подозреваю что это проблема конкретного провайдера. 

Два или три раза был полный отвал DNS записей нашего провайдера. Jelastic торчит наружу через CNAME вида `app.cooljelastichosting.com` и несколько раз было такое что они просто переставали существовать то есть показывать вникуда, соответственно у меня все падало кроме фоновых джоб.

Непонятно как работает SSL, там есть LetsEncrypt тем, но он то работает то не работает. Можно загружать свои сертификаты но я этим не пользовался. Короче для прода я настроил но оно дышит на ладан. Вчера пробовал сконфигурить его опять и не заработало. В саппорт мне писать впадлу потому что они просто заходят на виртуальную машину и вручную там правят конфиги, а мне надо чтобы оно само разворачивалось.

### Шо по деньгам? 

Значительно дешевле амазона. За всю инфру мы платим примерно 300$, это за 20-30 серверов. Ресурсы гипервизор выделяет автоматически в зависимости от потребностей приложения, как оно там работает не знаю но в целом ок.

### Чего мне не хватает?

Jelastic это что-то посредине между full-managed платформой как Heroku и IaaS как AWS/CGP/Azure/и тд.

Если говорить про первый подход, то мне капитально не хватает простоты деплоя и managed баз данных. В Heroku все сильно проще (но и дороже).

Если про второй — то не хватает дополнительных сервисов типа стореджа, очередей, лямбд, CDN, то есть Jelastic — это только про Compute. Не хватает тонкой настройки VPC, например фаерволы появились только недавно (год назад примерно), нет возможности изолировать окружения друг от друга, то есть создать отдельные подсети и конфигурить для них роутинг.

CLI/API очень топорный и не возникает желания ним пользоваться. Я даже было хотел начать писать своего клиента, более человеческого, но потом забил на эту историю.

### Рекомендую ли я вам эту штуку? 

Если бюджет сильно ограничен то можно выбрать дешевого провайдера и платить за всю инфру десяток евро. В остальном скорее нет, для простых вещей мне гораздо больше нравится Heroku, для сложных — уже хочется вращать терраформом амазон.

Мне лично Jelastic очень удобен для создания инстансов баз данных. Такой себе RDS на минималках, но при этом стоит копейки.

Мир идет в сторону кубера, кубер можно развернуть и на Jelastic (там прям кнопочка есть), но я не пробовал. Вообще мне кубер сильно не нравится, но про это потом.

### Послесловие

На мой взгляд Jelastic очень сильно опередил своё время, но по какой-то причине не получил хорошего traction внутри сообщества, несмотря на маркетинговые усилия компании. Насколько я понимаю, они неплохо себя чувствуют в энтерпрайзе, но вот так чтобы на рандомном девопс митапе спросить человека — “ты знаешь про Jelastic?” я думаю что 95% ответят “что? ElasticSearch?”

Вот такая история, задавайте ответы в комментариях + я уверен что сюда придут ребята из Jelastic и добавят что-то сверху.