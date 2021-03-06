<properties 
    pageTitle="Распространенные примеры использования DocumentDB | Microsoft Azure" 
    description="Изучите пять вариантов использования DocumentDB: созданное пользователями содержимое, ведение журнала событий, данные каталога, данные настроек пользователей и Интернет вещей (IoT)." 
    services="documentdb" 
    authors="h0n" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="hawong"/>

# Распространенные примеры использования DocumentDB
В этой статье представлен обзор нескольких распространенных примеров использования DocumentDB. Рекомендации в данной статье служат отправной точкой для разработки приложений с использованием DocumentDB.

Ознакомившись с данной статьей, вы сможете ответить на следующие вопросы.
 
- Каковы типичные примеры использования DocumentDB?
- Каковы преимущества использования DocumentDB в качестве созданного пользователем хранилища контента?
- Каковы преимущества использования DocumentDB в качестве хранилища данных каталога?
- Каковы преимущества использования DocumentDB в качестве хранилища журналов событий?
- Каковы преимущества использования DocumentDB в качестве хранилища настроек пользователей?
- Каковы преимущества использования DocumentDB в качестве хранилища данных для систем Интернета вещей (IoT)?

## Типичные варианты использования для DocumentDB
Azure DocumentDB — это база данных NoSQL общего назначения, которая используется для самых разных приложений и вариантов использования. Она хорошо подходит для любого приложения, которому требуется время ответа, измеряемое в миллисекундах, и возможность быстрого масштабирования. Ниже приведены некоторые особенности DocumentDB, благодаря которым эта система отлично подходит для высокопроизводительных приложений.

- DocumentDB изначально секционирует данные для обеспечения высокой доступности и масштабируемости.
- DocumentDB имеет хранилище на базе SSD с низкой задержкой и временем ответа, измеряемым в миллисекундах.
- Поддержка в DocumentDB уровней согласованности, таких как bounded-staleness (ограниченная), session (уровень сеанса) и eventual (согласованная в конечном счете), обеспечивает привлекательное соотношение затрат и производительности.
- DocumentDB имеет гибкую и понятную модель ценообразования, в которой место для хранения и пропускная способность измеряются независимо.
- Модель зарезервированной пропускной способности DocumentDB позволяет вести учет по количеству операций чтения и записи, а не по ресурсам ЦП, памяти и операций ввода-вывода базового оборудования.
- Структура DocumentDB допускает масштабирование для огромного числа запросов, измеряемого миллиардами запросов в день.

Эти особенности становятся особенно полезными при работе с веб-приложениями, мобильными устройствами, играми и приложениями IoT, которым требуется малое время отклика и возможность обрабатывать большие объемы операций чтения и записи.

## Создаваемое пользователями содержимое
DocumentDB часто используется для хранения и запроса созданного пользователями содержимого для мобильных и веб-приложений, особенно для приложений социальных сетей. Вот некоторые примеры созданного пользователями содержимого: чаты, твиты, сообщения в блогах, оценки и комментарии. Часто созданное пользователями содержимое — это смесь произвольного текста, свойств, тегов и связей, которые не ограничены жесткой структурой.

Содержимое, например чаты, комментарии и сообщения, могут храниться в DocumentDB без преобразования или сложного объекта для уровней реляционного сопоставления. Свойства данных можно легко добавлять и изменять в соответствии с требованиями по мере того, как разработчики меняют код приложения, что ускоряет разработку.

Приложений, которые интегрируются с различными социальными сетями, должны реагировать на изменение схем этих сетей. Так как в DocumentDB по умолчанию данные индексируются автоматически, они готовы к запросам в любое время. Следовательно, эти приложения могут получать проекции для своих нужд.

Многие социальные приложения работают в глобальном масштабе и могут сталкиваться с непредсказуемыми закономерностями использования. Гибкие возможности масштабирования хранилища данных очень важны при масштабировании уровня приложения в соответствии с потребностями. Можно добавить дополнительные секции данных для учетной записи DocumentDB. Кроме того, можно создать дополнительные учетные записи DocumentDB в нескольких регионах. Сведения о доступности регионов службы DocumentDB см. в разделе [Регионы Azure](https://azure.microsoft.com/regions/#services).

## Данные каталога
Сценарии использования данных каталога включают в себя хранение и запрос набора атрибутов для сущностей, например людей, расположений и продуктов. Вот некоторые примеры данных каталога: учетные записи пользователей, каталоги продуктов, реестры устройств для IoT и системы учета запасов. Атрибуты этих данных могут отличаться и изменяться со временем в соответствии с требованиями приложения.

Рассмотрим пример каталога товаров для поставщика автомобильных запчастей. У каждого компонента могут быть собственные атрибуты, помимо общих атрибутов всех деталей. Кроме того, атрибуты для определенной запчасти могут измениться в следующем году после выпуска новой модели. DocumentDB как хранилище документов JSON поддерживает гибкие схемы и позволяет представлять данные с помощью вложенных свойств, что идеально подходит для хранения данных каталога продукции.

## Ведение журнала и данные временных рядов
Журналы приложений часто достигают больших объемов и могут иметь различные атрибуты в зависимости от версии развернутого приложения или событий ведения журнала компонента. Данные журнала не ограничены сложными связями или жесткой структурой. Все чаще данные журнала хранятся в формате JSON, так как он удобен для чтения, а файлы не занимают много места.
   
С данными журналов событий связаны два основных варианта использования. Первый вариант — выполнение специализированных запросов с подмножеством данных для устранения неполадок. Во время устранения неполадок подмножество данных сначала извлекается из журналов, обычно в виде временных рядов. Затем выполняется детализация за счет фильтрации набора данных с использованием уровней ошибок или сообщений об ошибках. Здесь устанавливается преимущество хранения журналов событий в DocumentDB. В DocumentDB по умолчанию данные журналов индексируются автоматически, поэтому они готовы к запросам в любое время. Кроме того, данные журнала могут сохраняться в секциях данных как временные ряды. Старые журналы можно переместить в "холодное" хранилище в соответствии с политикой хранения.

Второй вариант использования — выполнение длительных заданий аналитики в автономном режиме с большим объемом данных журналов. Вот некоторые примеры: анализ доступности сервера, анализ ошибок приложения и анализ данных навигации. Как правило, для выполнения этих типов анализа используется Hadoop. С соединителем Hadoop для DocumentDB база данных DocumentDB служит источником и приемником данных для заданий Pig, Hive и Map/Reduce. Дополнительные сведения о соединителе Hadoop для DocumentDB см. в разделе [Выполнение задания Hadoop с помощью DocumentDB и HDInsight](documentdb-run-hadoop-with-hdinsight.md).

## Игры
Категория базы данных является критически важным компонентом для игровых приложений. Современные игры обрабатывают графику на клиентах мобильных устройств или консолей, однако получают из облака настраиваемое и персонализированное содержимое, например игровую статистику, интеграцию с социальными сетями и списки лидеров по набранным баллам. Для оперативного взаимодействия с пользователями играм требуются крайне низкие задержки для операций чтения и записи, а категория базы данных должна успешно справляться с пиковой и заниженной частотой запросов во время запусков новых игр и обновлений компонентов.

DocumentDB используется в крупномасштабных играх, таких как [The Walking Dead: No Man's Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) от [Next Games](http://www.nextgames.com/) и [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). В обоих случаях основные преимущества DocumentDB заключались в следующем:

- DocumentDB позволяет гибко масштабировать производительность. Это позволяет играм обновлять профили и статистику для как малого, так и большого числа одновременных игроков с помощью одного вызова API.
- DocumentDB поддерживает операции чтения и записи, занимающие считанные миллисекунды, что помогает избежать любой из задержек во время игры.
- Автоматическое индексирование DocumentDB обеспечивает фильтрацию по нескольким разным свойствам в режиме реального времени, например: можно найти игроков по внутренним идентификаторам или идентификаторам GameCenter, Facebook, Google либо выполнить запрос, учитывающий членство игрока в гильдии. Это возможно без создания сложной инфраструктуры сегментирования или индексирования.
- Социальные функции, включая внутриигровой обмен сообщениями, членство в гильдии, пройденные испытания, списки лидеров по набранным баллам и профили в социальных сетях, проще реализовать благодаря гибкой схеме.
- DocumentDB, как управляемая платформа как услуга (PaaS), требует минимальной настройки и управления для обеспечения быстрой доработки и ускорения выхода на рынок.


## Данные настроек пользователя
В настоящее время мобильные и веб-приложения используют сложные представления и функции. Они обычно динамически адаптируются к предпочтениям или настроению пользователя и потребностям фирменной символики. Таким образом приложения должны иметь возможность эффективного извлекать персональные настройки, чтобы быстро отображать элементы пользовательского интерфейса и функции.

JSON — это эффективный формат для представления данных макета пользовательского интерфейса, так как он не только прост, но и легко интерпретируется JavaScript. DocumentDB предлагает настраиваемые уровни согласованности, обеспечивающие быстрое чтение и запись. Поэтому хранение данных макета пользовательского интерфейса, включая личные настройки, в виде документов JSON в DocumentDB — эффективный способ получать эти данные.

## IoT и данные с датчиков устройств
Варианты использования Интернета вещей (IoT) часто характеризуются одинаковым способом приема, обработки и хранения данных. Во-первых, эти системы позволяют принимать большие объемы данных от датчиков с различными языковыми стандартами. Затем эти системы обрабатывают и анализируют потоковые данные для получения полезных сведений в реальном времени. Кроме того, большая часть данных, если не все, в итоге попадут хранилище данных для специализированных запросов и автономной аналитики.

Microsoft Azure предлагает мощные службы, которые можно использовать для IoT. Службы Azure IoT представляют собой набор служб, таких как концентраторы событий Azure, Azure DocumentDB, служба потоковой аналитики Azure, центр уведомлений Azure, служба машинного обучения Azure HDInsight и PowerBI.

Пакеты данных могут принимать концентраторы событий Azure, которые обеспечивают высокую пропускную способность с низкой задержкой. Принятые данные, которые необходимо обработать для анализа в реальном времени, можно туннелировать службе потоковой аналитики Azure. Данные могут быть загружены в DocumentDB для специальных запросов. После загрузки данных в DocumentDB данные готовы к запросам. Данные в DocumentDB можно использовать как справочные данные в ходе анализа в реальном времени. Кроме того, данные можно уточнять и обрабатывать, подключая данные DocumentDB к HDInsight для заданий Pig, Hive и Map/Reduce. Обработанные данные затем загружаются обратно в DocumentDB для создания отчетов.

Пример решения IoT, использующего DocumentDB, концентраторы событий и Storm, см. в [hdinsight-storm-examples на портале GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Дополнительные сведения о предложениях Azure для IoT см. в разделе [Создание Интернета вещей](http://www.microsoft.com/ru-RU/server-cloud/internet-of-things.aspx).

## Дальнейшие действия
 
Чтобы начать работу с DocumentDB, можно создать [учетную запись](https://azure.microsoft.com/pricing/free-trial/) и следовать [схеме обучения](https://azure.microsoft.com/documentation/learning-paths/documentdb/), чтобы узнать о DocumentDB и находить нужную информацию.

Или, если вы хотите узнать больше о клиентах, использующих DocumentDB, прочитайте следующие истории:

- [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Игра "№Walking Dead: No Man's Land" добралась до первого места при поддержке Azure DocumentDB.
- [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Реализация игрового процесса с социальными функциями в Halo 5 с помощью Azure DocumentDB.
- [Коллекция Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Коллекция Cortana Analytics — это масштабируемый сайт сообщества, созданный на базе Azure DocumentDB.
- [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Ведущий системный интегратор предоставляет многонациональным компаниям глобальные сведения с помощью гибких облачных технологий.
- [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Добавление аналитики в новости для предоставления информации заинтересованным гражданам.
- [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Крупные мировые компании обращаются к SGS за услугами. А компания SGS обращается к Azure.
- [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Глобальный лидер Telenor использует облако для перемещения с высочайшей скоростью.
- [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Хранилище будущего основано на быстром поиске и простом потоке данных.

<!---HONumber=AcomDC_0713_2016-->