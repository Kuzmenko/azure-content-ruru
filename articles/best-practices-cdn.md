<properties
   pageTitle="Руководство по сети доставки содержимого (CDN) | Microsoft Azure"
   description="Рекомендации по сети доставки содержимого (CDN) для обеспечения доставки содержимого, размещенного в Azure, по сети с высокой пропускной способностью."
   services="cdn"
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/17/2016"
   ms.author="masashin"/>

# Руководство по сети доставки содержимого (CDN)

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## Обзор
Сеть доставки содержимого (CDN) Microsoft Azure предлагает разработчикам глобальное решение по доставке содержимого, размещенного в Azure или любом другом расположении, по сети с высокой пропускной способностью. Используя CDN, вы можете кэшировать общедоступные объекты, загруженные из хранилища BLOB-объектов, веб-приложения, виртуальной машины, папки приложения или другого расположения HTTP или HTTPS. Кэш CDN может храниться в стратегически важных местах для обеспечения максимальной пропускной способности при доставке содержимого пользователям. CDN обычно используется для доставки статического содержимого, например изображений, таблиц стилей, документов, файлов, клиентских скриптов и HTML-страниц.

CDN можно также использовать как кэш для обслуживания динамического содержимого, например отчетов в формате PDF или графа на основе указанных входных данных. Если одни и те же входные значения предоставляются разными пользователями, выходные данные должны быть одинаковыми.

Основные преимущества использования CDN: небольшая задержка и ускоренная доставка содержимого пользователям независимо от их географического расположения относительно центра обработки данных, в котором размещено приложение.

![Схема сети CDN](./media/best-practices-cdn/CDN.png)

Использование CDN также позволяет снизить нагрузку на приложение благодаря отсутствию необходимости выполнять обработку, необходимую для доступа к содержимому и его доставки. Уменьшение нагрузки позволяет повысить производительность и масштабируемость приложения, а также минимизировать затраты на размещение путем сокращения объема вычислительных ресурсов, необходимых для получения определенного уровня производительности и доступности.

## Как и для чего используется CDN

К типичным случаям использования CDN относятся:

+ Доставка статических ресурсов для клиентских приложений, часто с веб-сайта. Это могут быть изображения, таблицы стилей, документы, файлы, клиентские скрипты, HTML-страницы, фрагменты HTML или любое другое содержимое, которое серверу не нужно изменять по каждому запросу. Приложение может создавать элементы во время выполнения и предоставлять их в сеть CDN (например, путем создания списка текущих новостей), но оно делает это не для каждого запроса.

+ Доставка общедоступного статического и общего содержимого на устройства, например мобильные телефоны и планшетные компьютеры. Само приложение является веб-службой, которая предоставляет доступ к API клиентам, запущенным на разных устройствах. CDN также может доставлять статические наборы данных (через веб-службу) для использования клиентами, в том числе для создания пользовательского интерфейса клиента. Например, CDN можно использовать для распределения документов JSON или XML.

+ Предоставление веб-сайтов целиком (при условии, что они состоят только из общедоступного статического содержимого) клиентам без необходимости использовать выделенные вычислительные ресурсы.

+ Потоковая передача видеофайлов в клиент по запросу. Видео передаются с низкой задержкой и по надежному подключению, доступному из центров обработки данных, расположенных по всему миру, которые предлагают подключения к сети CDN.

+ Оптимизация взаимодействия для пользователей, особенно для тех, кто находится далеко от центра обработки, в котором размещено приложение. В противном случае такие пользователи могут испытывать большие задержки. Большой объем содержимого в веб-приложении часто является статическим, а использование CDN поможет поддерживать производительность и общее качество взаимодействия с пользователями на нужном уровне, исключая при этом необходимость развертывания приложения в нескольких центрах обработки данных.

+ Обработка возрастающих нагрузок в приложениях с поддержкой решений IoT (Интернет вещей). Большое количество таких устройств может легко перегрузить приложение, если оно используется для обработки широковещательных сообщений и управления обновлением встроенного ПО непосредственно для каждого устройства.

+ Обработка пиковых нагрузок и бросков напряжения в использовании без масштабирования приложения и увеличения последующих затрат на его работу. Например, при выпуске обновления операционной системы для оборудования (например, определенной модели маршрутизатора) либо пользовательского устройства (например, смарт-ТВ), возникает пиковое количество запросов, ведь оно скачивается миллионами пользователей и устройств за короткий период времени.

В следующем списке показаны примеры медианы времени до получения первого байта из разных географических расположений. Целевая веб-роль развертывается в регионе "Запад США" Azure. Чем ближе приложение к узлу CDN, тем значительнее ускорение работы от использования этой сети. Полный список расположений узлов Azure CDN доступен на странице [Расположения узлов сети доставки содержимого Azure](./cdn/cdn-pop-locations.md/).


|| Время (мс) до получения первого байта (источник) | Время (мс) до первого (CDN) |Улучшение времени CDN (%)|
|-------------|------------------------|--------------------|------------------|
|*Сан-Хосе, Калифорния| 47,5 | 46,5 | 2 % |
|**Даллес, Вирджиния| 109 | 40,5 | 169% |
|Буэнос-Айрес, Аргентина| 210 | 151 | 39%|
|*Лондон, Великобритания| 195 | 44 | 343%|
|Шанхай, Китай| 242 | 206 | 17% |
|*Сингапур | 214 | 74 | 189 % |
|*Токио, Япония | 163 | 48 | 204 % |
|Сеул, Корея| 190 | 190 | 0% |


\* Узел Azure CDN расположен в одном городе. 
\*\* Узел Azure CDN расположен в соседнем городе.

## Сложности  

При планировании использования CDN следует учитывать некоторые сложности.

+ **Развертывание**. Вам нужно определить источник, из которого CDN получает содержимое, а также решить, нужно ли развертывать содержимое больше чем в одной системе хранения данных (например, в CDN и альтернативном расположении).

  Механизм развертывания приложения должен учитывать особенности процесса развертывания не только статического содержимого и ресурсов, но и файлов приложений, например страниц ASPX. Например, вам может понадобиться выполнить отдельное действие по загрузке содержимого в хранилище BLOB-объектов Azure.

+ **Управление версиями и кэшем**. Вам нужно выбрать способ обновления статического содержимого и развертывания новых версий. Содержимое CDN может быть [очищено](./cdn/cdn-purge-endpoint.md) с помощью портала Azure, когда доступны новые версии ресурсов. Это задача, аналогичная задаче управления кэшированием на стороне клиента, например в браузере.

+ **Тестирование**. Выполнение локального тестирования параметров CDN при разработке и тестировании приложения локально или в промежуточной среде может представлять сложности.

+ **Оптимизация поисковой системы (SEO)**. Содержимое, например изображения и документы, передаются из другого домена при использовании CDN, что может повлиять на SEO для этого содержимого.

+ **Безопасность содержимого**. Многие службы CDN, например Azure CDN, сейчас не предлагают какие-либо типы контроля доступа к содержимому.

+ **Безопасность клиента**. Клиенты могут подключаться из среды, которая не разрешает доступ к ресурсам в CDN. Это может быть среда с правилами безопасности, которая предоставляет доступ только к набору известных ресурсов или которая предотвращает загрузку ресурсов из любого расположения, кроме исходной страницы. В таких случаях требуется выполнить откат.

+ **Устойчивость**. CDN — это потенциальная единая точка отказа для приложения. Она имеет соглашение об уровне обслуживания с более низкой доступностью по сравнению с хранилищем больших двоичных объектов (которое можно использовать для доставки содержимого напрямую), поэтому вам может потребоваться реализовать механизм отработки отказа для критически важного содержимого.

  Вы можете отслеживать доступность содержимого CDN, пропускную способность, передаваемые данные, попадания в кэш, коэффициент попаданий в кэш, а также метрики кэша на портале Azure в режиме [реального времени](./cdn/cdn-real-time-stats.md) и в [статистических отчетах](./cdn/cdn-analyze-usage-patterns.md).

Сценарии, в которых сеть CDN может быть менее эффективной.

+ Доступ к содержимому с небольшим количеством попаданий может осуществляться только несколько раз, пока оно остается действительным (определяется значением его срока жизни). При первом скачивании элемента с вас взимается плата за две транзакции (от источника в CDN, а затем от CDN пользователю).

+ Если данные являются конфиденциальными, например в больших организациях или экосистемах цепочки поставок.


## Общие инструкции и рекомендации

Использование CDN — это хороший способ минимизировать нагрузку на приложение и максимально повысить доступность и производительность. Рассмотрите возможность внедрения этой стратегии для всего соответствующего содержимого и ресурсов, которые использует ваше приложение. При разработке стратегии по использованию CDN необходимо учитывать следующее.


### Исходный домен

Для развертывания содержимого в CDN необходимо просто указать конечную точку HTTP и/или HTTPS, которую служба CDN будет использовать для доступа к содержимому и его кэширования.

Конечная точка может определять контейнер хранилища BLOB-объектов Azure, содержащий статическое содержимое, которое будет поставляться через CDN. Контейнер должен быть помечен как общедоступный. Только большие двоичные объекты в общедоступном контейнере с доступом на общедоступное чтение будут доступны в сети CDN.

Конечная точка может указать папку с именем **cdn** в корне одного из вычислительных слоев приложения (например, веб-роли или виртуальной машины). Результаты запросов на ресурсы, включая динамические ресурсы, такие как страницы ASPX, будут кэшированы в CDN. Минимальный период кэширования составляет 300 секунд. Более короткий период не позволит выполнить развертывание содержимого в CDN (дополнительные сведения см. в разделе [Управление кэшем](#cache-control)).

При использовании веб-приложений в качестве конечной точки задается корневая папка сайта. Для этого необходимо выбрать сайт при создании экземпляра CDN. Все содержимое сайта будет доступно в CDN.

В большинстве случаев указание в качестве конечной точки CDN папки в одном из вычислительных слоев приложения предоставит дополнительную гибкость и контроль. Например, станет проще управлять текущими и будущими требованиями к маршрутизации, а также динамически создавать статическое содержимое, например эскизы изображений.

Если содержимое поставляется из динамических источников, таких как страницы ASPX, то для разделения объектов кэша можно использовать [строки запросов](./cdn/cdn-query-string). Это поведение можно отключить, установив соответствующий параметр на портале Azure при указании конечной точки CDN. При доставке содержимого из хранилища больших двоичных объектов строки запросов рассматриваются как строковые литералы и два элемента, которые имеют одно имя, но разные строки запросов, будут храниться в виде отдельных элементов в CDN.

Для таких ресурсов, как скрипты и другое содержимое, можно использовать переопределение URL-адресов, чтобы избежать перемещения файлов в папку источника CDN.

При использовании больших двоичных объектов Azure для хранения содержимого сети CDN в URL-адресах ресурсов в больших двоичных объектах учитывается регистр для имен контейнеров и больших двоичных объектов.

При использовании пользовательских источников или веб-приложений Azure следует указать путь к экземпляру CDN в ссылках на ресурсы. Например, ниже указан файл изображения в папке **Images** (Изображения) для сайта, который будет доставлен через CDN:

```XML
<img src="http://[your-cdn-endpoint].azureedge.net/Images/image.jpg" />
```

### Развертывание

Может потребоваться подготовить и развернуть статическое содержимое независимо от приложения, если вы не включите его в пакет или процесс развертывания приложения. Рассмотрите, как это повлияет на подход к управлению версиями, который используется для компонентов приложения и содержимого статических ресурсов.

Узнайте, как будет обрабатываться объединение (совмещение нескольких файлов в один) и минификация (удаление ненужных символов, например пробелов, символов новой строки, комментариев и других символов) для скрипта и CSS-файлов. Эти распространенные подходы позволяют сократить время загрузки для клиентов; кроме того, они совместимы с доставкой содержимого через CDN. Дополнительные сведения см. в разделе [Объединение и минификация](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

Если вам нужно развернуть содержимое в дополнительное расположение, это потребуется сделать в дополнительном шаге развертывания. Если приложение обновляет содержимое для CDN регулярно или в ответ на событие, оно должно сохранять обновленное содержимое и конечную точку CDN во всех дополнительных расположениях.

Задать конечную точку CDN для приложения в локальном эмуляторе Azure в Visual Studio нельзя. Это ограничение повлияет на модульное и функциональное тестирование, а также финальное тестирование перед развертыванием. Это необходимо учесть и реализовать альтернативный механизм. Например, вы можете предварительно развернуть содержимое в CDN с помощью пользовательского приложения или служебной программы, а затем выполнить тестирование во время периода кэширования. Кроме того, можно воспользоваться директивами компилятора или глобальными константами для управления расположением, из которого приложение загружает ресурсы. Например, при запуске в режиме отладки можно загрузить такие ресурсы, как клиентские наборы скриптов и другое содержимое из локальной папки, и использовать CDN при запуске в режиме выпуска.

Выберите метод сжатия, который должен поддерживаться CDN.

+ Можно [включить сжатие](./cdn/cdn-improve-performance) на сервере-источнике. В этом случае CDN будет поддерживать сжатие по умолчанию и доставлять клиентам содержимое, сжатое в таких форматах, как ZIP или GZIP. При использовании папки приложения в качестве конечной точки CDN сервер может сжимать часть содержимого автоматически — так же, как и при доставке непосредственно в браузер или другой тип клиента. Формат зависит от значения заголовка **Accept-Encoding** в запросе, отправленном клиентом. В Azure содержимое автоматически сжимается по умолчанию, если загрузка ЦП не превышает 50 %. Если приложение размещается в облачной службе, возможно, при изменении параметров потребуется выполнить задачу запуска, чтобы включить сжатие динамических выходных данных в IIS. Дополнительные сведения см. в статье [Включение сжатия GZIP в Microsoft Azure CDN с помощью веб-роли](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx).

+ Можно включить сжатие непосредственно на пограничных серверах CDN — в таком случае CDN будет сжимать файлы и доставлять их конечным пользователям. Дополнительную информацию см. в статье [Сжатие Azure CDN](./cdn/cdn-improve-performance.md/).

### Маршрутизация и управление версиями

Вы можете использовать разные экземпляры CDN в разные моменты времени. Например, при развертывании новой версии приложения можно использовать новую сеть CDN, а старую сеть CDN (вместе с содержимым в старом формате) оставить для предыдущих версий. Если вы используете хранилище больших двоичных объектов Azure в качестве источника содержимого, можно просто создать отдельную учетную запись хранения или отдельный контейнер и указать его в качестве конечной точки CDN. Если вы используете корневую папку *cdn* в приложении в качестве конечной точки CDN, вы можете использовать методы переопределения URL-адресов для направления запросов в другую папку.

Не используйте строку запроса для указания разных версий приложения в ссылках на ресурсы CDN, так как при получении содержимого из хранилища BLOB-объектов Azure строка запроса является частью имени ресурса (имени большого двоичного объекта). Это может повлиять на способ кэширования ресурсов клиентом.

Развертывание новых версий статического содержимого при обновлении приложения может представлять сложность, если предыдущие ресурсы кэшировались в CDN. Дополнительные сведения см. в разделе [Управление кэшем](#cache-control").

Доступ к содержимому CDN можно ограничивать по странам. Azure CDN позволяет отфильтровывать запросы на основе страны происхождения, ограничивая доставку содержимого. Дополнительные сведения см. в статье [Ограничение доступа к содержимому по странам](./cdn/cdn-restrict-access-by-country/).

###управление кэшем;

Выберите способ управления кэшированием в системе. Например, при использовании папки в качестве источника CDN вы можете указать возможность кэширования страниц, которые создают содержимое, а также срок истечения действия содержимого для всех ресурсов в определенной папке. Вы можете также указать свойства кэша для CDN и для клиента, использующего стандартные заголовки HTTP. Хотя вы, скорее всего, уже управляете кэшированием на сервере и клиенте, сеть CDN позволит получить дополнительные сведения о способе и месте кэширования содержимого.

Чтобы запретить доступ к объектам в CDN, их можно удалить из источника (контейнера больших двоичных объектов или корневой папки *cdn* приложения). Также можно удалить конечную точку CDN или, в случае хранилища BLOB-объектов, сделать контейнер или большой двоичный объект закрытым. При этом элементы будут удалены из CDN только по истечении их срока жизни. Если период истечения срока действия кэша не указан (например, если содержимое загружается из хранилища BLOB-объектов), оно будет кэшироваться в CDN до 7 дней. Можно также вручную [очистить конечную точку CDN](./cdn/cdn-purge-endpoint.md).

В веб-приложении можно задать параметры кэширования и срок истечения действия для всего содержимого с помощью элемента *clientCache* в разделе *system.webServer/staticContent* файла web.config. Учтите, что размещение файла web.config в какой-либо папке влияет на файлы в этой папке и всех вложенных папках.

Если содержимое для CDN создается динамически (например, в коде вашего приложения), убедитесь, что вы указали свойство *Cache.SetExpires* на каждой странице. CDN не будет кэшировать выходные данные со страниц, которые используют для возможности кэширования стандартный параметр *public*. Задайте нужное значение для срока истечения действия кэша, чтобы гарантировать, что содержимое не будет удаляться или перезагружаться из приложения через очень короткие интервалы времени.

### Безопасность

Хотя сеть CDN может доставлять содержимое по HTTPS (SSL) с помощью сертификата, предоставленного CDN, содержимое также будет доступно и по HTTP. Вы не можете заблокировать доступ через HTTP к элементам в CDN. Вам может потребоваться использовать HTTPS для запроса статического содержимого, которое отображается на страницах, предоставляемых посредством HTTPS (например, корзина), чтобы избежать предупреждений браузера о смешанном содержимом.

Сеть Azure CDN не предоставляет средства контроля доступа для обеспечения безопасного доступа к содержимому. Вы не можете использовать подписанные URL-адреса (SAS) с CDN.

При доставке клиентских скриптов по CDN вы можете столкнуться с проблемами, если эти скрипты используют вызов *XMLHttpRequest* для HTTP-запросов к другим ресурсам, например данным, изображениям или шрифтам в другом домене. Многие веб-браузеры предотвращают общий доступ к ресурсам независимо от источника (CORS), если сервер не настроен для задания соответствующих заголовков ответов. Вы можете настроить CDN для поддержки CORS.

+ Если источник, из которого поставляется содержимое, является хранилищем BLOB-объектов Azure, вы можете добавить *CorsRule* к свойствам службы. Правило может указать разрешенные источники для запросов CORS, разрешенные методы, например GET, и максимальный возраст правила в секундах (период, в течение которого клиент должен запросить связанные ресурсы после загрузки исходного содержимого). Дополнительные сведения см. в статье [Поддержка общего доступа к ресурсам независимо от источника (CORS) для служб хранилища Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

+ Если источник, из которого вы поставляете содержимое, является папкой в приложении, например корневой папкой *cdn*, вы можете настроить исходящие правила в файле конфигурации приложения для задания заголовка *Access-Control-Allow-Origin* во всех ответах. Дополнительные сведения об использовании правил переопределения см. в статье [Модуль переопределения URL-адресов](http://www.iis.net/learn/extensions/url-rewrite-module). Обратите внимание, что этот способ невозможно использовать с веб-сайтами Azure.

### Пользовательские домены

Azure CDN позволяет указывать [пользовательское доменное имя](./cdn/cdn-map-content-to-custom-domain.md), а также использовать его для доступа к ресурсам через CDN. Вы можете также настроить пользовательское поддоменное имя с помощью записи *CNAME* в DNS. Этот подход обеспечит дополнительный уровень абстракции и контроля.

При использовании *CNAME* вы не можете использовать SSL, так как в CDN есть собственный SSL-сертификат, который не соответствует пользовательским именам доменов и поддоменов.

### Резервная сеть CDN

Вам следует выбрать для приложения способ устранения сбоев и временной недоступности CDN. Клиентские приложения могут использовать копии ресурсов, которые были кэшированы локально (на клиенте) во время предыдущих запросов. Также вы можете включить код, который обнаруживает сбой и запрашивает ресурсы из источника (папки приложения или контейнера больших двоичных объектов Azure, в котором содержатся ресурсы), если сеть CDN недоступна.

### Оптимизация поисковой системы

Если SEO является важной частью приложения, выполните следующие задачи.

+ Включите канонический заголовок *Rel* на каждой странице или в каждом ресурсе.

+ Используйте запись поддомена *CNAME* и обращайтесь к ресурсам с помощью этого имени.

+ Учитывайте возможность того, что IP-адрес CDN может быть страной или регионом, который отличается от самого приложения.

+ При использовании хранилища больших двоичных объектов Azure в качестве источника сохраните ту же структуру файлов для ресурсов в CDN, что и в папках приложения.


### Мониторинг и ведение журнала

Включите CDN в стратегию мониторинга приложений, чтобы обнаруживать и измерять сбои приложения или длительные задержки. Мониторинг можно выполнять в диспетчере профилей CDN, расположенном на сайте портала Azure.

Включите ведение журнала для CDN и выполняйте мониторинг этого журнала как часть ежедневных операций.

Выполните анализ трафика CDN для схем использования. Портал Azure предоставляет средства, которые позволяют отслеживать следующие показатели:
+ пропускная способность;
+ переданные данные;
+ попадания в кэш (коды состояния);
+ состояние кэша;
+ коэффициент попаданий в кэш;
+ доля запросов IPV4 и IPV6.

Дополнительные сведения см. в статье [Анализ вариантов использования CDN](./cdn/cdn-analyze-usage-patterns.md/).

### Потенциальные затраты

За передачу исходящих данных из сети CDN производится тарификация. Кроме того, если вы используете хранилище больших двоичных объектов для размещения ресурсов, начисляется плата за хранение транзакций, когда CDN загружает данные из приложения. Следует задать реалистичные сроки действия для кэширования содержимого, но не настолько короткие, чтобы загрузка содержимого из приложения или хранилища больших двоичных объектов в CDN повторялась слишком часто.

Редко скачиваемые элементы повлекут расходы на две транзакции, не сильно уменьшая нагрузку на сервер.

### Объединение и минификация

Используйте объединение и минификацию, чтобы уменьшить размер таких ресурсов, как код JavaScript и HTML-страницы, которые хранятся в сети CDN. Эта стратегия позволяет сократить время, необходимое для загрузки этих элементов на клиент.

Объединение и минификацию может обрабатывать ASP.NET. В проекте MVC вы определяете наборы в *BundleConfig.cs*. Ссылка на набор минифицированных скриптов создается путем вызова метода *Script.Render*, обычно в коде класса представления. Эта ссылка содержит строку запроса, которая включает хэш, основанный на содержимом набора. При изменении содержимого набора созданный хэш также изменится.

По умолчанию в экземплярах Azure CDN параметр *Состояние строки запроса* отключен. Чтобы обновленные наборы скриптов правильно обрабатывались CDN, необходимо включить параметр *Состояние строки запроса* для экземпляра CDN. Учтите, что активация этого параметра может занять больше часа.


## Пример кода
В этом разделе содержатся некоторые примеры кода и методов работы с CDN.


### Переопределение URL-адресов
В следующем фрагменте из файла Web.config в корне приложения, размещенного в облачных службах, показано, как выполнить [переопределение URL-адресов](https://technet.microsoft.com/library/ee215194.aspx) при использовании CDN. Запросы на кэшируемое содержимое из CDN будут перенаправляться в определенные папки в корне приложения на основе типа ресурса (например, скрипты и изображения).


```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

Эти правила переопределения выполняют следующие переопределения.

+ Первое правило позволяет внедрить версию в имя файла ресурса, которая потом пропускается. Например, *Filename\_v123.jpg* перезаписывается как *Filename.jpg*.

+ Следующие четыре правила показывают, как перенаправлять запросы, если вы не хотите сохранять ресурсы в папке с именем *cdn** в корне веб-роли. Правило сопоставляет URL-адреса *cdn/Images*, *cdn/Content*, *cdn/Scripts* и *cdn/bundles* с их соответствующими корневыми папками в веб-роли.

Учтите, что при использовании переопределения URL-адресов требуется внести некоторые изменения в объединение ресурсов.

## Дополнительные сведения


+ [Azure CDN](https://azure.microsoft.com/services/cdn/)
+ [Документация по сетям доставки содержимого Azure (CDN)](https://azure.microsoft.com/documentation/services/cdn/)
+ [Обслуживание содержимого из CDN Azure в вашем веб-приложении](./cdn/cdn-serve-content-from-cdn-in-your-web-application/)
+ [Интеграция облачной службы с Azure CDN](./cdn/cdn-cloud-service-with-cdn.md/)
+ [Рекомендации по работе с сетями доставки содержимого Microsoft Azure](https://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)

<!---HONumber=AcomDC_0518_2016-->