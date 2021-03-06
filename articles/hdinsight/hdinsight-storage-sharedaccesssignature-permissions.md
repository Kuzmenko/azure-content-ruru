<properties
pageTitle="Ограничение доступа HDInsight к данным с использованием подписанных URL-адресов"
description="Узнайте, как использовать подписанные URL-адреса для ограничения доступа HDInsight к данным, хранящимся в хранилищах BLOB-объектов."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/25/2016"
ms.author="larryfr"/>

#Использование подписанных URL-адресов хранилища Azure для ограничения доступа к данным с помощью HDInsight

HDInsight использует для хранения данных хранилище BLOB-объектов Azure. HDInsight требуется полный доступ к BLOB-объекту, который используется для хранения кластера по умолчанию, однако доступ к данным, хранящимся в других используемых кластером BLOB-объектах, можно ограничить. Например, некоторые данные можно сделать доступными только для чтения. Для этого используются подписанные URL-адреса.

Подписанные URL-адреса (SAS) — это функция учетных записей хранения Azure, позволяющая ограничивать доступ к данным, например предоставлять доступ к данным только для чтения. Из этого документа вы узнаете, как использовать SAS в HDInsight для предоставления доступа к контейнеру BLOB-объектов только для чтения и включения в списки.

##Требования

* Подписка Azure

* C# или Python. Пример кода на C# предоставлен в решении Visual Studio.

    * Требуется Visual Studio версии 2013 или 2015.
    
    * Требуется Python версии 2.7 или более новой.

* Кластер HDInsight под управлением Linux ИЛИ [Azure PowerShell][powershell]. Если у вас есть кластер под управлением Linux, можно добавить подписанный URL-адрес в кластер с помощью Ambari. Если нет, создайте новый кластер с помощью Azure PowerShell и добавьте подписанный URL-адрес в процессе создания кластера.

* Примеры файлов взяты из статьи на странице [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Этот репозиторий содержит следующее:

    * Проект Visual Studio, способный создать контейнер хранилища, хранимую политику и SAS для использования с HDInsight.
    
    * Сценарий Python, способный создать контейнер хранилища, хранимую политику и SAS для использования с HDInsight.
    
    * Сценарий PowerShell, способный создать новый кластер HDInsight и настроить его для использования SAS.

##Подписи коллективного доступа

Существуют две формы подписанных URL-адресов:

* Одноранговый. При создании однорангового подписанного URL-адреса время начала действия, время окончания действия и разрешения указываются в URI подписанного URL-адреса (или подразумеваются в случае, когда опускается время начала).

* Хранимая политика доступа. Хранимая политика доступа определяется в контейнере ресурсов (контейнере больших двоичных объектов, таблице, очереди или общей папке) и может использоваться для управления ограничениями одного или нескольких подписанных URL-адресов. При сопоставлении подписи общего доступа с хранимой политикой доступа эта подпись наследует ограничения (время начала, время окончания и разрешения), определенные для данной хранимой политики доступа.

Различие между этими двумя формами важно для одного ключевого сценария — отзыва. Подпись общего доступа — это URL-адрес, этой подписью может воспользоваться любой человек, который ее получил, независимо от того, кто изначально запрашивал такую подпись. Размещенный в свободном доступе подписанный URL-адрес сможет использовать любой человек. Распространяемая подпись общего доступа действительна до тех пор, пока не произойдет одно из четырех возможных событий:

1. Наступает время истечения срока действия, указанное для данной подписи общего доступа.

2. Наступает время истечения срока действия, указанное в хранимой политике доступа, на которую ссылается подпись общего доступа (если имеется ссылка на хранимую политику доступа, в которой задано время окончания срока действия). Это может произойти либо из-за истечения заданного времени, либо из-за изменения хранимой политики доступа и переноса времени окончания срока действия в прошлое, что является одним из способов отозвать подпись общего доступа.

3. Удаляется хранимая политика доступа, на которую ссылается подпись, что является другим способом отозвать подпись общего доступа. Заметьте, что при повторном создании хранимой политики доступа с таким же именем все существующие маркеры подписи общего доступа снова станут действительными в соответствии с разрешениями, связанными с этой хранимой политикой доступа (предполагается, что время окончания срока действия для подписи общего доступа еще не истекло). Если планируется отозвать подпись общего доступа, следует использовать другое имя при повторном создании политики доступа, у которой время окончания срока действия относится к будущему.

4. Повторно создается ключ учетной записи, который был использован для создания подписи общего доступа. Обратите внимание, что это приведет к неспособности всех компонентов приложения, которые используют этот ключ учетной записи, пройти проверку подлинности до тех пор, пока они не будут обновлены для использования другого действительного ключа учетной записи или вновь созданного ключа учетной записи.

> [AZURE.IMPORTANT] URI подписанного URL-адреса связан с ключом учетной записи, который использовался для создания подписи и соответствующей хранимой политики доступа (при наличии таковой). Если хранимая политика доступа не указана, то единственный способ отменить подписанный URL-адрес — изменить ключ учетной записи.

Подписанные URL-адреса рекомендуется использовать всегда — подписи можно отзывать или продлевать по истечении срока действия по мере необходимости. В инструкциях, приведенных в этом документе, для формирования подписанных URL-адресов используются хранимые политики доступа.

Дополнительные сведения о подписанных URL-адресах см. в статье [Общие сведения о модели SAS](../storage/storage-dotnet-shared-access-signature-part-1.md).

##Создание хранимой политики и формирование SAS

В настоящее время хранимые политики создаются программным способом. Примеры кода для создания хранимой политики и SAS на C# и Python см. на странице [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###Создание хранимой политики и SAS с использованием C#

1. Откройте решение в Visual Studio.

2. В обозревателе решений щелкните проект __SASToken__ правой кнопкой мыши и выберите пункт __Свойства__.

3. Выберите __Параметры__ и добавьте значения для следующих записей:

    * StorageConnectionString: строка подключения к учетной записи хранения, для которой необходимо создать хранимую политику и SAS. Требуется формат: `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`, где `myaccount` — это имя вашей учетной записи хранения, а `mykey` — ключ к учетной записи хранения.
    
    * ContainerName: контейнер в учетной записи хранения, доступ к которому необходимо ограничить.
    
    * SASPolicyName: имя, которое будет использоваться для создаваемой учетной записи хранения.
    
    * FileToUpload: путь к файлу для передачи в контейнер.
    
4. Запустите проект. После создания SAS появится окно консоли, содержащее информацию следующего вида:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Сохраните маркер политики SAS — он вам понадобится для связи учетной записи хранения с кластером HDInsight. Кроме того, вам потребуется имя учетной записи хранения и имя контейнера.
    
###Создание хранимой политики и SAS с использованием Python

1. Откройте файл SASToken.py и измените следующие значения:

    * policy\_name: имя, которое будет использоваться для создаваемой учетной записи хранения.
    
    * storage\_account\_name: имя вашей учетной записи хранения.
    
    * storage\_account\_key: ключ для учетной записи хранения.
    
    * storage\_container\_name: контейнер в учетной записи хранения, доступ к которому необходимо ограничить.
    
    * example\_file\_path: путь к файлу для передачи в контейнер.

2. Выполните скрипт. После того как сценарий будет выполнен, отобразится маркер SAS следующего вида:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Сохраните маркер политики SAS — он вам понадобится для связи учетной записи хранения с кластером HDInsight. Кроме того, вам потребуется имя учетной записи хранения и имя контейнера.
    
##Использование SAS с HDInsight

При создании кластера HDInsight необходимо указать основную учетную запись хранения; дополнительные учетные записи хранения указываются по желанию. Оба способа добавления хранилища требуют полного доступа к соответствующим учетным записям хранения и контейнерам.

Чтобы использовать подписанный URL-адрес для ограничения доступа к контейнеру, необходимо добавить пользовательскую запись в файл конфигурации__core-site__ для кластера.

* Для кластеров HDInsight __под управлением Windows__ или __Linux__ это можно сделать на этапе создания кластера с помощью PowerShell.

* При создании кластеров HDInsight __под управлением Linux__ с помощью Ambari конфигурация корректируется позднее.

###Создание нового кластера, использующего SAS

Пример создания кластера HDInsight, использующего SAS, включен в каталог `CreateCluster` репозитория. Чтобы воспользоваться этим примером, выполните указанные ниже действия.

1. Откройте файл `CreateCluster\HDInsightSAS.ps1` в текстовом редакторе и измените указанные ниже значения в начале документа.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Например, измените `'mycluster'` в имени кластера, который требуется создать. Значения SAS должны совпадать со значениями, которые использовались при создании учетной записи хранения и маркера SAS.
    
    Изменив значения, сохраните файл.

1. Откройте командную строку Azure PowerShell. Если вы не знакомы с модулем Azure PowerShell или он у вас не установлен, см. статью [Установка и настройка Azure PowerShell][powershell].

2. В командной строке выполните следующую команду для аутентификации в подписке Azure.

        Login-AzureRmAccount
    
    При появлении запроса войдите в систему, используя учетную запись своей подписки Azure.
    
    Если ваше имя для входа связано с несколькими подписками Azure, выберите нужную подписку с помощью командлета `Select-AzureRmSubscription`.

2. В командной строке измените каталоги на каталог `CreateCluster`, содержащий файл HDInsightSAS.ps1. Затем выполните следующую команду для запуска сценария:
        
        .\HDInsightSAS.ps1
    
    По мере выполнения сценария информация о создании группы ресурсов и учетных записей хранения появится в командной строке PowerShell. Затем вам будет предложено указать пользователя HTTP для кластера HDInsight. Это учетная запись пользователя, используемая для безопасного доступа к кластеру по HTTP/s.
    
    При создании кластера под управлением Linux запрашивается также имя учетной записи пользователя SSH и пароль. Эти данные используются для удаленного входа в кластер.
    
    > [AZURE.IMPORTANT] При появлении запроса на имя и пароль пользователя HTTP/s или SSH необходимо указать пароль, который отвечает следующим условиям:
    >
    > - содержит не меньше 10 символов;
    > - содержит хотя бы одну цифру;
    > - содержит хотя бы один специальный символ;
    > - содержит хотя бы одну букву в верхнем или нижнем регистре.


Обычно выполнение сценария занимает около 15 минут. Если сценарий выполнен без ошибок, значит, кластер создан.

###Обновление существующего кластера для использования SAS

Если у вас уже есть кластер под управлением Linux, SAS можно добавить в конфигурацию файла __core-site__, выполнив описанные ниже действия.

1. Откройте веб-интерфейс Ambari для вашего кластера. Адрес этой страницы — https://YOURCLUSTERNAME.azurehdinsight.net. При появлении запроса пройдите проверку подлинности, указав имя пользователя и пароль администратора, которые использовались при создании кластера.

2. Выберите в левой части экрана веб-интерфейс Ambari, __HDFS__ а затем вкладку __Конфигурации__ в центре страницы.

3. Откройте вкладку __Дополнительно__ и прокрутите экран до раздела __Настройка файла core-site__.

4. Разверните раздел __Настройка файла core-site__, прокрутите экран до конца и щелкните ссылку __Добавить свойство...__ В полях __Ключ__ и __Значение__ укажите следующие значения:

    * __Key__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Value__: SAS, выданный выполненным ранее приложением C# или Python.
    
    Замените __CONTAINERNAME__ на имя контейнера, используемого в приложении C# или SAS. Замените __STORAGEACCOUNTNAME__ на имя используемой учетной записи хранения.

5. Нажмите кнопку __Добавить__, чтобы сохранить этот ключ и значение, а затем кнопку __Сохранить__, чтобы сохранить изменения в конфигурации. При появлении запроса добавьте описание внесенного изменения (например, "Добавление доступа к хранилищу SAS") и нажмите кнопку __Сохранить__.

    Закончив вносить изменения, нажмите кнопку __ОК__.

    > [AZURE.IMPORTANT] Изменения будут сохранены, но для того, чтобы они вступили в силу, необходимо перезапустить несколько служб.

6. В веб-интерфейсе Ambari выберите __HDFS__ в списке слева, а затем параметр __Перезапустить все__ в раскрывающемся списке __Действия службы__ справа. При появлении запроса выберите __Включить режим обслуживания__ и выберите пункт "Подтвердить перезапуск".

    Повторите этот процесс для записей MapReduce2 и YARN в списке в левой части страницы.

7. Перезапустив эти службы, выберите одну из них и отключите режим обслуживания в раскрывающемся списке __Учетные записи обслуживания__.

##Тестирование ограниченного доступа

Чтобы убедиться в том, что доступ ограничен, используйте следующие методы:

* Для кластеров HDInsight __под управлением Windows__ подключитесь к кластеру с помощью удаленного рабочего стола. Дополнительные сведения см. в статье [Подключение к HDInsight с использованием RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Установив подключение, щелкните значок __командной строки Hadoop__ на рабочем столе, чтобы открыть командную строку.

* Для кластеров HDInsight __под управлением Linux__ подключитесь к кластеру с помощью SSH. Сведения об использовании SSH с кластерами под управлением Linux см. в одной из следующих статей.

    * [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, OS X или Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Установив подключение к кластеру, выполните описанные ниже действия, чтобы убедиться в том, что элементы в учетной записи хранения SAS доступны только для чтения и включения в списки.

1. Когда появится запрос, введите следующую команду: Замените __SASCONTAINER__ на имя контейнера, созданного для учетной записи хранения SAS. Замените __SASACCOUNTNAME__ на имя учетной записи хранения, используемой для SAS:

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Появится список содержимого контейнера, который должен содержать файл, загруженный при создании контейнера и SAS.
    
2. Проверьте, можете ли вы прочитать содержимое файла, выполнив следующую команду: Замените __SASCONTAINER__ и __SASACCOUNTNAME__, как в предыдущем действии. Замените __FILENAME__ на имя файла, отображенное в предыдущей команде:

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    На экране появится содержимое файла.
    
3. Загрузите файл в локальную файловую систему, выполнив следующую команду:

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Файл будет загружен на локальный компьютер с именем __testfile.txt__.

4. Передайте локальный файл в новый файл с именем __testupload.txt__ в хранилище SAS, выполнив следующую команду:

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Должно появиться сообщение следующего вида:
    
        put: java.io.IOException
        
    Эта ошибка возникает из-за того, что расположение хранилища доступно только для чтения и включения в списки. Разместите данные в хранилище по умолчанию доступного для записи кластера, выполнив следующую команду:
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    На этот раз операция должна завершиться успешно.
    
##Устранение неполадок

###Задача была отменена

__Проблема__. При создании кластера с помощью сценария PowerShell может появиться следующее сообщение об ошибке:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Причина__. Эта ошибка может возникать при использовании пароля для администратора или пользователя HTTP кластера, либо (в случае кластеров под управлением Linux) пользователя SSH.

__Решение__. Используйте пароль, отвечающий следующим требованиям:

- содержит не меньше 10 символов;
- содержит хотя бы одну цифру;
- содержит хотя бы один специальный символ;
- содержит хотя бы одну букву в верхнем или нижнем регистре.

##Дальнейшие действия

Теперь, когда вы узнали, как добавить хранилище с ограниченным доступом в кластер HDInsight, ознакомьтесь с другими способами работы с данными в вашем кластере:

* [Использование Hive с HDInsight](hdinsight-use-hive.md)

* [Использование Pig с HDInsight](hdinsight-use-pig.md)

* [Использование MapReduce с HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0914_2016-->