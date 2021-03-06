<properties 
   pageTitle="Заметки о выпуске обновлений для виртуального массива StorSimple | Microsoft Azure"
   description="В этой статье описаны критические открытые проблемы виртуального массива StorSimple с обновлением 0.3 и способы их устранения."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2016"
   ms.author="alkohli" />

# Заметки о выпуске обновления 0.3 для виртуального массива StorSimple

## Обзор

В следующих заметках о выпуске перечислены важные нерешенные и устраненные проблемы, связанные с обновлениями для виртуального массива Microsoft Azure StorSimple.

Заметки о выпуске постоянно обновляются: обнаруживаются и добавляются критические проблемы, требующие временного решения. Перед развертыванием виртуального массива StorSimple внимательно изучите информацию, содержащуюся в заметках о выпуске.

Обновление 0.3 соответствует версии программного обеспечения **10.0.10288.0**.

> [AZURE.NOTE] Обновления нарушают работу системы и приводят к перезагрузке устройства. Если выполняются какие-либо операции ввода-вывода, то устройство находится в состоянии простоя.


## Новые возможности в обновлении 0.3

Обновление 0.3 — это в первую очередь сборка для исправления ошибок. В частности, в данной версии исправлено несколько ошибок, приводивших в предыдущей версии к сбоям архивации.

## Проблемы, устраненные в обновлении 0.3

В таблице ниже содержится список проблем, устраненных в этом выпуске.

| Нет. | Функция | Проблема |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Резервные копии |В предыдущем выпуске была обнаружена проблема: сбой при архивации файловых ресурсов. Если возникала эта ошибка, то происходил сбой задания архивации и в службе диспетчера StorSimple создавалось критическое оповещение для уведомления пользователя. Эта ошибка не влияла на данные в общих ресурсах или на доступ к этим данным. В данном выпуске основная причина была определена и устранена. <br></br> Это исправление не может быть применено задним числом к общим папкам, в которых уже наблюдается эта проблема. Пользователи, у которых наблюдается эта проблема, для ее устранения должны сначала применить обновление 0.3, а затем обратиться в службу поддержки Майкрософт, чтобы выполнить полную архивацию системы. Вместо обращения в службу поддержки Майкрософт пользователи также могут выполнить восстановление затронутых этой проблемой общих папок из работоспособного архива в новую общую папку. |
| 2 | iSCSI | В предыдущей версии была обнаружена проблема исчезновения томов при копировании данных в том виртуального массива StorSimple. В данном выпуске эта проблема исправлена. <br></br> Исправления вступают в силу только в отношении вновь создаваемых томов. Это исправление не может быть применено задним числом к томам, в которых уже наблюдается эта проблема. Пользователям рекомендуется подключить затронутые этой проблемой тома через классический портал Azure, выполнить архивацию этих томов, а затем восстановить их в новые тома. |


## Известные проблемы в обновлении 0.3

В следующей таблице представлен список известных проблем для виртуального массива StorSimple, а также содержатся проблемы для предыдущих выпусков, добавленные в заметки о выпуске.


| Нет. | Функция | Проблема | Временное решение и комментарии |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Обновления | Виртуальные устройства, созданные в предварительной версии, нельзя обновить до поддерживаемой общедоступной версии. | Для таких виртуальных устройств необходимо выполнить отработку отказа с переносом в общедоступную версию, используя процедуру аварийного восстановления. |
| **2.** | Подготовленный диск данных | После подготовки диска данных определенного размера и создания соответствующего виртуального устройства StorSimple не следует расширять или уменьшать этот диск. В противном случае это приведет к потере всех данных на локальных уровнях устройства. | |
| **3.** | Групповая политика | Если устройство присоединено к домену, применение групповой политики может отрицательно сказаться на работе устройства. | Убедитесь, что виртуальный массив находится в собственном подразделении (OU) для Active Directory и никакие объекты групповой политики (GPO) к нему не применяются.|
| **4.** | Локальный пользовательский веб-интерфейс | Если в Internet Explorer включена функция усиленной безопасности, некоторые страницы локального пользовательского веб-интерфейса, такие как "Устранение неполадок" или "Обслуживание", могут работать неправильно. Кроме этого, на этих страницах могут не работать кнопки. | Отключите функцию усиленной безопасности в Internet Explorer.|
| **5.** | Локальный пользовательский веб-интерфейс | Сетевые интерфейсы виртуальной машины Hyper-V в пользовательском веб-интерфейсе отображаются как интерфейсы со скоростью передачи 10 Гбит/с. | Эта особенность виртуальной машины Hyper-V. Для виртуальных сетевых адаптеров в этой машине всегда отображается скорость 10 Гбит/с. |
| **6.** | Многоуровневые тома или общие папки | Блокировка диапазона байтов для приложений, работающих с многоуровневыми томами StorSimple, не поддерживается. Если блокировка диапазона байтов включена, то распределение по уровням StorSimple не работает. | Рекомендованные меры включают следующие. <br></br>Отключите блокировку диапазона байтов в логике приложения.<br></br>Выберите помещение данных для этого приложения в локально закрепленные тома, а не в многоуровневые тома. <br></br>*Ограничение*. При использовании локально закрепленных томов с включенной блокировкой диапазона байтов локально закрепленный том может быть подключен к сети еще до завершения восстановления. В таких случаях нужно дождаться завершения восстановления. |
| **7.** | Многоуровневые общие папки | Иногда при работе с большими файлами распределение по уровнях выполняется очень медленно. | При работе с файлами большого размера рекомендуется, чтобы размер самого большого файла составлял не более 3 % от размера общей папки. |
| **8.** | Используемая емкость общей папки | Использование общей папки можно наблюдать даже при отсутствии в ней данных. Это связано с тем, что используемая емкость общих папок включает в себя метаданные. | |
| **9.** | Аварийное восстановление | Аварийное восстановление файлового сервера можно выполнить только в том же домене, в котором находится исходное устройство. Аварийное восстановление на целевое устройство в другом домене не поддерживается в этом выпуске. | Эта возможность реализована в более позднем выпуске. |
| **10.** | Azure PowerShell | В этом выпуске нельзя управлять виртуальным устройством StorSimple с помощью Azure PowerShell. | Виртуальным устройством можно управлять с помощью классического портала Azure и локального пользовательского веб-интерфейса. |
| **11.** | Изменение пароля | Консоль устройства виртуальных массивов принимает только входные данные в формате клавиатуры ru-RU. | |
| **12.** | CHAP | После создания учетных данных CHAP удалить их нельзя. Кроме того, при изменении учетных данных CHAP необходимо перевести тома в автономный режим, а затем снова перевести их в сетевой режим, чтобы изменения вступили в силу. | Эта проблема устраняется в более позднем выпуске. |
| **13.** | Сервер iSCSI | Объем, отображаемый в поле "Используемое хранилище" для локально закрепленных томов, может отличаться в службе диспетчера StorSimple и на узле iSCSI. | На узле iSCSI используется представление файловой системы.<br></br>Устройство видит блоки, выделенные в тот момент, когда размер тома был максимальным.|
| **14.** | Файловый сервер | Если с файлом в папке связан альтернативный поток данных (ADS), то этот поток данных не архивируется и не восстанавливается при аварийном восстановлении, клонировании и восстановлении на уровне элементов.| |


## Дальнейшие действия

[Установите обновление 0.3](storsimple-ova-install-update-01.md) на виртуальный массив StorSimple.

## Ссылки

Ищите заметки о более старом выпуске? Справочные ресурсы:

- [Заметки о выпуске обновлений 0.1 и 0.2 для виртуального массива StorSimple.](storsimple-ova-update-01-release-notes.md)

- [Заметки о выпуске общедоступной версии виртуального массива StorSimple.](storsimple-ova-pp-release-notes.md)
 

<!---HONumber=AcomDC_0921_2016-->