---
title: "Проектирование и реализация служб | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
helpviewer_keywords: 
  - "определение контрактов службы [WCF]"
ms.assetid: 036fae20-7c55-4002-b71d-ac4466e167a3
caps.latest.revision: 37
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 37
---
# Проектирование и реализация служб
В этом разделе показывается, как определить и реализовать контракты [!INCLUDE[indigo2](../../../includes/indigo2-md.md)].Контракт службы указывает, какую информацию конечная точка передает во внешний мир.На более конкретном уровне, это выписка о наборе специальных сообщений, объединенных в такие базовые шаблоны обмена сообщениями \(MEP\), как запрос\/ответ, односторонний обмен и дуплексный обмен.Если контракт службы является логически связанным набором обмена сообщениями, операция службы — это обмен одним сообщением.Например, операция `Hello` должна явно принять одно сообщение \(к примеру, вызывающая сторона может объявить приветствие\) и может вернуть или не вернуть сообщение \(в зависимости от характера операции\).  
  
 Дополнительные сведения о контрактах и других базовых концепциях [!INCLUDE[indigo1](../../../includes/indigo1-md.md)] см. в разделе [Основные понятия Windows Communication Foundation](../../../docs/framework/wcf/fundamental-concepts.md).В данном разделе рассматриваются контракты служб.Дополнительные сведения о создании клиентов, использующих контракты служб для подключения к службам, см. в разделе [Общие сведения о клиентах WCF](../../../docs/framework/wcf/wcf-client-overview.md).  
  
## Общие сведения  
 В этом разделе дается высокоуровневое концептуальное введение в разработку и реализацию служб [!INCLUDE[indigo2](../../../includes/indigo2-md.md)].В подразделах приводятся более подробные сведения об особенностях разработки и реализации.Перед разработкой и реализацией приложения [!INCLUDE[indigo2](../../../includes/indigo2-md.md)] рекомендуется:  
  
-   понять, что представляет собой контракт службы, как он работает и как его создать;  
  
-   понять, что контракты устанавливают минимальные требования, которые конфигурация среды выполнения или среда размещения может не поддерживать.  
  
## Контракты служб  
 В контракте службы определено следующее:  
  
-   операции, предоставляемые контрактом;  
  
-   сигнатура операций в терминах обмена сообщениями;  
  
-   типов данных этих сообщений;  
  
-   расположения операций;  
  
-   специальных протоколов и форматов сериализации, используемых для поддержки успешной связи со службой.  
  
 Например, контракт, связанный с заказом на поставку, может содержать операцию `CreateOrder`, которая принимает входные данные о типах информации заказа и возвращает информацию об успешности или сбое, включая идентификатор заказа.Он также может содержать операцию `GetOrderStatus`, которая принимает идентификатор заказа и возвращает информацию о состоянии заказа.Контракт службы этого типа указывает:  
  
1.  что контракт, связанный с заказом на поставку, состоит из операций `CreateOrder` и `GetOrderStatus`;  
  
2.  что операции имеют заданные входные и выходные сообщения;  
  
3.  данные, которые могут передаваться в этих сообщениях;  
  
4.  категорические заявления об инфраструктуре связи, необходимые для успешной обработки сообщений.Например, эти сведения включают данные о том, требуются ли какие\-либо формы безопасности для установления успешной связи, и какие конкретно формы безопасности.  
  
 Чтобы передать сведения этого типа в другие приложения на нескольких платформах \(включая платформы, отличные от Майкрософт\), контракты XML\-служб открыто представляются в стандартных форматах XML, например в формате [языка описания веб\-служб](http://go.microsoft.com/fwlink/?LinkId=94952) \(WSDL\), [схемы XML \(XSD\)](http://go.microsoft.com/fwlink/?LinkId=94953) и других форматах.Разработчики многих платформ могут использовать эти открытые данные контрактов для создания приложений, которые могут взаимодействовать со службой, поскольку они понимают язык спецификации, и эти языки позволяют обеспечить возможность взаимодействия, описывая открытые формы, форматы и протоколы, поддерживаемые службой.Дополнительные сведения о том, как [!INCLUDE[indigo2](../../../includes/indigo2-md.md)] обрабатывает сведения этого типа, см. в разделе [Метаданные](../../../docs/framework/wcf/feature-details/metadata.md).  
  
 Контракты могут представляться различными способами. Хотя языки WSDL и XSD отлично подходят для описания служб доступным способом, их трудно использовать непосредственно, и они представляют просто описания службы, а не реализации контракта службы.Поэтому приложения [!INCLUDE[indigo2](../../../includes/indigo2-md.md)] используют управляемые атрибуты, интерфейсы и классы для определения структуры службы и ее реализации.  
  
 Получающийся контракт, определенный в управляемых типах, может быть *экспортирован* как метаданные \(WSDL и XSD\), когда это требуется клиентам или другим ответственным за реализацию службы.Результат — простая модель программирования, которая может быть описана \(с использованием открытых метаданных\) для любого клиентского приложения.Подробности базовых сообщений SOAP, сведения, связанные с транспортировкой и безопасностью, и т. д. могут быть предоставлены системе [!INCLUDE[indigo2](../../../includes/indigo2-md.md)], которая автоматически выполняет необходимые преобразования в систему типа контракта службы и из системы типов контракта службы в систему типа XML.  
  
 Дополнительные сведения о разработке контрактов см. в разделе [Создание контрактов служб](../../../docs/framework/wcf/designing-service-contracts.md).Дополнительные сведения о реализации контрактов см. в разделе [Реализация контрактов служб](../../../docs/framework/wcf/implementing-service-contracts.md).  
  
### На первом плане — сообщения  
 Использовать управляемые интерфейсы, классы и методы для моделирования операций служб просто, если программист привык к сигнатурам методов стиля удаленного вызова процедур \(RPC\), когда передача параметров в метод и получение возвращаемых значений является обычной формой запроса функций от объекта или другого типа кода.Например, программисты, использующие управляемые языки типа [!INCLUDE[vbprvb](../../../includes/vbprvb-md.md)] и C\+\+ COM, могут применять свои знания подхода стиля RPC \(при использовании как объектов, так и интерфейсов\) для создания контрактов служб [!INCLUDE[indigo2](../../../includes/indigo2-md.md)], не испытывая проблем, встречающихся в системах распределенных объектов стиля RPC.Ориентация службы обеспечивает преимущества слабосвязанного программирования, ориентированного на сообщения, сохраняя при этом удобство и привычность программирования RPC.  
  
 Многим программистам удобнее работать с интерфейсами программирования приложений, ориентированными на сообщения, например с очередями сообщений типа MSMQ корпорации Майкрософт, пространствами имен <xref:System.Messaging> в платформе .NET Framework или с передачей неструктурированных данных XML в запросах HTTP для именования незначительного количества объектов.Дополнительные сведения о программировании на уровне сообщений см. в разделах [Использование контрактов сообщений](../../../docs/framework/wcf/feature-details/using-message-contracts.md), [Программирование служб на уровне канала](../../../docs/framework/wcf/extending/service-channel-level-programming.md) и [Взаимодействие с приложениями POX](../../../docs/framework/wcf/feature-details/interoperability-with-pox-applications.md).  
  
### Понимание иерархии требований  
 В контракте службы группируются операции, задаются шаблон обмена сообщениями, типы сообщений и типы данных, передаваемых в этих сообщениях, а также указываются категории поведения во время выполнения, которые должны быть предусмотрены в реализации для поддержки контракта \(например, может требоваться, чтобы сообщения шифровались и подписывались\).В самом контракте службы точно не указывается, как эти требования удовлетворяются, а указывается только то, что они должны быть удовлетворены.Тип шифрования или способ, с помощью которого подписывается сообщение, соответствует реализации и конфигурации совместимой службы.  
  
 Обратите внимание на то, каким способом контракт предъявляет определенные требования к реализации контракта службы и конфигурации среды выполнения для добавления поведения.Набор требований, которые должны быть удовлетворены для передачи службы в использование, основывается на предыдущем наборе требований.Если контракт предъявляет некоторые требования к реализации, реализация может предъявлять дополнительные требования к конфигурации и привязкам, которые позволяют запустить службу.Наконец, ведущее приложение должно также поддерживать любые требования, добавляемые конфигурацией и привязками службы.  
  
 Этот аддитивный процесс предъявления требований важно иметь в виду при разработке, реализации, настройке и размещении приложения службы [!INCLUDE[indigo1](../../../includes/indigo1-md.md)].Например, в контракте может указываться, что ему требуется для поддержки сеанса.В этом случае необходимо настроить привязку для поддержки этого требования контракта, иначе реализация службы работать не будет.Если же служба требует встроенной проверки подлинности Windows и размещается в службах IIS, в веб\-приложении, где находится служба, должна быть включена встроенная проверка подлинности Windows и отключена поддержка анонимных обращений.Дополнительные сведения о функциях и влиянии различных типов ведущих приложений служб см. в разделе [Размещение служб](../../../docs/framework/wcf/hosting-services.md).  
  
## См. также  
 [Создание контрактов служб](../../../docs/framework/wcf/designing-service-contracts.md)   
 [Реализация контрактов служб](../../../docs/framework/wcf/implementing-service-contracts.md)