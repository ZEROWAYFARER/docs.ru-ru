---
title: "System.ServiceModel.Channels.FailedPipeConnect | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 9a827e0f-fb91-46bb-bd54-926d4b74d8a6
caps.latest.revision: 6
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 6
---
# System.ServiceModel.Channels.FailedPipeConnect
Сбой попытки соединения с конечной точкой именованного канала.По истечении заданного промежутка времени выполнена еще одна попытка.  
  
## Описание  
 Данная информационная трассировка показывает сбой соединения с конечной точкой именованного канала.Возможная причина: не найдена или занята конечная точка именованного канала.Через короткие промежутки времени производится несколько дополнительных попыток, пока одна из них не будет успешной, или до истечения времени, заданного свойством OpenTimeout.  
  
## См. также  
 [Трассировка](../../../../../docs/framework/wcf/diagnostics/tracing/index.md)   
 [Использование трассировки для устранения неполадок приложения](../../../../../docs/framework/wcf/diagnostics/tracing/using-tracing-to-troubleshoot-your-application.md)   
 [Администрирование и диагностика](../../../../../docs/framework/wcf/diagnostics/index.md)