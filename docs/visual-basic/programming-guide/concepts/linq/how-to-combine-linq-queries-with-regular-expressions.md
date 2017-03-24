---
title: "Практическое руководство: объединение запросов LINQ с регулярными выражениями (Visual Basic) | Документы Microsoft"
ms.custom: 
ms.date: 2015-07-20
ms.prod: .net
ms.reviewer: 
ms.suite: 
ms.technology:
- devlang-visual-basic
ms.tgt_pltfrm: 
ms.topic: article
dev_langs:
- VB
ms.assetid: 3da1bd10-b0d8-4d5b-a637-966891c13592
caps.latest.revision: 3
author: stevehoag
ms.author: shoag
translation.priority.mt:
- cs-cz
- pl-pl
- pt-br
- tr-tr
translationtype: Machine Translation
ms.sourcegitcommit: a06bd2a17f1d6c7308fa6337c866c1ca2e7281c0
ms.openlocfilehash: 283b5e844c91da22aadd7bcf88ea327ccc080be7
ms.lasthandoff: 03/13/2017

---
# <a name="how-to-combine-linq-queries-with-regular-expressions-visual-basic"></a>Практическое руководство: объединение запросов LINQ с регулярными выражениями (Visual Basic)
В этом примере показано, как использовать <xref:System.Text.RegularExpressions.Regex>класса, чтобы создать регулярное выражение для более сложных соответствий в текстовых строках.</xref:System.Text.RegularExpressions.Regex> Запрос LINQ упрощает фильтрацию именно файлов, которые требуется поиск с помощью регулярных выражений и формирование результатов.  
  
## <a name="example"></a>Пример  
  
```vb  
Class LinqRegExVB  
  
    Shared Sub Main()  
  
        ' Root folder to query, along with all subfolders.  
        ' Modify this path as necessary so that it accesses your Visual Studio folder.  
        Dim startFolder As String = "C:\program files\Microsoft Visual Studio 9.0\"  
        ' One of the following paths may be more appropriate on your computer.  
        'string startFolder = @"c:\program files (x86)\Microsoft Visual Studio 9.0\";  
        'string startFolder = @"c:\program files\Microsoft Visual Studio 10.0\";  
        'string startFolder = @"c:\program files (x86)\Microsoft Visual Studio 10.0\";  
  
        ' Take a snapshot of the file system.  
        Dim fileList As IEnumerable(Of System.IO.FileInfo) = GetFiles(startFolder)  
  
        ' Create a regular expression to find all things "Visual".  
        Dim searchTerm As System.Text.RegularExpressions.Regex =   
            New System.Text.RegularExpressions.Regex("Visual (Basic|C#|C\+\+|J#|SourceSafe|Studio)")  
  
        ' Search the contents of each .htm file.  
        ' Remove the where clause to find even more matches!  
        ' This query produces a list of files where a match  
        ' was found, and a list of the matches in that file.  
        ' Note: Explicit typing of "Match" in select clause.  
        ' This is required because MatchCollection is not a   
        ' generic IEnumerable collection.  
        Dim queryMatchingFiles = From afile In fileList  
                                Where afile.Extension = ".htm"  
                                Let fileText = System.IO.File.ReadAllText(afile.FullName)  
                                Let matches = searchTerm.Matches(fileText)  
                                Where (matches.Count > 0)  
                                Select Name = afile.FullName,  
                                       Matches = From match As System.Text.RegularExpressions.Match In matches  
                                                 Select match.Value  
  
        ' Execute the query.  
        Console.WriteLine("The term " & searchTerm.ToString() & " was found in:")  
  
        For Each fileMatches In queryMatchingFiles  
            ' Trim the path a bit, then write   
            ' the file name in which a match was found.  
            Dim s = fileMatches.Name.Substring(startFolder.Length - 1)  
            Console.WriteLine(s)  
  
            ' For this file, write out all the matching strings  
            For Each match In fileMatches.Matches  
                Console.WriteLine("  " + match)  
            Next  
        Next  
  
        ' Keep the console window open in debug mode  
        Console.WriteLine("Press any key to exit")  
        Console.ReadKey()  
    End Sub  
  
    ' Function to retrieve a list of files. Note that this is a copy  
    ' of the file information.  
    Shared Function GetFiles(ByVal root As String) As IEnumerable(Of System.IO.FileInfo)  
        Return From file In My.Computer.FileSystem.GetFiles(  
                   root, FileIO.SearchOption.SearchAllSubDirectories, "*.*")   
               Select New System.IO.FileInfo(file)  
    End Function  
  
End Class  
```  
  
 Обратите внимание, что также можно запрашивать <xref:System.Text.RegularExpressions.MatchCollection>объект, возвращаемый `RegEx` поиска.</xref:System.Text.RegularExpressions.MatchCollection> В этом примере в результатах создается только значения каждого соответствия. Однако можно также использовать LINQ для выполнения всех видов фильтрации, сортировки и группировки в этой коллекции. Поскольку <xref:System.Text.RegularExpressions.MatchCollection>является неуниверсального <xref:System.Collections.IEnumerable>коллекции, необходимо явно указывать тип переменной диапазона в запросе.</xref:System.Collections.IEnumerable> </xref:System.Text.RegularExpressions.MatchCollection>  
  
## <a name="compiling-the-code"></a>Компиляция кода  
 Создайте проект, в платформе .NET Framework версии 3.5 или более поздней версии с ссылку на библиотеку System.Core.dll и `Imports` оператор для пространства имен System.Linq.  
  
## <a name="see-also"></a>См. также  
 [LINQ и строки (Visual Basic)](../../../../visual-basic/programming-guide/concepts/linq/linq-and-strings.md)   
 [LINQ и каталоги файлов (Visual Basic)](../../../../visual-basic/programming-guide/concepts/linq/linq-and-file-directories.md)