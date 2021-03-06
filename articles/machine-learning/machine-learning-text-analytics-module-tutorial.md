<properties
	pageTitle="Создание моделей текстовой аналитики в Студии машинного обучения Azure | Microsoft Azure"
	description="Узнайте, как с помощью модулей для предварительной обработки текста, N-грамм и хэширования признаков создавать модели текстовой аналитики в Студии машинного обучения Azure."
	services="machine-learning"
	documentationCenter=""
	authors="rastala"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="roastala" />


#Создание моделей текстовой аналитики в Студии машинного обучения Azure

Машинное обучения Azure можно использовать для создания и ввода в эксплуатацию моделей текстовой аналитики. Эти модели могут быть полезными, например, при решении проблем с классификацией документов или анализом мнений.

В эксперименте текстовой аналитики, как правило, выполняются следующие действия:

 1. Очистка и предварительная обработка набора текстовых данных.
 2. Извлечение из предварительно обработанного текста векторов числовых признаков.
 3. Обучение модели классификации или регрессии.
 4. Оценка и проверка модели.
 5. Развертывание модели в рабочей среде.

В данном учебнике эти действия демонстрируются на примере модели анализа мнений с использованием набора данных Amazon Book Reviews (Обзоры книг на Amazon). См. исследовательскую работу "Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification" (Биографии, Болливуд, бум-боксы и блендеры: адаптация домена для классификации мнений пользователей), авторы — Джон Блитцер (John Blitzer), Марк Дредзе (Mark Dredze) и Фернандо Перейра (Fernando Pereira); Ассоциация компьютерной лингвистики (ACL), 2007 г. Этот набор данных состоит из оценок в обзоре (1-2 или 4-5) и текста в произвольной форме. Целью является прогнозирование оценки в обзоре: низкая (1-2) или высокая (4-5).

Эксперименты, рассматриваемые в этом учебнике, можно найти в коллекции Cortana Intelligence:

[Predict Book Reviews (Прогнозирование оценок в обзоре книг).](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Predict Book Reviews - Predictive Experiment (Прогнозирование оценок в обзоре книг — прогнозной эксперимент).](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## Шаг 1. Очистка и предварительная обработка набора текстовых данных

Мы начинаем эксперимент с разделения оценок в обзоре на категориальные контейнеры низких и высоких оценок, чтобы сформулировать проблему как двухклассовую классификацию. Для этого мы используем модули [Редактирование метаданных](https://msdn.microsoft.com/library/azure/dn905986.aspx) и [Значения категорий группы](https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Создание метки](./media/machine-learning-text-analytics-module-tutorial/create-label.png)

Затем мы выполняем очистку текста с помощью модуля [Preprocess Text](https://msdn.microsoft.com/library/azure/mt762915.aspx) (Предварительная обработка текста). Очистка снижает количество шумов в наборе данных, помогает найти наиболее важные признаки и повысить точность конечной модели. Удаляются стоп-слова (такие как артикли или частицы), числа, специальные знаки, повторяющиеся знаки, адреса электронной почты и URL-адреса. Также текст преобразовывается в нижний регистр, выполняется лемматизация слов и определяются границы предложений, которые затем обозначаются в предварительно обработанном тексте символом |||.

![Предварительная обработка текста](./media/machine-learning-text-analytics-module-tutorial/preprocess-text.png)

Можно ли использовать настраиваемый список стоп-слов? Его можно передать в качестве дополнительных входных данных. Также можно использовать настраиваемые регулярные выражения с синтаксисом C# для замены подстрок и удаления слов по частям речи (существительные, глаголы или прилагательные).

Когда предварительная обработка завершена, мы разделяем данные на наборы для обучения и тестирования.

## Шаг 2. Извлечение из предварительно обработанного текста векторов числовых признаков

Чтобы создать модель из текстовых данных, как правило, требуется преобразовать произвольный текст в векторы числовых признаков. В этом примере для преобразования текстовых данных в такой формат используется модуль [Extract N-Gram Features from Text](https://msdn.microsoft.com/library/azure/mt762916.aspx) (Извлечение из текста признаков N-грамм). Этот модуль принимает столбец слов, разделенных пробелами, и вычисляет словарь слов, или N-граммы слов, которые отображаются в наборе данных. Затем модуль подсчитывает, сколько раз каждое слово, или N-грамм, встречается в каждой записи, и создает на основании этих подсчетов векторы признаков. В этом учебнике для N-грамм задано значение 2, поэтому наши векторы признаков включают отдельные слова и сочетания из двух последовательных слов.

![Извлечение N-грамм](./media/machine-learning-text-analytics-module-tutorial/extract-ngrams.png)

При подсчете N-грамм применяются взвешенные значения TF*IDF (частота условия — инверсная частота в документе). Этот подход добавляет взвешенные значения слов, которые часто встречаются в одной записи, но редко — по всему набору данных. Другие варианты включают двоичные значения, TF и взвешенные значения диаграммы.

Такие текстовые признаки часто обладают высокой размерностью. Например, если ваш текст состоит из 100 000 уникальных слов, то пространство признаков будет иметь 100 000 размеров, или даже больше (когда используются N-граммы). Модуль "Extract N-Gram Features from Text" (Извлечение из текста признаков N-грамм) предоставляет набор параметров для уменьшения размерности. Можно исключить слова, которые являются короткими, длинными, либо встречаются слишком редко или часто, чтобы влиять на прогнозное значение. В этом учебнике мы исключаем N-граммы, которые встречаются реже чем в 5 записях или чаще чем в 80 % записей.

Вы также можете использовать выбор признаков, чтобы отбирались только те признаки, которые максимально связаны с целью прогноза. Мы используем выбор признаков хи-квадрат, чтобы отобрать 1000 признаков. Словарь выбранных слов или N-грамм можно просмотреть, щелкнув правую часть выходных данных модуля "Extract N-Gram Features from Text" (Извлечение из текста признаков N-грамм).

В качестве альтернативы этому модулю можно использовать модуль "Функции хэширования". Однако имейте в виду, что модуль [Функции хэширования](https://msdn.microsoft.com/library/azure/dn906018.aspx) не имеет встроенной функции выбора признаков или взвешенных значений TF*IDF.

## Шаг 3. Обучение модели классификации или регрессии

Итак, текст преобразован в столбцы числовых признаков. Набор данных по-прежнему содержит строковые столбцы из предыдущих шагов, поэтому для их исключения мы используем модуль "Select Columns in Dataset" (Выбор столбцов в наборе данных).

Затем мы используем [двухклассовую логистическую регрессию](https://msdn.microsoft.com/library/azure/dn905994.aspx) для прогнозирования цели: высокая или низкая оценка в обзоре. На этом этапе задача текстовой аналитики преобразовывается в обычную задачу классификации. Для улучшения модели можно воспользоваться инструментами, доступными в Машинном обучении Azure. Например, можно поэкспериментировать с разными классификаторами, чтобы узнать, насколько точные результаты они предоставляют, или воспользоваться настройкой гиперпараметров для повышения точности.

![Обучение и оценка](./media/machine-learning-text-analytics-module-tutorial/scoring-text.png)

## Шаг 4. Оценка и проверка модели

Как проверить обученную модель? Мы оцениваем ее на основе тестового набора данных и анализируем точность. Однако модель усвоила словарь N-грамм и их взвешенные значения из набора данных для обучения. Поэтому при извлечении признаков из тестовых данных мы должны использовать этот словарь и эти взвешенные значения, а не создавать словарь заново. Следовательно, мы добавляем модуль "Extract N-Gram Features from Text" (Извлечение из текста признаков N-грамм) в ветвь оценки эксперимента, подключаем выходной словарь из ветви обучения и выбираем режим словаря "только для чтения". Мы также отключаем фильтрацию N-грамм по частоте, задав минимальное значение — 1 экземпляр, а максимальное значение — 100 %, и отключаем выбор признаков.

После преобразования текстового столбца в тестовых данных в столбцы числовых признаков мы исключаем строковые столбцы из предыдущих шагов, как в ветви обучения. Затем мы используем модуль "Score Model" (Оценка модели) для выполнения прогнозов и модуль "Evaluate Model" (Анализ модели) для оценки точности.

## Шаг 5. Развертывание модели в рабочей среде

Модель почти готова к развертыванию в рабочей среде. Если модель развернута как веб-служба, то в качестве входных данных она принимает строку с текстом в произвольной форме, а возвращает прогнозную оценку — "высокая" или "низкая". Она использует усвоенный словарь N-грамм для преобразования текста в признаки, а обученную модель логистической регрессии — для формирования прогноза на основе этих признаков.

Чтобы настроить прогнозный эксперимент, сначала необходимо сохранить словарь N-грамм как набор данных, а также обученную модель логистической регрессии из ветви обучения эксперимента. Затем мы сохраняем эксперимент с помощью команды "Сохранить как", чтобы создать диаграмму эксперимента для прогнозного эксперимента. Мы удаляем из эксперимента модуль "Split Data" (Разделение данных) и ветвь обучения. Затем мы подключаем сохраненные ранее словарь N-грамм и модель к модулям "Extract N-Gram Features from Text" (Извлечение из текста признаков N-грамм) и "Score Model" (Оценка модели) соответственно. Кроме того, мы удаляем модуль "Evaluate Model" (Анализ модели).

Вставляем модуль "Select Columns in Dataset" (Выбор столбцов в наборе данных) перед модулем "Preprocess Text" (Предварительная обработка текста), чтобы удалить столбец меток, и снимаем флажок "Append score column to dataset" (Добавить в набор данных столбец оценок) в модуле "Score Model" (Оценка модели). Таким образом, веб-служба не запрашивает метку, которую она пытается прогнозировать, и в результате входные признаки не выводятся на экран.

![Прогнозный эксперимент](./media/machine-learning-text-analytics-module-tutorial/predictive-text.png)

Теперь у нас готов эксперимент, который можно опубликовать как веб-службу и вызывать с помощью интерфейсов API "запрос-ответ" или пакетного выполнения.

## Дальнейшие действия

Дополнительные сведения о модулях текстовой аналитики см. в [документации MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

<!---HONumber=AcomDC_0914_2016-->