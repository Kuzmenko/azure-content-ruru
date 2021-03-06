<properties 
   pageTitle="Отработки аварийного восстановления базы данных SQL | Microsoft Azure" 
   description="Руководство и рекомендации по использованию базы данных SQL Azure для отработки аварийного восстановления, которая обеспечивает устойчивость критически важных бизнес-приложений против сбоев и простоев." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/31/2016"
   ms.author="mihaelab"/>

#Отработка аварийного восстановления

Рекомендуется периодически проверять готовность приложений к рабочему процессу восстановления. Проверка поведения приложения и возможных последствий потери данных и нарушений работы, которая включает отработку отказа, предусмотрена стандартами разработки. Эта процедура также является обязательной для большинства отраслевых стандартов в рамках сертификации непрерывности бизнес-процессов.

Этапы отработки аварийного восстановления:

- моделирование сбоя уровня данных;
- восстановление;
- проверка целостности приложения после восстановления.

Рабочий процесс отработки зависит от того, как вы [спроектировали приложение для обеспечения непрерывности бизнес-процессов](sql-database-business-continuity.md). Ниже приведены рекомендации по отработке аварийного восстановления с использованием базы данных SQL Azure.

##Геовосстановление

Чтобы избежать потери данных при проведении отработки аварийного восстановления, рекомендуется выполнять отработку в тестовой среде. Для этого создайте копию рабочей среды и используйте ее для проверки рабочего процесса отработки отказа приложения.
 
####Моделирование сбоя

Для имитации сбоя можно удалить или переименовать базу данных-источник. Это вызовет сбой подключения приложения.

####Восстановление

- Выполните геовосстановление базы данных на другой сервер, следуя инструкциям [здесь](sql-database-disaster-recovery.md).
- Измените конфигурацию приложения, чтобы подключиться к восстановленной базе данных (или базам данных), и следуйте инструкциям в руководстве [по настройке базы данных после восстановления](sql-database-disaster-recovery.md), чтобы завершить восстановление.

####Проверка

- Выполните отработку, проверив целостность приложения после восстановления (строки подключения, учетные данные, тестирование основных функций или другие проверки стандартной процедуры утверждения приложений).

##Георепликация

Для базы данных, защищенной с помощью георепликации, в тренировочном упражнении предусмотрена плановая отработка отказа на базу данных-получатель. Плановая отработка отказа гарантирует, что база данных-источник и база данных-приемник останутся синхронизированы после переключения ролей. В отличие от незапланированной отработки отказа, эта операция не приведет к потере данных, поэтому отработку можно выполнить в рабочей среде.

####Моделирование сбоя

Для имитации сбоя можно отключить веб-приложение или виртуальную машину, подключение к базе данных. Это приведет сбоям подключения веб-клиентов.

####Восстановление

- Убедитесь, что конфигурация приложения в регионе аварийного восстановления указывает на бывшую базу данных-получатель, которая станет полностью доступной новой базой данных-источником.
- Выполните [плановую отработку отказа](sql-database-geo-replication-powershell.md#initiate-a-planned-failover), чтобы база данных-получатель стала новой базой данных-источником.
- Следуйте инструкциям в руководстве [по настройке базы данных после восстановления](sql-database-disaster-recovery.md), чтобы завершить восстановление.

####Проверка

- Выполните отработку, проверив целостность приложения после восстановления (строки подключения, учетные данные, тестирование основных функций или другие проверки стандартной процедуры утверждения приложений).


## Дальнейшие действия

- Чтобы изучить сценарии обеспечения непрерывности бизнес-процессов, ознакомьтесь со [сценариями обеспечения непрерывности](sql-database-business-continuity.md).
- Чтобы узнать об автоматически создаваемых резервных копиях базы данных SQL Azure, ознакомьтесь с разделом [Общие сведения об автоматическом резервном копировании базы данных SQL](sql-database-automated-backups.md).
- Чтобы узнать об использовании создаваемых автоматически резервных копий для восстановления, ознакомьтесь с [восстановлением базы данных из резервных копий, инициируемых службой](sql-database-recovery-using-backups.md).
- Чтобы узнать о более быстрых вариантах восстановления, ознакомьтесь с [активной георепликацией](sql-database-geo-replication-overview.md).

<!---HONumber=AcomDC_0803_2016-->