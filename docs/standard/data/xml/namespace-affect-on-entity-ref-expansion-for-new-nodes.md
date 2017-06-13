---
title: "Влияние пространства имен на раскрытие ссылок на сущности для новых узлов, содержащих элементы и атрибуты | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-standard"
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
  - "C++"
  - "jsharp"
ms.assetid: 64359aee-aab0-4042-9a32-d19789af6ca7
caps.latest.revision: 3
author: "mairaw"
ms.author: "mairaw"
manager: "wpickett"
caps.handback.revision: 3
---
# Влияние пространства имен на раскрытие ссылок на сущности для новых узлов, содержащих элементы и атрибуты
Так как содержимое декларации сущности может содержать абсолютно все, существует вероятность, что содержимое может содержать элемент типа `<!ENTITY aname "<elem>test</elem>">`.  
  
 Во время анализа XML элемент `&aname;` не раскрывается своим замененным содержимым.  Раскрывание XML не выполняется, так как разрешение пространства имен для элемента не может произойти, пока узел не размещается в документе.  До этого времени неизвестно, какое пространство имен расположено в области.  Когда узел помещается в документ, происходит разрешение пространства имен и результирующее содержимое сущности анализируется внутри соответствующих узлов.  
  
> [!NOTE]
>  После того, как раскрывание произошло в заново созданном узле ссылки сущности, оно никогда повторно не происходит.  Поэтому пространства имен, используемые в тексте замены для элемента, привязываются во время задания родительского узла.  Однако, пространство имен может быть изменено для существующих узлов ссылки сущности, и они могут быть вставлены куда\-либо еще, или для узлов ссылки сущности, которые копируются с помощью метода **CloneNode**.  
  
## См. также  
 [Модель DOM для XML](../../../../docs/standard/data/xml/xml-document-object-model-dom.md)