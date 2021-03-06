<properties
	pageTitle="Использование добавочных моментальных снимков для архивации и восстановления виртуальных машин Azure | Microsoft Azure"
	description="Создание пользовательского решения для архивации и восстановления дисков виртуальной машины Azure с помощью добавочных моментальных снимков."
	services="storage"
	documentationCenter="na"
	authors="aungoo-msft"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="tamram;aungoo"/>


# Архивация дисков виртуальной машины Azure с помощью добавочных моментальных снимков

## Обзор

Служба хранилища Azure предоставляет возможность создавать моментальные снимки больших двоичных объектов. В моментальные снимки записывается состояние большого двоичного объекта в определенный момент времени. В этой статье мы опишем, как можно поддерживать архивы дисков виртуальной машины с помощью моментальных снимков. Эту методику можно применять, если вы решили не использовать службу архивации и службу восстановления Azure и хотите создать пользовательскую стратегию архивации дисков виртуальной машины.

Диски виртуальной машины Azure хранятся в службе хранилища Azure как страничные BLOB-объекты. Так как в статье рассматривается стратегия архивации для дисков виртуальной машины, то говорить о моментальных снимках мы будем в контексте страничных BLOB-объектов. Чтобы больше узнать о моментальных снимках, см. раздел [Создание моментального снимка большого двоичного объекта](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## Что такое моментальный снимок?

Моментальный снимок большого двоичного объекта — это доступная только для чтения версия большого двоичного объекта, созданная в определенный момент времени. После создания моментального снимка его можно читать, копировать или удалять, но нельзя изменять. Моментальные снимки обеспечивают способ резервного копирования BLOB-объекта в том виде, в котором он находится в данный момент времени. До REST версии 2015-04-05 можно было копировать полные моментальные снимки. Начиная с REST версии 2015-07-08 и выше стало возможным копировать добавочные моментальные снимки.

## Копирование полных моментальных снимков

Моментальные снимки можно копировать в другую учетную запись хранения в виде больших двоичных объектов, чтобы хранить архивы базового большого двоичного объекта. Также можно скопировать моментальный снимок, заменив им базовый большой двоичный объект, восстановив его более раннюю версию. Моментальный снимок, копируемый из одной учетной записи хранения в другую, займет столько же места, сколько базовый страничный BLOB-объект. Поэтому полное копирование моментальных снимков из одной учетной записи хранения в другу будет выполняться медленно и потребует много места в целевой учетной записи хранения.

>[AZURE.NOTE] При копировании базового большого двоичного объекта в другое место назначения его моментальные снимки не копируются. Аналогично, при перезаписи базового большого двоичного объекта копией связанные с ним моментальные снимки не затрагиваются и остаются неизменными под именем базового большого двоичного объекта.

### Архивация дисков с помощью моментальных снимков

В рамках стратегии архивации для дисков виртуальной машины может потребоваться периодическое создание моментальных снимков диска или страничного BLOB-объекта, а также их копирование в другую учетную запись хранения с помощью таких инструментов, как операция [копирования большого двоичного объекта](https://msdn.microsoft.com/library/azure/dd894037.aspx) или [AzCopy](storage-use-azcopy.md). Можно скопировать моментальный снимок в целевой страничный BLOB-объект с другим именем. Полученный целевой страничный BLOB-объект будет большим двоичным объектом, доступным для записи, а не моментальным снимком. Далее в этой статье мы опишем, как создавать архивы дисков виртуальной машины с помощью моментальных снимков.

### Восстановление дисков с помощью моментальных снимков

Когда требуется восстановить предыдущую стабильную версию диска, записанную в один из архивных моментальных снимков, можно скопировать этот моментальный снимок поверх базового страничного BLOB-объекта. После повышения уровня моментального снимка до базового страничного BLOB-объекта этот моментальный снимок не исчезает, но его источник перезаписывается копией, которая допускает и чтение, и запись. Далее в этой статье мы опишем, как восстановить предыдущую версию диска из его моментального снимка.

### Реализация копирования полных моментальных снимков

Копирование полных моментальных снимков можно реализовать, выполнив следующее.

-   Сначала создайте моментальный снимок базового большого двоичного объекта с помощью операции [создания моментального снимка большого двоичного объекта](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Затем скопируйте этот моментальный снимок в целевую учетную запись хранения с помощью операции [копирования большого двоичного объекта](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Повторите эту процедуру, чтобы создавать резервные копии своего базового BLOB-объекта.

## Копирование добавочных моментальных снимков

Новая функция [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) в API обеспечивает намного лучший способ архивации моментальных снимков страничных BLOB-объектов или дисков. Этот API возвращает список изменений в базовом большом двоичном объекте по сравнению с моментальными снимками. Это позволяет уменьшить объем хранилища, используемого в учетной записи архивации. Этот API поддерживает страничные BLOB-объекты в хранилищах уровней "Премиум" и "Стандартный". С помощью данного API можно создавать более быстрые и эффективные решения архивации для виртуальных машин Azure. Это будет доступно в REST версии 2015-07-08 и выше.

Функция копирования добавочных моментальных снимков позволяет копировать из одной учетной записи хранения в другую разницу:

-   между базовым большим двоичным объектом и его моментальным снимком ИЛИ
-   между любыми двумя моментальными снимками базового большого двоичного объекта.

Ниже указаны поддерживаемые условия.

- BLOB-объект был создан 1 января 2016 г. или позже.
- BLOB-объект не был перезаписан с помощью операций [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) или [копирования BLOB-объекта](https://msdn.microsoft.com/library/azure/dd894037.aspx) между двумя моментальными снимками.


**Примечание**. Эта функция доступна для страничных BLOB-объектов Azure уровня "Премиум" или "Стандартный".

При наличии пользовательской стратегии архивации, использующей моментальные снимки, копирование моментальных снимков из одной учетной записи хранения в другую может выполняться очень медленно и требует много емкости хранилища. Вместо копирования всего моментального снимка в учетную запись хранения архивов вы можете записывать в архивный страничный BLOB-объект только данные, изменившиеся между последовательными снимками. Это значительно сокращает время копирования и емкость хранилища для архивов.

### Реализация копирования добавочных моментальных снимков

Копирование добавочных моментальных снимков можно реализовать, выполнив следующее.

-   Создайте моментальный снимок базового BLOB-объекта с помощью операции [создания моментального снимка BLOB-объекта](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Затем скопируйте этот моментальный снимок в архивную целевую учетную запись хранения с помощью операции [копирования BLOB-объекта](https://msdn.microsoft.com/library/azure/dd894037.aspx). Это будет архивный страничный BLOB-объект. Сделайте моментальный снимок этого архивного страничного BLOB-объекта и сохраните его в архивной учетной записи.
-   Создайте другой моментальный снимок базового BLOB-объекта с помощью операции создания моментального снимка BLOB-объекта.
-   Получите разницу между первым и вторым моментальными снимками базового BLOB-объекта с помощью операции [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Используйте новый параметр **prevsnapshot**, чтобы указать значение DateTime моментального снимка, разницу с которым вы хотите получить. При наличии этого параметра ответ REST будет содержать только страницы, которые были изменены в целевом моментальном снимке с момента создания предыдущего моментального снимка, включая пустые страницы.
-   С помощью операции [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) примените эти изменения к архивному страничному BLOB-объекту.
-   Наконец, создайте моментальный снимок архивного страничного BLOB-объекта и сохраните его в архивной учетной записи хранения.

В следующем разделе мы более подробно опишем, как хранить архивы дисков с помощью копирования добавочных моментальных снимков.

## Сценарий

В этом разделе описывается сценарий, включающий в себя пользовательскую стратегию архивации для дисков виртуальной машины с помощью моментальных снимков.

Рассмотрим виртуальную машину Azure серии DS с подключенным диском P30 хранилища уровня "Премиум". Диск P30 называется *mypremiumdisk*, он хранится в учетной записи хранения класса "Премиум", которая именуется *mypremiumaccount*. Учетная запись хранения класса "Стандартный" *mybackupstdaccount* будет использоваться для хранения архива *mypremiumdisk*. Нам нужно сохранять моментальный снимок *mypremiumdisk* каждые 12 часов.

Сведения о создании учетной записи хранения и дисках см. в статье [Об учетных записях службы хранилища Azure](storage-create-storage-account.md).

Сведения об архивации виртуальных машин Azure см. в статье [Планирование инфраструктуры резервного копирования виртуальных машин в Azure](../backup/backup-azure-vms-introduction.md).

## Хранение архивов диска с помощью добавочных моментальных снимков

Ниже описано, как создавать моментальные снимки *mypremiumdisk* и хранить архивы в *mybackupstdaccount*. Архив будет представлять собой стандартный страничный BLOB-объект с именем *mybackupstdpageblob*. Архивный страничный BLOB-объект всегда будет отражать состояние последнего моментального снимка *mypremiumdisk*.

1.  Сначала создайте архивный страничный BLOB-объект для диска хранилища класса "Премиум". Для этого создайте моментальный снимок *mypremiumdisk* с именем *mypremiumdisk\_ss1*.
2.  Скопируйте этот моментальный снимок в mybackupstdaccount как страничный BLOB-объект с именем *mybackupstdpageblob*.
3.  Создайте моментальный снимок *mybackupstdpageblob* с именем *mybackupstdpageblob\_ss1* с помощью операции [создания моментального снимка BLOB-объекта](https://msdn.microsoft.com/library/azure/ee691971.aspx) и сохраните его в *mybackupstdaccount*.
4.  Во время окна архивации создайте другой моментальный снимок *mypremiumdisk*, например *mypremiumdisk\_ss2*, и сохраните его в *mypremiumaccount*.
5.  Получите добавочные изменения между двумя снимками *mypremiumdisk\_ss2* и *mypremiumdisk\_ss1* с помощью операции [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) в *mypremiumdisk\_ss2*, где параметру **prevsnapshot** задано значение отметки времени *mypremiumdisk\_ss1*. Запишите эти добавочные изменения в архивный страничный BLOB-объект *mybackupstdpageblob*, расположенный в *mybackupstdaccount*. Если добавочные изменения содержат какие-либо удаленные диапазоны, их следует удалить из архивного страничного BLOB-объекта. С помощью операции [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) запишите добавочные изменения в архивный страничный BLOB-объект.
6.  Создайте моментальный снимок архивного страничного BLOB-объекта *mybackupstdpageblob* с именем *mybackupstdpageblob\_ss2*. Удалите предыдущий моментальный снимок *mypremiumdisk\_ss1* из учетной записи хранения класса "Премиум".
7.  Повторите шаги 4–6 в каждом окне архивации. Так вы сможете хранить архивы *mypremiumdisk* в учетной записи хранения класса "Стандартный".

![Архивация диска с помощью добавочных моментальных снимков](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## Восстановление диска из моментальных снимков

Ниже описано, как восстановить диск класса "Премиум" *mypremiumdisk* до более раннего моментального снимка из архивной учетной записи хранения *mybackupstdaccount*.

1.  Определите момент времени, на который вы хотите восстановить диск уровня "Премиум". Предположим, что это моментальный снимок *mybackupstdpageblob\_ss2*, который хранится в архивной учетной записи хранения *mybackupstdaccount*.
2.  В mybackupstdaccount повысьте уровень моментального снимка *mybackupstdpageblob\_ss2* до нового архивного базового страничного BLOB-объекта *mybackupstdpageblobrestored*.
3.  Создайте моментальный снимок архивного страничного BLOB-объекта с именем *mybackupstdpageblob\_ss1*.
4.  Скопируйте восстановленный страничный BLOB-объект *mybackupstdpageblobrestored* из *mybackupstdaccount* в *mypremiumaccount* как новый диск класса "Премиум" *mypremiumdiskrestored*.
5.  Создайте моментальный снимок *mypremiumdiskrestored* с именем *mypremiumdiskrestored\_ss1* для дальнейшей добавочной архивации.
6.  Укажите виртуальной машине серии DS восстановленный диск *mypremiumdiskrestored* и отключите от нее старый *mypremiumdisk*.
7.  Начните процесс архивации, описанный в предыдущем разделе, для восстановленного диска *mypremiumdiskrestored*, используя *mybackupstdpageblobrestored* в качестве архивного страничного BLOB-объекта.

![Восстановление диска из моментальных снимков](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## Дальнейшие действия

Дополнительные сведения о создании моментальных снимков BLOB-объектов и планировании инфраструктуры резервного копирования виртуальных машин см. в приведенных ниже статьях.

- [Создание моментального снимка большого двоичного объекта](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Планирование инфраструктуры резервного копирования виртуальных машин в Azure](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0921_2016-->