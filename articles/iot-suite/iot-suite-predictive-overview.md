<properties
 pageTitle="Предварительно настроенное решение прогнозируемого обслуживания | Microsoft Azure"
 description="Описание предварительно настроенного решения прогнозируемого обслуживания IoT Azure | Microsoft Azure"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# Обзор предварительно настроенного решения прогнозируемого обслуживания

Предварительно настроенное решение *прогнозируемого обслуживания* является одним из [предварительно настроенных решений][lnk_preconfigured_solutions], выпущенных как часть [пакета IoT Microsoft Azure][lnk_iot_suite]. Это решение интегрирует сбор данных телеметрии устройства в режиме реального времени и прогнозируемую модель, созданные с помощью [машинного обучения Azure][lnk_machine_learning].


С Azure IoT Suite организации могут быстро и легко подключаться к ресурсам и отслеживать их, а также анализировать данные в режиме реального времени. Предварительно настроенное решение прогнозируемого обслуживания принимает данные и использует многофункциональные панели мониторинга и визуализации для предоставления компаниям новой аналитики, которая может повысить эффективность и увеличить доход.

## Сценарий

Компания Fabrikam является региональной авиалинией, которая концентрируется на хорошем впечатлении клиентов по приемлемым ценам. Одной из причин задержки рейсов являются проблемы обслуживания, в частности, особенно сложной задачей является обслуживание двигателей самолетов. Сбоя двигателя во время полета следует избегать любой ценой, поэтому Fabrikam регулярно проверяет двигатели и придерживается программы запланированного обслуживания. Однако износ двигателей самолетов не всегда одинаков. В некоторых случаях выполняется ненужное обслуживание двигателей. Что более важно, возникают проблемы, которые могут задержать самолет на земле до проведения обслуживания. Это приводит к дорогостоящим задержкам, особенно в том случае, когда самолет находится в месте, где недоступны нужные специалисты или запасные части.

Самолеты компании Fabrikam снабжены датчиками, которые отслеживают состояние двигателей во время полета. Fabrikam использует пакет Azure IoT для сбора данных датчиков, собранных во время полета. После нескольких лет накопления данных о работе и сбоях двигателей специалисты по анализу данных Fabrikam смоделировали способ прогнозирования оставшегося полезного срока службы (RUL) двигателя самолета. Они обнаружили корреляцию между данными четырех датчиков двигателя и износом двигателя, который в конечном счете приводит к сбою. Хотя компания Fabrikam продолжает выполнение регулярных проверок, чтобы обеспечить безопасность, она теперь может использовать модели для вычисления RUL каждого двигателя после каждого рейса, используя данные телеметрии, собранные на двигателях во время полета. Fabrikam теперь может прогнозировать будущие точки сбоя и планировать обслуживание и ремонт заранее.

> [AZURE.NOTE] Модель решений использует данные о фактическом износе двигателя.

Получив возможность прогнозировать момент, когда понадобится обслуживание, Fabrikam может оптимизировать операции для снижения затрат. Координаторы обслуживания работают с планировщиками, чтобы запланировать обслуживание, совпадающее с остановкой самолета в определенном месте, и обеспечить достаточное время для обслуживания самолета, не вызывая перебоев в расписании. Fabrikam может запланировать работу специалистов соответствующим образом — таким образом самолеты обслуживаются эффективно и без времени ожидания. Менеджеры снабжения получают планы обслуживания, чтобы оптимизировать процесс размещения заказов и хранение запасных частей. Все это позволяет Fabrikam свести к минимуму время, проведенное самолетом на земле, и снизить эксплуатационные расходы, одновременно обеспечивая безопасность пассажиров и сотрудников.

Чтобы понять, как [пакет Azure IoT][lnk_iot_suite] предоставляет возможности, необходимые клиентам для реализации потенциала прогнозируемого обслуживания, просмотрите эту [инфографику][lnk_infographic].

## Принцип построения решения прогнозируемого обслуживания

Чтобы продемонстрировать эти возможности на основе данных телеметрии устройства, собранных с помощью служб пакета IoT, решение использует существующую модель машинного обучения Azure, доступную в виде шаблона. Корпорация Майкрософт разработала [модель регрессии][lnk_regression_model] двигателя самолета и опубликовала полный шаблон <sup>[1]</sup>, данные и пошаговое руководство по использованию модели.

Предварительно настроенное решение прогнозного обслуживания Azure IoT использует модель регрессии, созданную на основе этого шаблона. Это решение развертывается в подписке Azure и предоставляется с помощью автоматически созданного интерфейса API. Решение включает набор проверочных данных, представляющий 4 (из 100) двигателя и 4 (из 21) потока данных датчиков, который обеспечивает точный результат из обученной модели.

*[1] А. Саксена (A. Saxena) и К. Гебель (K. Goebel) (2008). "Набор данных для симуляции деградации турбореактивного двигателя" ("Turbofan Engine Degradation Simulation Data Set"), Хранилище данных Центра прогнозирования Эймса (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/, Исследовательский центр Эймса, Моффетт-филд, Калифорния*

## Дальнейшие действия

Дополнительные сведения о том, как Azure IoT реализует сценарии прогнозирования обслуживания, см. в разделе [Захват значения из Интернета вещей][lnk_capture_value].

Воспользуйтесь [пошаговым руководством][lnk-predictive-walkthrough] по работе с предварительно настроенным решением прогнозируемого обслуживания.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Вы можете также ознакомиться с другими функциями и возможностями предварительно настроенных решений IoT Suite.

- [Часто задаваемые вопросы об IoT Suite][lnk-faq]
- [Все аспекты безопасности IoT][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md

<!---HONumber=AcomDC_0817_2016-->