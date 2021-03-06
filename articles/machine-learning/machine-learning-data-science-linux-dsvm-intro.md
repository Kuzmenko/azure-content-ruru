<properties
	pageTitle="Подготовка виртуальной машины Linux для обработки и анализа данных | Microsoft Azure"
	description="Настройка и создание виртуальной машины Linux в Azure для обработки и анализа данных и машинного обучения."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="bradsev" />

# Подготовка виртуальной машины Linux для обработки и анализа данных

Виртуальная машина Linux для обработки и анализа данных — это виртуальная машина Azure, содержащая набор предварительно установленных инструментов. Эти инструменты повсеместно используются для анализа данных и машинного обучения. К основным установленным приложениям относятся:

- Microsoft R Open
- Дистрибутив Anaconda Python (версий 2.7 и 3.5), включая популярные библиотеки для анализа данных.
- Записная книжка Jupyter (R, Python)
- Обозреватель хранилищ Azure
- Интерфейс командной строки Azure (Azure CLI) для управления ресурсами Azure.
- Базы данных PostgresSQL
- Инструменты машинного обучения
    - [Computational Network Toolkit (CNTK)](https://github.com/Microsoft/CNTK): набор программных средств для глубокого обучения от Microsoft Research.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): система быстрого машинного обучения, поддерживающая такие методы, как онлайн-обучение, хэширование, общее сокращение, сокращение, обучение поиску, активное и интерактивное обучение.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): инструмент, предоставляющий быструю и точную реализацию повышенного дерева.
    - [Rattle](http://rattle.togaware.com/) (аналитический инструмент R для легкого обучения): инструмент, который облегчает начало работы с анализом данных и машинным обучением в R с помощью графического пользовательского интерфейса, в котором осуществляется исследование данных и моделирование с автоматическим созданием кода R.
- Пакет SDK для Azure в Java, Python, node.js, Ruby и PHP
- Библиотеки R и Python для машинного обучения Azure и других служб Azure
- Средства разработки и редакторы (Eclipse, Emacs, gedit, vi)

Анализ данных включает выполнение следующих задач:

1. поиск, загрузка и предварительная обработка данных;
2. построение и тестирование моделей;
3. развертывание моделей для использования в приложениях для анализа.

Для выполнения этих задач специалисты по обработке и анализу данных используют широкий набор инструментов. Поиск подходящей версии программного обеспечения и его последующее скачивание, компиляция и установка могут занимать довольно много времени.

Виртуальная машина Linux для обработки и анализа данных может существенно упростить выполнение этих задач. Используйте ее, чтобы быстро начать работу со своим аналитическим проектом. Она позволяет работать над задачами, используя различные языки, включая R, Python, SQL, Java и C++. Eclipse предоставляет удобную среду разработки для разработки и тестирования кода. Пакет SDK для Azure в виртуальной машине позволяет создавать приложения с помощью различных служб в Linux для облачной платформы Майкрософт. Кроме того, доступны другие языки, такие как Ruby, Perl, PHP и node.js, которые также предварительно установлены.

Плата за программное обеспечение для этого образа виртуальной машины не взимается. Вы платите только за оборудование Azure в зависимости от размера виртуальной машины, которая подготавливается с образом. Дополнительные сведения о плате за вычисления можно найти на [сайте партнерской программы Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linuxdsvm/).


## Предварительные требования

Перед созданием виртуальной машины Linux для обработки и анализа данных необходимо убедиться в наличии следующих компонентов:

- **Подписка на Azure** — сведения о ее получении см. в разделе [Получение бесплатной пробной версии Azure](https://azure.microsoft.com/free/).
- **Учетная запись хранения Azure** — ознакомьтесь с разделом [Создание учетной записи хранения Azure](storage-create-storage-account.md#create-a-storage-account) для получения информации о процессе ее создания. Если вы не хотите использовать существующую учетную запись хранения, то ее можно создать при создании виртуальной машины.


## Создание виртуальной машины Linux для обработки и анализа данных

Ниже приведены шаги по созданию экземпляра виртуальной машины Linux для обработки и анализа данных.

1.	Перейдите к списку виртуальных машин на [портале Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.	 В нижней части страницы нажмите кнопку **Создать**. Откроется мастер![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.	 В следующих разделах приведены входные данные для всех шагов мастера (номера см. справа на предыдущем рисунке) по созданию виртуальной машины Майкрософт для обработки и анализа данных. Ниже приведено описание входных данных, необходимых для настройки на каждом из шагов.

  	а. **Основные сведения**:

  - **Имя** — имя создаваемого сервера для обработки и анализа данных.
  - **Имя пользователя** — идентификатор для входа первой учетной записи.
  - **Пароль** — пароль первой учетной записи (вместо пароля можно использовать открытый ключ SSH).
  - **Подписка** — при наличии нескольких подписок выберите ту, в которой будет создана виртуальная машина и для которой будет взиматься плата. Вам необходимо иметь права на создание ресурсов для этой подписки.
  - **Группа ресурсов** — вы можете создать новую группу или использовать существующую.
  - **Расположение** — выберите наиболее подходящий центр обработки данных. Обычно это центр обработки данных, в котором размещена большая часть ваших данных, или ближайший к вашему физическому расположению центр для наиболее быстрого доступа к сети.

  	b. **Размер**:

  - Выберите один из типов серверов, который соответствует вашим функциональным требованиям и финансовым ограничениям. Выберите **Просмотреть все**, чтобы отобразить дополнительные варианты размеров виртуальных машин.

  	c. **Параметры**:

  - **Тип диска** — выберите **Премиум**, если требуется твердотельный накопитель (SSD). В противном случае выберите тип **Стандартный**.
  - **Учетная запись хранения** — вы можете создать учетную запись хранения Azure в подписке или использовать существующую учетную запись в **расположении**, которое было выбрано на этапе работы мастера "Основные сведения".
  - **Другие параметры** — в большинстве случаев используются значения по умолчанию. Чтобы использовать значения, отличные от значений по умолчанию, наведите указатель мыши на информационную ссылку, чтобы получить справку по конкретному полю.

  	г) **Сводка**:

  - Проверьте, все ли сведения введены правильно.

  	д. **Купить**:

  - Чтобы начать подготовку, щелкните **Купить**. Отобразится ссылка на условия транзакции. За использование виртуальной машины не взимается дополнительная плата, кроме платы за размер сервера, выбранный на шаге **Размер**.

Подготовка занимает 10–20 минут. Состояние подготовки отображается на портале Azure.

## Как осуществить доступ к виртуальной машине Linux для обработки и анализа данных

После создания виртуальной машины вы можете войти в нее с помощью протокола SSH. Для входа с помощью интерфейса текстовой оболочки используйте учетную запись, созданную в разделе **Основные сведения** на шаге 3. В Windows можно скачать клиент SSH, например [Putty](http://www.putty.org). Если вы предпочитаете графический рабочий стол (система X Windows), можете использовать перенаправление X11 в Putty или установить клиент X2Go.

>[AZURE.NOTE] При тестировании клиент X2Go работал значительно лучше по сравнению с перенаправлением X11. Мы рекомендуем клиент X2Go для графического интерфейса рабочего стола.


## Установка и настройка клиента X2Go

Виртуальная машина Linux уже подготовлена на сервере X2Go и готова к приему подключений клиента. Для подключения к рабочему столу виртуальной машины Linux выполните следующие действия на клиенте.

1. Скачайте клиент X2Go для своей клиентской платформы [отсюда](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) и установите его.
2. Запустите клиент X2Go и выберите **Новая сессия**. Откроется окно конфигурации с несколькими вкладками. Введите следующие параметры конфигурации:
    * **Вкладка "Сеанс"**:
        - **Хост** — имя узла или IP-адрес виртуальной машины Linux для обработки и анализа данных.
        - **Пользователь** — имя пользователя на виртуальной машине Linux.
        - **Порт SSH**: оставьте 22 (значение по умолчанию).
        - **Тип сеанса**: измените значение на XFCE. В настоящее время виртуальная машина Linux поддерживает только рабочий стол XFCE.
    * **Вкладка "Мультимедиа"**: здесь можно отключить поддержку звука и печати на клиенте, если они не нужны.
    * **Общие папки**: если вы хотите смонтировать каталоги клиентских компьютеров на виртуальной машине Linux, добавьте необходимые каталоги на этой вкладке.

После входа в виртуальную машину с помощью клиента SSH или подключения к рабочему столу XFCE посредством клиента X2Go можно начать использовать инструменты, которые установлены и настроены на виртуальной машине. На рабочем столе XFCE вы видите ярлыки из меню и значки рабочего стола для многих инструментов.


## Средства, установленные на виртуальной машине Linux для обработки и анализа данных

### Microsoft R Open
R — один из самых популярных языков для анализа данных и машинного обучения. Если вы хотите использовать R для анализа, воспользуйтесь установленным на виртуальной машине компонентом Microsoft R Open (MRO) с библиотекой Math Kernel Library (MKL). Библиотека MKL оптимизирует математические операции, которые широко применяются в аналитических алгоритмах. MRO полностью совместим с CRAN-R, и любые библиотеки R, опубликованные в CRAN, можно установить в MRO. Для редактирования программ R можно воспользоваться одним из редакторов по умолчанию, например vi, Emacs или gedit. Кроме того, вы можете скачать и использовать другие интегрированные среды разработки, например [RStudio](http://www.rstudio.com). Для вашего удобства в каталоге **/dsvm/tools** размещен простой сценарий (installRStudio.sh), который устанавливает RStudio. При использовании редактора Emacs обратите внимание на предварительно установленный пакет ESS (Emacs Speaks Statistics), который упрощает работу с файлами R в редакторе Emacs.

Чтобы запустить R, просто введите **R** в оболочке. Откроется интерактивная среда. Для разработки программы R обычно используется редактор, такой как Emacs, vi или gedit, а затем сценарии запускаются в R. При установке RStudio вы получаете полноценную графическую интегрированную среду разработки для программ R.

При желании для установки [20 самых популярных пакетов R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) можно воспользоваться R-скриптом. Этот сценарий можно запустить в интерактивном интерфейсе R, для подключения к которому, как уже упоминалось, необходимо ввести **R** в оболочке.

### Python
Для разработки на языке Python установлен дистрибутив Anaconda Python версий 2.7 и 3.5. Этот дистрибутив содержит базовую версию Python, а также примерно 300 наиболее популярных математических и инженерных пакетов и пакетов аналитики данных. Можно использовать текстовые редакторы по умолчанию. Кроме того, можно использовать Spyder, интегрированную среду разработки Python, которая входит в состав дистрибутива Anaconda Python. Для Spyder необходим графический рабочий стол или перенаправление X11. Ярлык для Spyder находится на рабочем столе.

Поскольку у нас есть Python как версии 2.7, так и версии 3.5, необходимо специально активировать необходимую версию Python, с которой вы собираетесь работать в текущем сеансе. Во время активации нужная версия Python указывается в качестве значения переменной PATH.

Для активации Python 2.7 выполните следующую команду из оболочки:

	source /anaconda/bin/activate root

Язык Python 2.7 установлен в каталоге */anaconda/bin*.

Для активации Python 3.5 выполните следующую команду из оболочки:

	source /anaconda/bin/activate py35


Язык Python 3.5 установлен в каталоге */anaconda/envs/py35/bin*.

Чтобы вызвать интерактивный сеанс python, просто введите **python** в оболочке. Если вы используете графический интерфейс или перенаправление X11, то можете ввести команду **spyder** для запуска интегрированной среды разработки Python.

### Записная книжка Jupyter

Дистрибутив Anaconda также содержит записную книжку Jupyter, среду для совместного использования кода и анализа. Доступ к записной книжке Jupyter можно получить через JupyterHub. Для входа используйте учетные данные локального пользователя Linux.

На сервере записной книжки Jupyter предварительно настроена поддержка ядер Python 2, Python 3 и R. На рабочем столе есть значок "Записная книжка Jupyter", с помощью которого можно запустить браузер для доступа к серверу записной книжки. Если вы подключаетесь к виртуальной машине с помощью клиента SSH или X2Go, то к серверу записной книжки Jupyter можно обратиться по адресу [https://localhost:8000/](https://localhost:8000/).

>[AZURE.NOTE] При появлении любых предупреждений о сертификатах нажмите кнопку "Продолжить".

Сервер записной книжки Jupyter доступен с любого узла. Просто введите *https://\<DNS-имя\_или\_IP-адрес\_виртуальной\_машины>:8000/*.

>[AZURE.NOTE] Когда виртуальная машина подготавливается, на брандмауэре по умолчанию открывается порт 8000.

Мы добавили по одному примеру записной книжки для Python и R. Ссылки на примеры можно найти на домашней странице записной книжки, войдя в записную книжку Jupyter с помощью учетных данных локального пользователя Linux. Для создания новой записной книжки выберите **Создать**, а затем укажите соответствующий язык ядра. Если вы не видите кнопки **Создать**, то щелкните значок **Jupyter** в верхнем левом углу, чтобы перейти на домашнюю страницу сервера записной книжки.


### Интегрированные среды разработки и редакторы

Для выбора доступны несколько редакторов кода. К ним относятся vi/VIM, Emacs, gEdit и Eclipse. gEdit и Eclipse — графические редакторы, и для их использования необходимо войти в графический рабочий стол. На рабочем столе есть меню и значки для запуска этих редакторов.

**VIM** и **Emacs** — текстовые редакторы. В Emacs мы установили пакет надстройки ESS, который упрощает работу с R в редакторе Emacs. Дополнительные сведения см. в описании пакета [ESS](http://ess.r-project.org/).

**Eclipse** — расширяемая интегрированная среда разработки с открытым кодом, поддерживающая несколько языков. На виртуальной машине установлена редакция Eclipse для разработчиков Java. Для некоторых популярных языков доступны подключаемые модули, которые можно установить для расширения среды Eclipse. В Eclipse также установлен подключаемый модуль, который называется **Azure Toolkit for Eclipse** (Набор средств Azure для Eclipse). Он позволяет создавать, разрабатывать, тестировать и развертывать приложения Azure с помощью среды разработки Eclipse, которая поддерживает такие языки программирования, как Java. Кроме того, есть **пакет SDK Azure для Java**, который предоставляет доступ к различным службам Azure из среды Java. Дополнительные сведения о наборе средств Azure для Eclipse см. в [этой статье](../azure-toolkit-for-eclipse.md).

**LaTex** устанавливается с помощью пакета texlive вместе с пакетом надстройки Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html), который упрощает создание документов LaTex в Emacs.

### Базы данных

#### Postgres
На виртуальной машине доступна база данных **Postgres**, для которой уже запущены службы и выполнена операция initdb. Вам необходимо создать базы данных и пользователей. Дополнительные сведения см. в [документации по Postgres](https://www.postgresql.org/docs/).

####  Графический клиент SQL
Для подключения к различным базам данных (Microsoft SQL Server, Postgres и MySQL) и для выполнения SQL-запросов предоставлен графический клиент SQL — **SQuirrel SQL**. Его можно запустить в графическом рабочем столе (например, с помощью клиента X2Go). Для запуска SQuirrel SQL можно воспользоваться значком на рабочем столе или выполнить следующую команду в оболочке.

	/usr/local/squirrel-sql-3.7/squirrel-sql.sh

Перед первым применением настройте драйверы и псевдонимы базы данных. Драйверы JDBC расположены в каталоге:

*/usr/share/java/jdbcdrivers*

Дополнительные сведения см. в разделе [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### Программы командной строки для доступа к Microsoft SQL Server

В пакете драйвера ODBC для SQL Server также содержатся две программы командной строки.

**bcp** — это служебная программа для массового копирования данных между экземпляром Microsoft SQL Server и файлом данных в любом пользовательском формате. С помощью служебной программы bcp можно выполнять импорт большого количества новых строк в таблицы SQL Server или экспорт данных из таблиц в файлы данных. Чтобы импортировать данные в таблицу, необходимо использовать файл форматирования, созданный для этой таблицы, или изучить структуру таблицы и типы данных для ее столбцов.

Дополнительную информацию см. в статье [Соединение с помощью bcp](https://msdn.microsoft.com/library/hh568446.aspx).

Служебная программа **sqlcmd** позволяет выполнять из командной строки инструкции Transact-SQL, системные процедуры и файлы сценариев. Эта программа использует ODBC для выполнения пакетов Transact-SQL.

Дополнительную информацию см. в статье [Соединение с помощью sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Версии этой служебной программы для платформ Windows и Linux немного отличаются. Дополнительные сведения см. в документации по .


#### Библиотеки для доступа к базам данных

В Python и R доступны библиотеки для доступа к базам данных.

- Для языка R: пакеты **RODBC** и **dplyr** позволяют выполнять на сервере базы данных запросы и инструкции SQL.
- Для Python: библиотека **pyodbc** предоставляет доступ к базе данных с использованием ODBC в качестве базового уровня.

Чтобы получить доступ к **Postgres**:

- для R используйте пакет **RPostgreSQL**;
- для Python используйте библиотеку **psycopg2**.


### Инструменты Azure
На виртуальной машине установлены следующие инструменты Azure:

- **Интерфейс командной строки Azure** (Azure CLI) позволяет создавать ресурсы Azure и управлять ими с помощью команд оболочки. Для вызова инструментов Azure просто введите **azure help**. Дополнительные сведения см. на [странице документации по Azure CLI](../virtual-machines-command-line-tools.md).
- **Microsoft Azure Storage Explorer** — это графический инструмент, который позволяет просматривать объекты, сохраненные в учетной записи хранения Azure, а также передавать и скачивать данные больших двоичных объектов Azure. Для доступа к обозревателю хранилищ воспользуйтесь значком рабочего стола. Его можно открыть из командной строки, введя **StorageExplorer**. Для этого необходимо выполнить вход в клиенте X2Go или настроить перенаправление X11.
- **Библиотеки Azure**. Ниже приведены некоторые из предварительно установленных библиотек.

 - **Python**. Среди установленных библиотек Python к Azure имеют отношение **azure**, **azureml**, **pydocumentdb** и **pyodbc**. Первые три библиотеки позволяют обращаться к службам хранилища Azure, Машинному обучению Azure и Azure DocumentDB (база данных NoSQL в Azure). Четвертая библиотека, pyodbc, (вместе с драйвером Microsoft ODBC для SQL Server) обеспечивает доступ к SQL Server, Базе данных SQL Azure и хранилищу данных SQL Azure из Python с помощью интерфейса ODBC. Для просмотра всех перечисленных библиотек введите команду **pip list**. Выполните эту команду как в среде Python версии 2.7, так и в среде Python версии 3.5.
 - **R**. Среди установленных библиотек R к Azure имеют отношение **AzureML** и **RODBC**.
 - **Java**. Список библиотек Java для Azure можно найти в каталоге **/dsvm/sdk/AzureSDKJava** на виртуальной машине. Основные библиотеки — это API-интерфейсы для службы хранилища Azure и API-интерфейсы для управления Azure, драйверы DocumentDB и драйверы JDBC для SQL Server.

[Портал Azure](https://portal.azure.com) можно открыть в предустановленном браузере Firefox. На портале Azure можно создавать ресурсы Azure, а также управлять ими и отслеживать их.

### Машинное обучение Azure

Машинное обучение Azure — полностью управляемая облачная служба, которая позволяет легко создавать, развертывать решения прогнозной аналитики и предоставлять к ним общий доступ. В Студии машинного обучения Azure можно создавать свои эксперименты и модели. Ее можно открыть из браузера на виртуальной машине для обработки и анализа данных, перейдя в раздел [Машинное обучение Microsoft Azure](https://studio.azureml.net).

После входа в Студию машинного обучения Azure откроется холст для экспериментов, на котором вы можете создать логическую блок-схему алгоритмов машинного обучения. Вам также доступна записная книжка Jupyter, размещенная в Машинном обучении Azure, и вы можете легко работать с экспериментами в Студии машинного обучения. Вы можете ввести в эксплуатацию созданные модели машинного обучения, упаковав их в интерфейс веб-службы. Это позволяет клиентам, написанным на любом языке, получать прогнозы из моделей машинного обучения. Дополнительные сведения см. в [документации по машинному обучению](https://azure.microsoft.com/documentation/services/machine-learning/).

Можно также создавать модели в R или Python на виртуальной машине, а затем развертывать их в рабочей среде в Машинном обучении Azure. Мы установили библиотеки в R (**AzureML**) и Python (**azureml**), которые позволят выполнить эти действия.

Сведения о развертывании моделей R и Python в Машинном обучении Azure см. в разделе "Создание моделей с помощью R или Python и их ввод в эксплуатацию с помощью машинного обучения Azure" статьи [10 задач, которые можно выполнить в виртуальной машине для обработки и анализа данных](machine-learning-data-science-vm-do-ten-things.md).

>[AZURE.NOTE] Эти инструкции были написаны для Windows-версии виртуальной машины для обработки и анализа данных. Но приведенная информация о развертывании моделей в Машинном обучении Azure также применима к виртуальной машине Linux.

### Инструменты машинного обучения

В состав виртуальной машины входят некоторые инструменты и алгоритмы машинного обучения, которые были предварительно скомпилированы и установлены локально. В частности, описаны такие возможности:

* **CNTK** (Computational Network Toolkit) — набор инструментов Microsoft Research для глубинного обучения.
* **Vowpal Wabbit** — алгоритм быстрого онлайн-обучения.
* **xgboost** — инструмент, предоставляющий оптимизированные алгоритмы повышенного дерева.
* **Python** — в Anaconda Python алгоритмы машинного обучения содержатся в разных библиотеках, например Scikit-learn. С помощью команды `pip install` можно установить другие библиотеки.
* **R** — для R существует обширный набор функций машинного обучения. В число предустановленных входят библиотеки lm, glm, randomForest и rpart. Другие библиотеки можно установить, выполнив приведенную ниже команду.

		install.packages(<lib name>)

Вот некоторые дополнительные сведения о первых трех инструментах машинного обучения в списке.

#### CNTK
Это открытый набор инструментов для глубокого обучения. Это программа командной строки (cntk), и она уже добавлена в переменную PATH.

Для запуска простого примера выполните следующие команды в оболочке.

	# Copy samples to your home directory and execute cntk
	cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
	cd cntkdemo/Data
	cntk configFile=../Config/Simple.cntk

Выходные данные модели находятся в каталоге *~/cntkdemo/Output/Models*.

Дополнительные сведения см. в разделе CNTK на сайте [GitHub](https://github.com/Microsoft/CNTK) и на [вики-сайте CNTK](https://github.com/Microsoft/CNTK/wiki).


#### Vowpal Wabbit

Vowpal Wabbit — это система машинного обучения, поддерживающая такие методы, как онлайн-обучение, хэширование, общее сокращение, сокращение, обучение поиску, активное и интерактивное обучение.

Для запуска инструмента с очень простым примером сделайте следующее.

	cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
	cd vwdemo
	vw house_dataset

В этом каталоге есть и другие примеры, большего размера. Дополнительные сведения о VW см. в [этом разделе GitHub](https://github.com/JohnLangford/vowpal_wabbit) и на [вики-сайте Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### xgboost
Это библиотека, разработанная и оптимизированная для алгоритмов увеличивающегося дерева. Цель этой библиотеки — сдвинуть вычислительные ограничения виртуальных машин до пределов, необходимых для получения повышенного дерева большого размера, которое является масштабируемым, переносимым и точным.

Эта библиотека, так же как и библиотека R, предоставляется в виде оболочки командной строки.

Чтобы использовать эту библиотеку в R, запустите интерактивный сеанс R (просто введя **R** в оболочке) и загрузите библиотеку.

Ниже приведен простой пример, который можно запустить в командной строке R:

	library(xgboost)

	data(agaricus.train, package='xgboost')
	data(agaricus.test, package='xgboost')
	train <- agaricus.train
	test <- agaricus.test
	bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
	pred <- predict(bst, test$data)

Для запуска командной строки xgboost выполните следующие команды в оболочке.

	cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
	cd xgboostdemo
	xgboost mushroom.conf


Файл .model записывается в указанный каталог. Дополнительную информацию об этом демонстрационном примере см. [на сайте GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Дополнительные сведения об xgboost можно см. на [странице документации по xgboost](https://xgboost.readthedocs.org/en/latest/) и в соответствующем [репозитории GitHub](https://github.com/dmlc/xgboost).

#### Rattle
Rattle (**R** **A**nalytical **T**ool **T**o **L**earn **E**asily, аналитический инструмент R для легкого обучения) использует изучение и моделирование данных на основе графического пользовательского интерфейса. Он предоставляет статистические и визуальные сводные данные, преобразует данные для удобного моделирования, создает контролируемые и неконтролируемые модели на основе данных, показывает производительность моделей в графическом виде и оценивает новые наборы данных. Он также формирует код R, соответствующий операциям в пользовательском интерфейсе. Этот код можно запустить непосредственно в R или использовать в качестве отправной точки для дальнейшего анализа.

Для запуска Rattle необходимо войти в сеанс графического рабочего стола. В терминале введите ```R``` для запуска среды R. В командной строке R введите следующие команды.

	library(rattle)
	rattle()

Откроется графический интерфейс с набором вкладок. Вот список действий в Rattle, которые позволят быстро построить модель на основе примера погодных данных. В некоторых действиях будет предложено автоматически скачать и установить необходимые пакеты R, которые еще не установлены в системе.

>[AZURE.NOTE] Если у вас нет разрешений на установку пакета в системном каталоге (по умолчанию), то в окне консоли R может появиться запрос на установку пакетов в вашей персональной библиотеке. Ответьте *y* (да) на все эти запросы.

1. Нажмите **Execute (Выполнить)**.
2. Откроется диалоговое окно с вопросом, хотите ли вы использовать пример погодных данных. Нажмите кнопку **Yes** (Да), чтобы загрузить пример.
3. Перейдите на вкладку **Model** (Модель).
4. Нажмите кнопку **Execute** (Выполнить) для построения дерева принятия решений.
5. Нажмите кнопку **Draw** (Нарисовать) для отображения дерева принятия решений.
6. Щелкните переключатель **Forest** (Лес) и нажмите кнопку **Execute** (Выполнить) для создания случайного леса.
7. Перейдите на вкладку **Evaluate** (Оценка).
8. Щелкните переключатель **Risk** (Риск) и нажмите кнопку **Execute** (Выполнить) для отображения двух диаграмм производительности "Risk (Cumulative)" (Риск (накопительный)).
9. Щелкните вкладку **Log** (Журнал), чтобы показать код R, созданный для предыдущих операций. (Из-за ошибки в текущем выпуске Rattle необходимо вставить знак *#* перед строкой *Export this log…* (Экспортировать этот файл журнала…) в тексте журнала.)
10. Нажмите кнопку **Export** (Экспортировать), чтобы сохранить файл R-скрипт под именем *weather\_script.R* в домашней папке.

Закройте Rattle и R. Теперь можно изменить созданный R-скрипт или запустить его в любое удобное время для повторения всех действий, которые были выполнены в пользовательском интерфейсе Rattle. Это простой способ (особенно для новичков в R) быстро выполнить анализ и машинное обучение в простом графическом интерфейсе с автоматическим созданием кода на R для изменения и (или) изучения.


## Дальнейшие действия
Вот как можно продолжить обучение и изучение.

* В пошаговом руководстве [Data science on the Linux Data Science Virtual Machine](machine-learning-data-science-linux-dsvm-walkthrough.md) (Обработка и анализ данных на виртуальной машине Linux для обработки и анализа данных) описаны способы выполнения распространенных задач обработки и анализа данных с помощью подготовленной виртуальной машины Linux.
* Изучите различные средства обработки и анализа данных на виртуальной машине, используя средства, описанные в этой статье. Кроме того, можно выполнить команду *dsvm-more-info* в оболочке на виртуальной машине, чтобы получить базовые сведения и ссылки на дополнительные сведения об инструментах, которые установлены на виртуальной машине.
* Изучите систематический подход к созданию комплексных аналитических решений с помощью [группового процесса обработки и анализа данных](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Перейдите в [коллекцию Cortana Analytics](http://gallery.cortanaanalytics.com), чтобы найти примеры машинного обучения и анализа данных с использованием Cortana Analytics Suite.

<!---HONumber=AcomDC_0914_2016-->