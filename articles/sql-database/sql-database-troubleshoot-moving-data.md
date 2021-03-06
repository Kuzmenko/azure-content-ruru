<properties
	pageTitle="Перенос баз данных между серверами, между подписками, в службу Azure и из нее"
	description="Быстрый порядок копирования, перемещения и переноса данных и баз данных в базу данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# Перенос баз данных между серверами, между подписками, в среду Azure и из нее

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##Перенос базы данных на другой сервер в пределах одной подписки
- На [портале Azure](https://portal.azure.com) щелкните пункт **Базы данных SQL**, выберите базу данных из списка и нажмите кнопку **Копировать**. Дополнительные сведения см. в статье [Копирование базы данных SQL Azure](sql-database-copy.md).

## Перенос базы данных из одной подписки в другую
- На [портале Azure](https://portal.azure.com) щелкните **Серверы SQL** и выберите в списке сервер, на котором размещена ваша база данных. Щелкните **Перенести** и выберите ресурсы, которые нужно перенести, а также подписку, в которую их нужно переместить.

## Перенос базы данных SQL в Azure
- Определите совместимость баз данных и выберите подходящий способ переноса. Выполните инструкции и настройте параметры [мастера переноса базы данных SQL Server](sql-database-cloud-migrate.md).

## Создание копии базы данных для использования за пределами Azure
- [Экспортируйте файл BACPAC.](sql-database-export.md)

<!---HONumber=AcomDC_0914_2016-->