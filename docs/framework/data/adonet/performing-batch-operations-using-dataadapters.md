---
title: "Выполнение пакетных операций с помощью объектов DataAdapter (ADO.NET) | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-ado"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: e72ed5af-b24f-486c-8429-c8fd2208f844
caps.latest.revision: 3
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
caps.handback.revision: 3
---
# Выполнение пакетных операций с помощью объектов DataAdapter (ADO.NET)
Поддержка пакетных операций в ADO.NET позволяет объекту <xref:System.Data.Common.DataAdapter> группировать операции INSERT, UPDATE и DELETE из <xref:System.Data.DataSet> или <xref:System.Data.DataTable> к серверу вместо отправки за один раз одной операции.  Уменьшение числа двусторонних передач сигнала на сервер обычно приводит к значительному повышению производительности.  Пакетные обновления поддерживаются для поставщиков .NET\-данных для SQL Server \(<xref:System.Data.SqlClient>\) и Oracle \(<xref:System.Data.OracleClient>\).  
  
 При обновлении базы данных изменениями из объекта <xref:System.Data.DataSet> в более ранних версиях ADO.NET метод `Update` для `DataAdapter` выполняет обновления в базе данных по одной строке.  Когда он просматривает строки в указанной таблице <xref:System.Data.DataTable>, он проверяет каждую строку <xref:System.Data.DataRow>, чтобы выявить, не была ли она изменена.  Если строка изменена, он вызывает соответствующую команду `UpdateCommand`, `InsertCommand` или `DeleteCommand` в зависимости от значения свойства <xref:System.Data.DataRow.RowState%2A> для этой строки.  Каждое обновление строки подразумевает сетевую двустороннюю передачу сигнала в базу данных.  
  
 Начиная с ADO.NET 2.0, объект <xref:System.Data.Common.DbDataAdapter> предоставляет свойство <xref:System.Data.Common.DbDataAdapter.UpdateBatchSize%2A>.  При установке для свойства `UpdateBatchSize` положительного целого значения обновления базы данных посылаются как пакеты указанного размера.  Например, при установке `UpdateBatchSize` в 10 производится группирование 10 отдельных инструкций и передача их как один пакет.  При установке `UpdateBatchSize` в 0 <xref:System.Data.Common.DataAdapter> использует самой большой размер пакета, который может обработать сервер.  При его установке в 1 отключаются пакетные обновления, т. к. строки посылаются по одной.  
  
 Выполнение очень больших пакетов может снизить производительность.  Поэтому необходимо экспериментальным путем найти параметр оптимального размера пакета перед реализацией приложения.  
  
## Использование свойства UpdateBatchSize  
 Если пакетные обновления включены, значение свойства <xref:System.Data.IDbCommand.UpdatedRowSource%2A> для `UpdateCommand`, `InsertCommand` и `DeleteCommand` объекта DataAdapter должно быть установлено в <xref:System.Data.UpdateRowSource> или <xref:System.Data.UpdateRowSource>.  При выполнении пакетного обновления значение свойства <xref:System.Data.IDbCommand.UpdatedRowSource%2A> команды для <xref:System.Data.UpdateRowSource> или <xref:System.Data.UpdateRowSource> недействительно.  
  
 Следующая процедура демонстрирует использование свойства `UpdateBatchSize`.  Процедура принимает два аргумента, объект <xref:System.Data.DataSet>, который имеет столбцы, представляющие поля **ProductCategoryID** и **Name** в таблице **Production.ProductCategory**, и целое число, представляющее размер пакета \(число строк в пакете\).  Код создает новый объект <xref:System.Data.SqlClient.SqlDataAdapter>, устанавливая его свойства <xref:System.Data.SqlClient.SqlDataAdapter.UpdateCommand%2A>, <xref:System.Data.SqlClient.SqlDataAdapter.InsertCommand%2A> и <xref:System.Data.SqlClient.SqlDataAdapter.DeleteCommand%2A>.  В коде предполагается, что объект <xref:System.Data.DataSet> имеет измененные строки.  В нем устанавливается свойство `UpdateBatchSize` и выполняется обновление.  
  
```vb  
Public Sub BatchUpdate( _  
  ByVal dataTable As DataTable, ByVal batchSize As Int32)  
    ' Assumes GetConnectionString() returns a valid connection string.  
    Dim connectionString As String = GetConnectionString()  
  
    ' Connect to the AdventureWorks database.  
    Using connection As New SqlConnection(connectionString)  
        ' Create a SqlDataAdapter.  
        Dim adapter As New SqlDataAdapter()  
  
        'Set the UPDATE command and parameters.  
        adapter.UpdateCommand = New SqlCommand( _  
          "UPDATE Production.ProductCategory SET " _  
          & "Name=@Name WHERE ProductCategoryID=@ProdCatID;", _  
          connection)  
        adapter.UpdateCommand.Parameters.Add("@Name", _  
          SqlDbType.NVarChar, 50, "Name")  
        adapter.UpdateCommand.Parameters.Add("@ProdCatID",  _  
          SqlDbType.Int, 4, " ProductCategoryID ")  
        adapter.UpdateCommand.UpdatedRowSource = _  
          UpdateRowSource.None  
  
        'Set the INSERT command and parameter.  
        adapter.InsertCommand = New SqlCommand( _  
          "INSERT INTO Production.ProductCategory (Name) VALUES (@Name);", _  
  connection)  
        adapter.InsertCommand.Parameters.Add("@Name", _  
          SqlDbType.NVarChar, 50, "Name")  
        adapter.InsertCommand.UpdatedRowSource = _  
          UpdateRowSource.None  
  
        'Set the DELETE command and parameter.  
        adapter.DeleteCommand = New SqlCommand( _  
          "DELETE FROM Production.ProductCategory " _  
          & "WHERE ProductCategoryID=@ProdCatID;", connection)  
        adapter.DeleteCommand.Parameters.Add("@ProdCatID", _  
           SqlDbType.Int, 4, " ProductCategoryID ")  
        adapter.DeleteCommand.UpdatedRowSource = UpdateRowSource.None  
  
        ' Set the batch size.  
        adapter.UpdateBatchSize = batchSize  
  
        ' Execute the update.  
        adapter.Update(dataTable)  
    End Using  
End Sub  
```  
  
```csharp  
public static void BatchUpdate(DataTable dataTable,Int32 batchSize)  
{  
    // Assumes GetConnectionString() returns a valid connection string.  
    string connectionString = GetConnectionString();  
  
    // Connect to the AdventureWorks database.  
    using (SqlConnection connection = new   
      SqlConnection(connectionString))  
    {  
  
        // Create a SqlDataAdapter.  
        SqlDataAdapter adapter = new SqlDataAdapter();  
  
        // Set the UPDATE command and parameters.  
        adapter.UpdateCommand = new SqlCommand(  
            "UPDATE Production.ProductCategory SET "  
            + "Name=@Name WHERE ProductCategoryID=@ProdCatID;",   
            connection);  
        adapter.UpdateCommand.Parameters.Add("@Name",   
           SqlDbType.NVarChar, 50, "Name");  
        adapter.UpdateCommand.Parameters.Add("@ProdCatID",   
           SqlDbType.Int, 4, "ProductCategoryID");  
         adapter.UpdateCommand.UpdatedRowSource = UpdateRowSource.None;  
  
        // Set the INSERT command and parameter.  
        adapter.InsertCommand = new SqlCommand(  
            "INSERT INTO Production.ProductCategory (Name) VALUES (@Name);",   
            connection);  
        adapter.InsertCommand.Parameters.Add("@Name",   
          SqlDbType.NVarChar, 50, "Name");  
        adapter.InsertCommand.UpdatedRowSource = UpdateRowSource.None;  
  
        // Set the DELETE command and parameter.  
        adapter.DeleteCommand = new SqlCommand(  
            "DELETE FROM Production.ProductCategory "  
            + "WHERE ProductCategoryID=@ProdCatID;", connection);  
        adapter.DeleteCommand.Parameters.Add("@ProdCatID",   
          SqlDbType.Int, 4, "ProductCategoryID");  
        adapter.DeleteCommand.UpdatedRowSource = UpdateRowSource.None;  
  
        // Set the batch size.  
        adapter.UpdateBatchSize = batchSize;  
  
        // Execute the update.  
        adapter.Update(dataTable);  
    }  
}  
```  
  
## Обработка событий и ошибок, связанных с обновлением пакета  
 Объект **DataAdapter** имеет два события, связанных с обновлением: **RowUpdating** и **RowUpdated**.  В более ранних версиях ADO.NET, если пакетная обработка отключена, каждое из этих событий формируется для каждой обрабатываемой строки.  **RowUpdating** формируется перед появлением обновления, а **RowUpdated** формируется после завершения обновления базы данных.  
  
### Изменение поведения событий при пакетных обновлениях  
 Если пакетное обновление включено, несколько строк обновляются одной операцией базы данных.  Поэтому для каждого пакета происходит только одно событие `RowUpdated`, в то время как событие `RowUpdating` происходит для каждой обрабатываемой строки.  Если пакетное обновление отключено, два события инициируются с чередованием один к одному, где одно событие `RowUpdating` и одно событие `RowUpdated` инициируется для строки, а затем одно событие `RowUpdating` и одно событие `RowUpdated` инициируются для следующей строки, до тех пор пока не будут обработаны все строки.  
  
### Доступ к обновленным строкам  
 Если пакетная обработка отключена, доступ к обновляемой строке может быть выполнен при помощи свойства <xref:System.Data.Common.RowUpdatedEventArgs.Row%2A> класса <xref:System.Data.Common.RowUpdatedEventArgs>.  
  
 Если пакетная обработка включена для нескольких строк, формируется одно событие `RowUpdated`.  Поэтому значение свойства `Row` для каждой из строк равно NULL.  События `RowUpdating` по\-прежнему вызываются для каждой строки.  Метод <xref:System.Data.Common.RowUpdatedEventArgs.CopyToRows%2A> класса <xref:System.Data.Common.RowUpdatedEventArgs> позволяет обращаться к обработанным строкам, копируя ссылки на строки в массив.  Если строки не обрабатываются, метод `CopyToRows` инициирует исключение <xref:System.ArgumentNullException>.  Свойство <xref:System.Data.Common.RowUpdatedEventArgs.RowCount%2A> используется для возврата числа строк, обработанных перед вызовом метода <xref:System.Data.Common.RowUpdatedEventArgs.CopyToRows%2A>.  
  
### Обработка ошибок данных  
 Пакетное выполнение оказывает то же влияние, что и выполнение каждой отдельной инструкции.  Инструкции выполняются в порядке, который инструкции добавили в пакет.  Ошибки обрабатываются в пакетном режиме, как если было бы при отключении пакетного режима.  Каждая строка обрабатывается отдельно.  Только успешно обработанные в базе данных строки будут обновлены в соответствующей строке <xref:System.Data.DataRow> таблицы <xref:System.Data.DataTable>.  
  
 Поставщик данных и сервер базы данных определяют, какие конструкции SQL поддерживаются для пакетного обновления.  Если неподдерживаемая инструкция подается на выполнение, создается исключение.  
  
## См. также  
 [Объекты DataAdapter и DataReader](../../../../docs/framework/data/adonet/dataadapters-and-datareaders.md)   
 [Обновление источников данных с помощью объектов DataAdapter](../../../../docs/framework/data/adonet/updating-data-sources-with-dataadapters.md)   
 [Обработка событий DataAdapter](../../../../docs/framework/data/adonet/handling-dataadapter-events.md)   
 [Центр разработчиков, поставщики ADO.NET Managed Provider и набор данных](http://go.microsoft.com/fwlink/?LinkId=217917)