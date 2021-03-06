---
title: "Link Demands | Microsoft Docs"
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
  - "C++"
  - "jsharp"
helpviewer_keywords: 
  - "security [.NET Framework], demands"
  - "demanded permissions"
  - "permissions [.NET Framework], demands"
  - "granting permissions, demands"
  - "code access security, demands"
  - "user demands for permission"
  - "caller security checks"
  - "link demands"
ms.assetid: a33fd5f9-2de9-4653-a4f0-d9df25082c4d
caps.latest.revision: 18
author: "mairaw"
ms.author: "mairaw"
manager: "wpickett"
caps.handback.revision: 16
---
# Link Demands
Требование связывания вызывает проверку безопасности во время JIT\-компиляции и выполняет проверку только непосредственно вызывающую сборку кода.  Связывание происходит, когда ваш код привязывается к ссылке на тип, включая ссылки на указатели функций и вызовы методов.  Если вызывающая сборка не имеет достаточных разрешений для связывания с кодом, связывание не разрешается, а при загрузке и запуске кода создается исключение времени выполнения.  Требования связывания могут переопределяться в классах, наследующих от кода.  
  
 Обратите внимание, что полный обход стека не выполняется для этого типа требования, а код все равно подвержен атакам с заманиванием.  Например, если метод в сборке А защищен требованием связывания, непосредственный вызывающий объект в сборке Б оценивается на основе разрешений сборки Б.  Однако требование связывания не будет оценивать метод в сборке В, если он косвенно вызывает метод в сборке А, используя метод в сборке Б.  Требование связывания определяет только те разрешения, которые должны иметь непосредственные вызывающие объекты в непосредственно вызывающей сборке для связывания с кодом.  Оно не определяет, какие разрешения должны иметь все вызывающие объекты для запуска кода.  
  
 Модификаторы обхода стека <xref:System.Security.CodeAccessPermission.Assert%2A>, <xref:System.Security.CodeAccessPermission.Deny%2A> и <xref:System.Security.CodeAccessPermission.PermitOnly%2A> не влияют на оценку требований связывания.  Поскольку требования связывания не выполняют обход стека, модификаторы обхода стека не оказывают влияния на требования связывания.  
  
 В случае обращения к методу, защищенному требованием связывания, при помощи отражения [Reflection](../../../docs/framework/reflection-and-codedom/reflection.md) требование связывания проверяет непосредственный вызывающий объект кода, к которому было совершено обращение через отражение.  Это справедливо как для обнаружения метода, так и для вызова метода, выполненного при помощи отражения.  Например, предположим, что код использует отражение для возврата объекта <xref:System.Reflection.MethodInfo>, представляющего метод, защищенный требованием связывания, а затем передает объект **MethodInfo** в другой код, который использует этот объект для вызова исходного метода.  В этом случае проверка требования связывания происходит дважды: один раз для кода, возвращающего объект **MethodInfo**, и один раз для кода, вызывающего его.  
  
> [!NOTE]
>  Требование связывания, выполняемое в статическом конструкторе класса, не защищает конструктор, так как статические конструкторы вызываются системой за пределами пути выполнения кода приложения.  В результате, когда требование связывания применяется ко всему классу, оно не может защитить доступ к статическому конструктору, хотя защищает остальную часть класса.  
  
 В следующем фрагменте кода декларативно указывается, что любой код, связываемый с методом `ReadData`, должен иметь разрешение `CustomPermission`.  Это гипотетическое разрешение, которого не существует в .NET Framework.  Требование осуществляется посредством передачи флага **SecurityAction.LinkDemand** в `CustomPermissionAttribute`.  
  
```vb  
<CustomPermissionAttribute(SecurityAction.LinkDemand)> _  
Public Shared Function ReadData() As String  
    ' Access a custom resource.  
End Function    
```  
  
```csharp  
[CustomPermissionAttribute(SecurityAction.LinkDemand)]  
public static string ReadData()  
{  
    // Access a custom resource.  
}  
```  
  
## См. также  
 [Атрибуты](../../../docs/standard/attributes/index.md)   
 [Code Access Security](../../../docs/framework/misc/code-access-security.md)