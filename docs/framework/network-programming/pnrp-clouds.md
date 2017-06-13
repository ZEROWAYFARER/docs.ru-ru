---
title: "Облака PNRP | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
  - "C++"
  - "jsharp"
ms.assetid: a82e2bf1-62ab-4c2d-83f3-3217a6aead2e
caps.latest.revision: 4
author: "mcleblanc"
ms.author: "markl"
manager: "markl"
caps.handback.revision: 4
---
# Облака PNRP
Протокол PNRP "облако" представляет набор узлов, которые могут взаимодействовать друг с другом через сеть.  Термин "облако" синонимна с "сетке одноранговых узлов" и "одноранговый узел диаграммой".  
  
 Данные, которыми обмениваются узлы, никогда не должны переходить из одного облака в другое.  Экземпляр класса <xref:System.Net.PeerToPeer.Cloud> уникально определяется по имени, являющемуся чувствительным к регистру.  Один одноранговый узел может быть соединен с несколькими облаками.  
  
 Облака очень тесно связаны с сетевыми интерфейсами.  На многосетевом компьютере с двумя сетевыми адаптерами, назначенными разным подсетям, будет возвращено три облака: одно для каждого локального для канала адреса на интерфейс, и одно облако глобальной области.  
  
 Использования 3 протокола PNRP заволакивают "область", в которых область группирования компьютеров, которые может найти друг друга.  
  
-   Глобальное облака соответствует глобальную область адреса IP версии 6 и глобальным адресам и представляет все компьютеры все IP версии 6 Интернете.  Только одно глобальное облака.  
  
-   Связь\- локальное облака соответствует связь\- локальной области адреса IP версии 6 и связь\- локальным адресам.  Связь\- локальное облака для определенной связи, которые обычно совпадает с локально вложенная подсеть.  Облако могут быть кратно связь\- локальные.  
  
 Третий облаков, облака сайт\-, соответствующий определенной области адреса IP версии 6 сайта и сайт\- локальным адресам.  Это облака нерекомендуемо, хотя она по\-прежнему поддерживается в протоколе PNRP.  
  
## Облако  
 Облака PNRP представлены экземплярами класса <xref:System.Net.PeerToPeer.Cloud>.  Группы в составе облаков, используемые одноранговым представлены экземплярами перечислимого класса <xref:System.Net.PeerToPeer.CloudCollection>.  Коллекции облака PNRP известных к текущему узлу могут быть получены путем вызова статический метод <xref:System.Net.PeerToPeer.Cloud.GetAvailableClouds%2A>.  
  
 Отдельные облака имеют уникальные имена, представленные в виде строки в юникоде 256 символов.  Эти имена вместе с вышеупомянутый областью, используемые для создания уникальных экземпляров класса облака.  Эти экземпляры могут быть сериализованы и восстановления для постоянного потребления.  
  
 После создания экземпляра облака или получен, имена однорангового узла может быть зарегистрирован с ним, чтобы создать известных сетку одноранговых узлов.  
  
## См. также  
 <xref:System.Net.PeerToPeer.Cloud>   
 [Протокол PNRP](../../../docs/framework/network-programming/peer-name-resolution-protocol.md)