---
title: "Преимущества универсальных шаблонов (Руководство по программированию на C#)"
ms.date: 2015-07-20
ms.prod: .net
ms.technology:
- devlang-csharp
ms.topic: article
dev_langs:
- CSharp
helpviewer_keywords:
- generics [C#], benefits
ms.assetid: 80f037cd-9ea7-48be-bfc1-219bfb2d4277
caps.latest.revision: 23
author: BillWagner
ms.author: wiwagn
translation.priority.ht:
- cs-cz
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pl-pl
- pt-br
- ru-ru
- tr-tr
- zh-cn
- zh-tw
ms.translationtype: HT
ms.sourcegitcommit: 306c608dc7f97594ef6f72ae0f5aaba596c936e1
ms.openlocfilehash: 8b05a3a695064764618564293e6ccd92e2ce60be
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---
# <a name="benefits-of-generics-c-programming-guide"></a>Преимущества универсальных шаблонов (Руководство по программированию на C#)
Универсальные шаблоны позволяют обойти ограничение, существовавшее в более ранних версиях общеязыковой среды выполнения (CLR) и языка C#, из-за которого обобщение реализовывалось путем приведения типов к универсальному базовому типу <xref:System.Object> или из него. Создавая универсальный класс, вы можете создать коллекцию, которая будет типобезопасной во время компиляции.  
  
 Ограничение на использование классов коллекции, не являющихся универсальными, можно продемонстрировать на примере небольшой программы, в которой используется класс коллекции <xref:System.Collections.ArrayList> из библиотеки платформы .NET Framework. <xref:System.Collections.ArrayList> — это очень удобный класс коллекции, который можно использовать без изменений для хранения любых типов значений и ссылочных типов.  
  
 [!code-cs[csProgGuideGenerics#4](../../../csharp/programming-guide/generics/codesnippet/CSharp/benefits-of-generics_1.cs)]  
  
 Тем не менее его применение имеет свои недостатки. Любые типы значений или ссылочные типы, которые добавляются в <xref:System.Collections.ArrayList>, неявно приводятся с повышением к типу <xref:System.Object>. Если элементы представляют собой типы значений, их необходимо упаковывать при добавлении в список и распаковывать при извлечении из него. Выполнение операций приведения, упаковки-преобразования и распаковки-преобразования отрицательно сказывается на производительности, причем последние две операции при работе с большими коллекциями могут приводить к ее заметному снижению.  
  
 Еще одно ограничение связано с отсутствием проверки типов во время компиляции. Поскольку <xref:System.Collections.ArrayList> приводит все элементы к типу <xref:System.Object>, во время компиляции невозможно предотвратить выполнение действий, схожих с приведенными ниже, из клиентского кода:  
  
 [!code-cs[csProgGuideGenerics#5](../../../csharp/programming-guide/generics/codesnippet/CSharp/benefits-of-generics_2.cs)]  
  
 Несмотря на то, что объединение строк `ints` в единый объект <xref:System.Collections.ArrayList> полностью приемлемо, а при создании разнородной коллекции нередко выполняется намеренно, в большинстве случаев это повлечет за собой ошибку программирования, которая может быть выявлена только во время выполнения.  
  
 В версиях 1.0 и 1.1 языка C# исключить опасность, связанную с использованием обобщенного кода классов коллекций из библиотеки базовых классов платформы .NET Framework, можно было только путем написания собственных коллекций нужного типа. Естественно, из-за невозможности использовать такой класс для работы с несколькими типами данных утрачивались преимущества обобщения, поскольку в этом случае требовалось создавать отдельный класс для каждого хранящегося типа.  
  
 Для <xref:System.Collections.ArrayList> и схожих с ним классов было необходимо, чтобы в клиентском коде для каждого экземпляра задавался конкретный тип данных, который будет использоваться. Это помогло бы исключить необходимость в приведении с повышением к типу `T:System.Object` и позволило бы обеспечить проверку типов компилятором. Другими словами, для <xref:System.Collections.ArrayList> требуется параметр типа. Именно с этой целью были реализованы универсальные шаблоны. В универсальной коллекции <xref:System.Collections.Generic.List%601> в пространстве имен `N:System.Collections.Generic` та же операция добавления элементов в коллекцию выглядит следующим образом:  
  
 [!code-cs[csProgGuideGenerics#6](../../../csharp/programming-guide/generics/codesnippet/CSharp/benefits-of-generics_3.cs)]  
  
 В клиентском коде в <xref:System.Collections.Generic.List%601> по сравнению с <xref:System.Collections.ArrayList> добавлен только аргумент типа для объявления и создания экземпляра. После этого незначительного усложнения кода стало возможным создание списка, который не только безопаснее <xref:System.Collections.ArrayList>, но и обрабатывается значительно быстрее его, особенно если его элементы представляют собой типы значений.  
  
## <a name="see-also"></a>См. также  
 <xref:System.Collections.Generic>   
 [Руководство по программированию на C#](../../../csharp/programming-guide/index.md)   
 [Введение в универсальные шаблоны](../../../csharp/programming-guide/generics/introduction-to-generics.md)   
 [Упаковка-преобразование и распаковка-преобразование](../../../csharp/programming-guide/types/boxing-and-unboxing.md)   
 [Рекомендации по работе с коллекциями](http://go.microsoft.com/fwlink/?LinkId=112403)

