---
title: "О простых докладах"
date: 2019-06-15T09:51:07+03:00
lastmod: 2019-06-15T09:51:07+03:00
tags: ["random"]
---

На прошлой неделе посетил мини-митап на 4 человека с одним из известных киевских деятелей ML-тусовки — Андреем Никишаевым (легко ищется в фб если кому интересно). Тема была примерно такой — "Как подымать $10k в месяц без напряга". Андрей рассказывал довольно интересные вещи, в числе которых было следующее утверждение (примерно): "если ты будешь делать работу нормально, то таким образом уже будешь лучше чем 80% рынка". 

Не обязательно быть жоским олимпиадником и клобасить адовые алгоритмы, не обязательно иметь 10 лет опыта платформе/языке, для того чтобы просто делать работу нормально, качественно. "Нормально делай, нормально будет", вот это все. Большинство задач не требуют какой-то очень серьезной квалификации, наличия PhD или работы с супервысоконагруженными сервисами 300kk req/sec. Базовые концепции построения нормальных приложений довольно просты — не тупите с боттлнеками (чаще всего, базой), и все будет работать быстро и чётко. 

В целом я уже писал об этом в посте ["О сложности рабочих задач"](/post/work-tasks-complexity/). Но тут на проблему взглянул немного с другой стороны когда посетил конференцию AWS Dev Day. В числе прочих там был доклад "How Wikr's movile apps serves 5M users withi a serverless backend". Крутое название, правда? 5M пользователей, впечатляет. Не указано впрочем количество запросов в секунду, да и в итоге оказалось что приложение очень хорошо шардируется и за лямбдами у них торчит довольно толстая Аврора (которая на самом деле и является боттлнеком). Тестов ребята не пишут потому что стартап, инфру они держат в Cloudformation, деплоят вообще каким-то довольно изощренным способом. В итоге — никакого рокет саенса. Ребята просто сделали адекватное для своего продукта решение. Сходу можно было предъявить за кучу вещей которые являются моветоном, но.

СТО сделал доклад. В зале сидело человек 200-300. Задавали довольно много вопросов, и потом толпа людей подошла узнавать дополнительные детали уже в p2p формате.

Доклад простой, но, тем не менее, на эту простоту есть хороший спрос. Всего-то нужно решить задачу нормально, прикрутить пару баззвордов релевантных теме конфы, и вот пожалуйста — куча лайков, вопросы, известность. Хотя я уверен, что множество людей в зале решали точно такие же задачи. 

Можно конечно сказать что это днищеконфа, и типа вот приходите на HighLoad++ там вам пояснят с вертушки в щщи, но я думаю что бизнесу пофиг. Бизнесу нужен работающий продукт, нормально сделанный и нормально работающий. И для этого не нужно быть турбо-сеньером. Надо просто не тупить и делать нормально.

