<properties
   pageTitle="5 вопросов, на которые дают ответ обработка и анализ данных — Обработка и анализ данных для начинающих | Microsoft Azure"
   description="Ознакомьтесь с кратким введением ";Обработка и анализ данных для начинающих";. Это пять коротких видео, первое из которых — ";5 вопросов, на которые дают ответ обработка и анализ данных";."
   keywords="выполнение обработки и анализа данных,введение в обработку и анализ данных,обработка и анализ данных для начинающих,типы вопросов,вопросы по обработке и анализу данных,алгоритмы обработки и анализа данных"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="cgronlun;brohrer;garye"/>

# Обработка и анализ данных для начинающих (видео 1): 5 вопросов, на которые дают ответ обработка и анализ данных

Ознакомьтесь с кратким введением *Обработка и анализ данных для начинающих*, которое состоит из пяти коротких видео. Эти видеоролики будут полезны для тех, кто интересуется обработкой и анализом данных или работает с людьми, занятыми в сфере обработки и анализа данных, и желает начать с базовых понятий.

Первое видео посвящено типам вопросов, на которые можно ответить с помощью обработки и анализа данных. Обработка и анализ данных прогнозируют ответы на вопросы, используя число или категорию. Для получения оптимального результата просмотрите все видео в указанном порядке. [Перейти к списку видео](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-the-5-questions-data-science-answers]

## Другие видео из этого цикла

*Обработка и анализ данных для начинающих* — это пять коротких видеороликов об основах обработки и анализа данных, которые займут у вас не более 25 минут. Ознакомьтесь с другими 4 видео:

  * Видео 1. 5 вопросов, на которые дают ответ обработка и анализ данных.
  * Видео 2. [Готовы ли ваши данные к обработке и анализу?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 мин 56 с)*
  * Видео 3. [Задайте вопрос, на который можно ответить с помощью данных](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 мин 17 с)*
  * Видео 4. [Прогнозирование ответа с помощью простой модели](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 мин 42 с)*
  * Видео 5. [Копирование работы других пользователей для обработки и анализа данных](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 мин 18 с)*

## Расшифровка видео "5 вопросов, на которые дают ответ обработка и анализ данных"

Привет! Добро пожаловать в цикл видеороликов *Обработка и анализ данных для начинающих*.

Обработка и анализ данных могут быть непростой задачей, поэтому я расскажу о базовых понятиях в этой области, без каких-либо формул или компьютерного жаргона, понятного только программистам.

Это первое видео называется "5 вопросов, на которые дают ответ обработка и анализ данных".

Для прогнозирования ответов на вопросы функция обработки и анализа данных использует числа и имена (также известные как категории или метки).

Возможно, вас это удивит, но *существует только пять вопросов, на которые обработка и анализ данных дают ответ*:

  * Это A или B?
  * Является ли это странным?
  * Сколько?
  * Как это организовано?
  * Что делать дальше?

  Для ответа на каждый из этих вопросов используется отдельная группа методов машинного обучения, называемых алгоритмами.


Чтобы лучше понять, представьте, что алгоритм — это рецепт блюда, а данные — это ингредиенты для его приготовления. Алгоритм указывает, как объединить и смешать данные, чтобы получить ответ. Компьютер можно сравнить с миксером. Большую часть тяжелой работы алгоритма компьютер делает за вас, и делает это довольно быстро.

## Вопрос 1 "Это A или B?" использует алгоритмы классификации

Давайте начнем с вопроса "Это A или B?"

![Алгоритмы классификации: "Это A или B?"](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-classification-algorithms.png)

Эта группа алгоритмов называется двуклассовой классификацией.

Ее удобно использовать для вопросов, которые имеют только два возможных варианта ответа.

Например:

  *	Выйдет ли из строя эта шина на следующих 1000 км: да или нет?
  *	Какой купон привлекает больше клиентов: купон на 5 долларов или на скидку в 25 %?

Этот вопрос также можно перефразировать, чтобы он включал более двух вариантов ответа: "Это A, B, C или D, и т. д.?" Это называется классификацией по нескольким классам. Ее удобно использовать, когда допускается несколько (или несколько тысяч) возможных вариантов ответа. Классификация по нескольким классам выбирает наиболее вероятный вариант.

## Вопрос 2 "Является ли это странным?" использует алгоритмы обнаружения аномалий

Следующий вопрос, на который дают ответ обработка и анализ данных: "Является ли это странным?" Для ответа на этот вопрос используется группа алгоритмов, называемых обнаружением аномалий.

![Алгоритмы обнаружения аномалий: "Является ли это странным?"](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-anomaly-detection-algorithms.png)


Если у вас есть кредитная карта, то вы уже пользовались функцией обнаружения аномалий. Компания (банк), обслуживающая кредитную карту, анализирует ваши стандартные покупки и может предупредить вас о возможной попытке мошенничества. "Странными" расходами могут, например, считаться покупки в магазине, где вы обычно не бываете, или покупка очень дорогостоящего товара.

Этот тип вопроса может быть полезным во многих ситуациях. например

  *	Если ваш автомобиль оснащен манометром, возможно, вы хотите знать, корректно ли он считывает данные о давлении.
  *	Если вы ведете наблюдение за интернет-трафиком, возможно, вы хотите знать, является ли типичным это сообщение.

Обнаружение аномалий сигнализирует о неожиданных событиях или необычном поведении. А также подсказывает, где могут быть проблемы.



## Вопрос 3 "Сколько?" использует алгоритмы регрессии

Служба машинного обучения также может прогнозировать ответ на вопрос "Сколько?" Группа алгоритмов, отвечающая на этот вопрос, называется регрессией.

![Алгоритмы регрессии: "Сколько?"](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-regression-algorithms.png)


Алгоритмы регрессии делают числовые прогнозы, такие как в приведенных ниже примерах.

  *	Какой будет температура в следующий вторник?
  *	Каким будет объем продаж за четвертый квартал?

Эти алгоритмы помогают ответить на любой вопрос, запрашивающий число.

## Вопрос 4 "Как это организовано?" использует алгоритмы кластеризации

Последние два вопроса немного сложней.

Иногда требуется понять структуру набора данных, то есть задать вопрос "Как это организовано?" Для этого типа вопросов не существует примеров с заготовленными вариантами ответов.

Существует множество способов выявления структуры данных. Одним из способов является кластеризация. Она разделяет данные на естественные группы, чтобы упростить их интерпретацию. При использовании кластеризации нет единого правильного ответа.

![Алгоритмы кластеризации: "Как это организовано?"](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-clustering-algorithms.png)

Ниже приводятся распространенные примеры вопросов с кластеризацией.

  *	Какие зрители предпочитают те же типы фильмов?
  *	Какие модели принтеров выходят из строя аналогичным образом?

Поняв, как организованы данные, вы сможете лучше понять и спрогнозировать варианты поведения и событий.

## Вопрос 5 "Что делать дальше?" использует алгоритмы обучения с подкреплением

Последний вопрос – "Что делать дальше?" — использует группу алгоритмов, которая называется обучением с подкреплением.

Принцип обучения с подкреплением основан на том, как реагирует мозг крысы и человека на наказание и поощрение. Эти алгоритмы делают выводы на основе полученных результатов, а затем принимают решение о дальнейших действиях.

Как правило, обучение с подкреплением подходит для автоматизированных систем, которые должны принимать много мелких решений без вмешательства человека.

![Алгоритмы обучения с подкреплением: "Что делать дальше?"](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-reinforcement-learning-algorithms.png)

Ответом на вопрос всегда является то, какое действие необходимо выполнить. Обычно это относится к машине или роботу. Примеры

  *	Для системы климат-контроля дома: изменить температуру или оставить как есть?
  *	Для автомобиля с автономным управлением: при желтом сигнале светофора затормозить или ускориться?
  *	Для робота-пылесоса: продолжить уборку или вернуться к зарядной станции?

Алгоритмы обучения с подкреплением собирают данные в процессе работы, обучаясь методом проб и ошибок.

Готово! Мы ознакомились с видео "5 вопросов, на которые дают ответ обработка и анализ данных".



## Дальнейшие действия

  * [Выполните свой первый эксперимент по обработке и анализу данных с использованием Машинного обучения Azure.](machine-learning-create-experiment.md)
  * [Ознакомьтесь с введением в машинное обучение в Microsoft Azure.](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->