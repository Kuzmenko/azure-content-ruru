<properties
   pageTitle="Общие сведения по обеспечению безопасности в ";Интернете вещей"; | Microsoft Azure"
   description=" Службы ";Интернета вещей"; (IoT) Azure предлагают широкий спектр возможностей. Эта статья поможет вам понять, как защитить свои решения IoT в Azure. "
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# Общие сведения по обеспечению безопасности в "Интернете вещей"

Службы "Интернета вещей" (IoT) Azure предлагают широкий спектр возможностей. Это службы корпоративного уровня, которые обеспечивают:

- сбор данных с устройств;
- анализ движения потоков данных;
- хранение больших наборов данных и создание запросов к ним;
- визуализацию данных, получаемых в реальном времени, и архивных данных;
- интеграцию с системами операционных отделов организации.

Для реализации этих возможностей в Azure IoT Suite собраны вместе службы Azure в сочетании с пользовательскими расширениями, образуя предварительно настроенные решения. Эти предварительно настроенные решения являются базовой реализацией стандартных шаблонов решений IoT, позволяющих сократить время разработки решений IoT. С помощью наборов средств для разработки программного обеспечения IoT можно настроить и расширить эти решения в соответствии с вашими требованиями. Эти решения также можно использовать как примеры и шаблоны при разработке новых решений IoT.

Пакет Azure IoT — это мощное решение для потребностей IoT. Тем не менее очень важно обеспечить безопасность решений IoT еще на стадии разработки. Из-за большого количества устройств IoT любые проблемы с безопасностью могут быстро перерасти в широкомасштабное событие с серьезными последствиями.

Чтобы разобраться в том, как защитить свои решения IoT, мы предлагаем вам следующую информацию.

## Архитектура безопасности

При разработке системы важно знать угрозы, которым она может подвергаться, а по завершении разработки и создания архитектуры предусмотреть надлежащие средства ее защиты. Особенно важно спланировать стратегию безопасности в самом начале разработки продукта, ведь зная, как злоумышленники могут скомпрометировать систему, можно изначально устранить соответствующие риски.

Сведения об архитектуре безопасности IoT представлены в статье [Архитектура безопасности "Интернета вещей"](../iot-suite/iot-security-architecture.md).

В этой статье рассматриваются следующие темы.

- [Обеспечение безопасности начинается с рассмотрения модели рисков](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
- [Безопасность в IoT](../iot-suite/iot-security-architecture.md#security-in-iot)
- [Моделирование рисков эталонной архитектуры Azure IoT](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## Все аспекты безопасности

В результате использования "Интернета вещей" предприятия во всем мире сталкиваются с уникальными проблемами, связанными с обеспечением безопасности, конфиденциальности и соответствия требованиям. В традиционных виртуальных технологиях эти вопросы связаны с использованием программного обеспечения и способами его внедрения. IoT имеет дело с проблемами, которые возникают на стыке виртуального и физического миров. Чтобы защитить решения IoT, нужно обеспечить безопасность подготовки устройств и их подключения к облаку, а также конфиденциальность данных в облаке во время обработки и хранения. Этому препятствует наличие устройств с ограниченными ресурсами, географическое распределение развертываний и большое количество используемых в решении устройств.

Сведения о том, как обеспечить безопасность в данных областях, представлены в статье [Полная безопасность в Интернете вещей](../iot-suite/securing-iot-ground-up.md).

В этой статье рассматриваются следующие темы.

- [Безопасная инфраструктура на всех уровнях](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
- [Microsoft Azure — защищенная инфраструктура IoT для вашего бизнеса](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## Рекомендации

Обеспечение безопасности инфраструктуры "Интернета вещей" (IoT) требует тщательно продуманной стратегии. С каждым уровнем общая безопасность инфраструктуры становится надежнее — от защиты данных в облаке и до защиты целостности данных при передаче через общедоступный Интернет, а также обеспечения безопасной подготовки устройств к работе.

Рекомендации по обеспечению безопасности "Интернета вещей" представлены в статье [Рекомендации по обеспечению безопасности "Интернета вещей"](../iot-suite/iot-security-best-practices.md).

В этой статье рассматриваются следующие темы.

- [Производитель или интегратор оборудования IoT](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
- [Разработчик решений IoT](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
- [Специалист по развертыванию решений IoT](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
- [Оператор решений IoT](../iot-suite/iot-security-best-practices.md#iot-solution-operator)

<!---HONumber=AcomDC_0810_2016-->