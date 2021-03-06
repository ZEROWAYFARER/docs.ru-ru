---
title: "Образец технологии сериализации, независимой от версии"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net
ms.reviewer: 
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 2a183664-bfbf-4ff0-96f6-c836284ea916
caps.latest.revision: 6
author: Erikre
ms.author: erikre
manager: erikre
ms.translationtype: HT
ms.sourcegitcommit: 717bcb6f9f72a728d77e2847096ea558a9c50902
ms.openlocfilehash: 43057a4b0014ac2ea8aec6f298ccc0b2d9103154
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="version-tolerant-serialization-technology-sample"></a>Образец технологии сериализации, независимой от версии
[Скачать образец](http://download.microsoft.com/download/4/7/B/47B2164C-E780-4B10-8DE4-2CB5B886E0A6/Technologies/Serialization/Runtime%20Serialization/VTS.zip.exe)  
  
 В этом образце демонстрируются возможности .NET-сериализации, независимой от версии. В примере выполняется построение приложений, использующих различные версии объекта <xref:System.Runtime.Serialization.Formatters.Binary.BinaryFormatter> для сериализации и десериализации данных. Несмотря на наличие различных типов версий, приложения легко взаимодействуют друг с другом. Дополнительные сведения см. в разделе [Независимая от версий сериализация](../../../docs/standard/serialization/version-tolerant-serialization.md).  
  
### <a name="to-build-the-sample-using-the-command-prompt"></a>Сборка образца с использованием командной строки  
  
1.  Откройте окно командной строки и перейдите к вложенной папке (в приложении V1 или V2) для данного образца, соответствующей выбранному языку.  
  
2.  Введите **msbuild.exe \<ver> application.sln** в командной строке (где \<ver> равно v1 или v2).  
  
### <a name="to-build-the-sample-using-visual-studio"></a>Сборка образца с использованием Visual Studio  
  
1.  Откройте [!INCLUDE[fileExplorer](../../../includes/fileexplorer-md.md)] и перейдите к вложенной папке для данного образца, соответствующей выбранному языку.  
  
2.  Перейдите во вложенную папку приложения V1 из выбранного на прошлом шаге каталога.  
  
3.  Дважды щелкните изображение V1 Application.sln, чтобы открыть файл в Visual Studio.  
  
4.  В меню **Сборка** выберите **Собрать решение**.  
  
5.  Перейдите во вложенную папку приложения V2 и повторите два предыдущих шага для построения приложения V2.  
  
 По умолчанию построение приложений помещается во вложенные папки \bin или \bin\Debug каталогов соответствующего проекта.  
  
### <a name="to-run-the-sample"></a>Выполнение образца  
  
1.  В окне командной строки перейдите к одной из вложенных языковых папок, выбранных во время построения приложения.  
  
2.  Введите в командной строке **runme.cmd** для одновременного выполнения обоих приложений.  
  
 Также можно перейти в каталоги, содержащие новые исполняемые файлы и последовательно выполнить их.  
  
> [!NOTE]
>  В образце производится построение консольных приложений. Чтобы просмотреть выводимые ими данные, необходимо загрузить и выполнить их в окне командной строки.  
  
## <a name="see-also"></a>См. также  
 <xref:System.Runtime.Serialization.Formatters.Binary.BinaryFormatter>   
 <xref:System.IO.FileStream>

