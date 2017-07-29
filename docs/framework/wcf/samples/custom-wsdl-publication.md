---
title: "Пользовательская публикация WSDL | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 3b3e8103-2c95-4db3-a05b-46aa8e9d4d29
caps.latest.revision: 21
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 21
---
# Пользовательская публикация WSDL
В данном образце демонстрируются указанные ниже возможности.  
  
-   Реализация <xref:System.ServiceModel.Description.IWsdlExportExtension?displayProperty=fullName> пользовательского атрибута <xref:System.ServiceModel.Description.IContractBehavior?displayProperty=fullName> для экспорта свойств атрибута в виде заметок WSDL.  
  
-   Реализация <xref:System.ServiceModel.Description.IWsdlImportExtension?displayProperty=fullName> для импорта пользовательских заметок WSDL.  
  
-   Реализация <xref:System.ServiceModel.Description.IServiceContractGenerationExtension?displayProperty=fullName> и <xref:System.ServiceModel.Description.IOperationContractGenerationExtension?displayProperty=fullName> в пользовательском поведении контракта и пользовательском поведении операции соответственно для записи импортированных заметок в виде комментариев в CodeDom для импортированных контракта и операции.  
  
-   Использование <xref:System.ServiceModel.Description.MetadataExchangeClient?displayProperty=fullName> для загрузки WSDL, <xref:System.ServiceModel.Description.WsdlImporter?displayProperty=fullName> для импорта WSDL с помощью пользовательского импортера WSDL и <xref:System.ServiceModel.Description.ServiceContractGenerator?displayProperty=fullName> для создания кода клиента [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] с заметками WSDL в виде комментариев \/\/\/ и ''' в C\# и Visual Basic.  
  
> [!NOTE]
>  Процедура настройки и инструкции по построению для данного образца приведены в конце этого раздела.  
  
## Служба  
 В этом образце служба помечена двумя пользовательскими атрибутами.Первый атрибут, `WsdlDocumentationAttribute`, принимает строку в конструкторе и может применяться для предоставления интерфейса контракта или операции со строкой, описывающей их использование.Второй атрибут, `WsdlParamOrReturnDocumentationAttribute`, может применяться для возврата значений или параметров, описывающих эти значения в операции.В следующем примере показан контракт службы `ICalculator`, описанный с помощью этих атрибутов.  
  
```  
// Define a service contract.      
[ServiceContract(Namespace="http://Microsoft.ServiceModel.Samples")]  
// Document it.  
[WsdlDocumentation("The ICalculator contract performs basic calculation services.")]  
public interface ICalculator  
{  
    [OperationContract]  
    [WsdlDocumentation("The Add operation adds two numbers and returns the result.")]  
    [return:WsdlParamOrReturnDocumentation("The result of adding the two arguments together.")]  
    double Add(  
      [WsdlParamOrReturnDocumentation("The first value to add.")]double n1,   
      [WsdlParamOrReturnDocumentation("The second value to add.")]double n2  
    );  
  
    [OperationContract]  
    [WsdlDocumentation("The Subtract operation subtracts the second argument from the first.")]  
    [return:WsdlParamOrReturnDocumentation("The result of the second argument subtracted from the first.")]  
    double Subtract(  
      [WsdlParamOrReturnDocumentation("The value from which the second is subtracted.")]double n1,   
      [WsdlParamOrReturnDocumentation("The value that is subtracted from the first.")]double n2  
    );  
  
    [OperationContract]  
    [WsdlDocumentation("The Multiply operation multiplies two values.")]  
    [return:WsdlParamOrReturnDocumentation("The result of multiplying the first and second arguments.")]  
    double Multiply(  
      [WsdlParamOrReturnDocumentation("The first value to multiply.")]double n1,   
      [WsdlParamOrReturnDocumentation("The second value to multiply.")]double n2  
    );  
  
    [OperationContract]  
    [WsdlDocumentation("The Divide operation returns the value of the first argument divided by the second argument.")]  
    [return:WsdlParamOrReturnDocumentation("The result of dividing the first argument by the second.")]  
    double Divide(  
      [WsdlParamOrReturnDocumentation("The numerator.")]double n1,   
      [WsdlParamOrReturnDocumentation("The denominator.")]double n2  
    );  
}  
  
```  
  
 Атрибут `WsdlDocumentationAttribute` реализует интерфейсы <xref:System.ServiceModel.Description.IContractBehavior> и <xref:System.ServiceModel.Description.IOperationBehavior>, поэтому экземпляры атрибута при открытии службы добавляются к соответствующему описанию <xref:System.ServiceModel.Description.ContractDescription> или <xref:System.ServiceModel.Description.OperationDescription>.Кроме того, атрибут реализует интерфейс <xref:System.ServiceModel.Description.IWsdlExportExtension>.При вызове метода <xref:System.ServiceModel.Description.IWsdlExportExtension.ExportContract%28System.ServiceModel.Description.WsdlExporter%2CSystem.ServiceModel.Description.WsdlContractConversionContext%29> объект <xref:System.ServiceModel.Description.WsdlExporter>, который служит для экспорта метаданных, и объект <xref:System.ServiceModel.Description.WsdlContractConversionContext>, который содержит объекты описания службы, передаются в качестве параметров, что позволяет изменить экспортированные метаданные.  
  
 В этом образце в зависимости от того, какое описание \(<xref:System.ServiceModel.Description.ContractDescription> или <xref:System.ServiceModel.Description.OperationDescription>\) имеется у объекта контекста экспорта, комментарий извлекается из атрибута с помощью текстового свойства и добавляется в элемент заметки WSDL, как показано в следующем образце кода.  
  
```  
public void ExportContract(WsdlExporter exporter, WsdlContractConversionContext context)  
{  
    if (contractDescription != null)  
    {  
        // Inside this block it is the contract-level comment attribute.  
        // This.Text returns the string for the contract attribute.  
        // Set the doc element; if this isn't done first, there is no XmlElement in the   
        // DocumentElement property.  
        context.WsdlPortType.Documentation = string.Empty;  
        // Contract comments.  
        XmlDocument owner = context.WsdlPortType.DocumentationElement.OwnerDocument;  
        XmlElement summaryElement = owner.CreateElement("summary");  
        summaryElement.InnerText = this.Text;  
        context.WsdlPortType.DocumentationElement.AppendChild(summaryElement);  
    }  
    else  
    {  
        Operation operation = context.GetOperation(operationDescription);  
        if (operation != null)  
        {  
            // We are dealing strictly with the operation here.  
            // This.Text returns the string for the operation-level attributes.  
            // Set the doc element; if this isn't done first, there is no XmlElement in the   
            // DocumentElement property.  
            operation.Documentation = String.Empty;  
  
            // Operation C# triple comments.  
            XmlDocument owner = operation.DocumentationElement.OwnerDocument;  
            XmlElement newSummaryElement = owner.CreateElement("summary");  
            newSummaryElement.InnerText = this.Text;  
            operation.DocumentationElement.AppendChild(newSummaryElement);  
```  
  
 Если экспортируется операция, в образце используется отражение, чтобы получить все значения `WsdlParamOrReturnDocumentationAttribute` параметров и возвращаемые значения, после чего эти значения добавляются в элементы заметки WSDL этой операции, как показано ниже.  
  
```  
// Get returns information  
ParameterInfo returnValue = operationDescription.SyncMethod.ReturnParameter;  
object[] returnAttrs = returnValue.GetCustomAttributes(typeof(WsdlParamOrReturnDocumentationAttribute), false);  
if (returnAttrs.Length != 0)  
{  
    // <returns>text.</returns>  
    XmlElement returnsElement = owner.CreateElement("returns");  
    returnsElement.InnerText = ((WsdlParamOrReturnDocumentationAttribute)returnAttrs[0]).ParamComment;  
    operation.DocumentationElement.AppendChild(returnsElement);  
}  
  
// Get parameter information.  
ParameterInfo[] args = operationDescription.SyncMethod.GetParameters();  
for (int i = 0; i < args.Length; i++)  
{  
    object[] docAttrs = args[i].GetCustomAttributes(typeof(WsdlParamOrReturnDocumentationAttribute), false);  
    if (docAttrs.Length == 1)  
    {  
        // <param name="Int1">Text.</param>  
        XmlElement newParamElement = owner.CreateElement("param");  
        XmlAttribute paramName = owner.CreateAttribute("name");  
        paramName.Value = args[i].Name;  
        newParamElement.InnerText = ((WsdlParamOrReturnDocumentationAttribute)docAttrs[0]).ParamComment;  
        newParamElement.Attributes.Append(paramName);  
        operation.DocumentationElement.AppendChild(newParamElement);  
    }  
}  
  
```  
  
 После этого образец обычным образом публикует метаданные с использованием следующего файла конфигурации.  
  
```  
<services>  
  <service   
      name="Microsoft.ServiceModel.Samples.CalculatorService"  
      behaviorConfiguration="CalculatorServiceBehavior">  
    <!-- ICalculator is exposed at the base address provided by host: http://localhost/servicemodelsamples/service.svc  -->  
    <endpoint address=""  
              binding="wsHttpBinding"  
              contract="Microsoft.ServiceModel.Samples.ICalculator" />  
    <!-- the mex endpoint is exposed at http://localhost/servicemodelsamples/service.svc/mex -->  
    <endpoint address="mex"  
              binding="mexHttpBinding"  
              contract="IMetadataExchange" />  
  </service>  
</services>  
  
<!--For debugging purposes set the includeExceptionDetailInFaults attribute to true-->  
<behaviors>  
  <serviceBehaviors>  
    <behavior name="CalculatorServiceBehavior">  
      <serviceMetadata httpGetEnabled="True"/>  
      <serviceDebug includeExceptionDetailInFaults="False" />  
    </behavior>  
  </serviceBehaviors>  
</behaviors>  
  
```  
  
## Клиент Svcutil  
 В этом образце средство Svcutil.exe не используется.Контракт предоставляется в файле generatedClient.cs, поэтому службу можно вызывать после того, как образец продемонстрирует пользовательский импорт WSDL и создание кода.Чтобы использовать в этом примере приведенный ниже пользовательский импортер WSDL, можно запустить средство Svcutil.exe с параметром `/svcutilConfig`, задав путь к используемому в этом примере файлу конфигурации клиента, в котором содержится ссылка на библиотеку `WsdlDocumentation.dll`.Но для загрузки `WsdlDocumentationImporter` средство Svuctil.exe должно иметь возможность находить и загружать библиотеку `WsdlDocumentation.dll`, что означает, что она либо должна быть зарегистрирована в глобальном кэше сборок, либо располагаться в одном каталоге с файлом Svcutil.exe.Для простых образцах, таких как этот, удобнее всего скопировать программу Svcutil.exe и файл конфигурации в один каталог с файлом `WsdlDocumentation.dll` и запускать ее оттуда.  
  
## Пользовательский импортер WSDL  
 Пользовательский импортер `WsdlDocumentationImporter` объекта <xref:System.ServiceModel.Description.IWsdlImportExtension> также реализует интерфейсы <xref:System.ServiceModel.Description.IContractBehavior> и <xref:System.ServiceModel.Description.IOperationBehavior>, добавляемые в импортированные конечные точки службы, и интерфейсы<xref:System.ServiceModel.Description.IServiceContractGenerationExtension> и <xref:System.ServiceModel.Description.IOperationContractGenerationExtension>, вызываемые для изменения создания кода контракта или операции.  
  
 Сначала в методе <xref:System.ServiceModel.Description.IWsdlImportExtension.ImportContract%28System.ServiceModel.Description.WsdlImporter%2CSystem.ServiceModel.Description.WsdlContractConversionContext%29> образец определяет, относится ли заметка WSDL к уровню контракта или службы, после чего он добавляет себя в качестве поведения в соответствующую область, передавая импортированный текст заметки в конструктор.  
  
```  
public void ImportContract(WsdlImporter importer, WsdlContractConversionContext context)  
{  
    // Contract Documentation  
    if (context.WsdlPortType.Documentation != null)  
    {  
        // System examines the contract behaviors to see whether any implement IWsdlImportExtension.  
        context.Contract.Behaviors.Add(new WsdlDocumentationImporter(context.WsdlPortType.Documentation));  
    }  
    // Operation Documentation  
    foreach (Operation operation in context.WsdlPortType.Operations)  
    {  
        if (operation.Documentation != null)  
        {  
            OperationDescription operationDescription = context.Contract.Operations.Find(operation.Name);  
            if (operationDescription != null)  
            {  
                // System examines the operation behaviors to see whether any implement IWsdlImportExtension.  
                operationDescription.Behaviors.Add(new WsdlDocumentationImporter(operation.Documentation));  
            }  
        }  
    }  
}  
  
```  
  
 Затем, при создании кода, система вызывает методы <xref:System.ServiceModel.Description.IServiceContractGenerationExtension.GenerateContract%28System.ServiceModel.Description.ServiceContractGenerationContext%29> и <xref:System.ServiceModel.Description.IOperationContractGenerationExtension.GenerateOperation%28System.ServiceModel.Description.OperationContractGenerationContext%29>, передавая им сведения о соответствующем контексте.Образец форматирует пользовательские заметки WSDL и вставляет их в CodeDom в виде комментариев.  
  
```  
public void GenerateContract(ServiceContractGenerationContext context)  
{  
    Debug.WriteLine("In generate contract.");  
    context.ContractType.Comments.AddRange(FormatComments(text));  
}  
  
public void GenerateOperation(OperationContractGenerationContext context)  
{  
    context.SyncMethod.Comments.AddRange(FormatComments(text));  
    Debug.WriteLine("In generate operation.");  
}  
  
```  
  
## Клиентское приложение  
 Клиентское приложение загружает пользовательский импортер WSDL путем указания его в файле конфигурации приложения.  
  
```  
<client>  
  <endpoint address="http://localhost/servicemodelsamples/service.svc"   
  binding="wsHttpBinding"   
  contract="ICalculator" />  
  <metadata>  
    <wsdlImporters>  
      <extension type="Microsoft.ServiceModel.Samples.WsdlDocumentationImporter, WsdlDocumentation"/>  
    </wsdlImporters>  
  </metadata>  
</client>  
  
```  
  
 После указания пользовательского импортера система метаданных [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] загружает пользовательский импортер в любой созданный для этого объект <xref:System.ServiceModel.Description.WsdlImporter>.В этом образце используется <xref:System.ServiceModel.Description.MetadataExchangeClient> для загрузки метаданных, правильно настроенный <xref:System.ServiceModel.Description.WsdlImporter> для импорта метаданных с помощью создаваемого образецом пользовательского импортера и <xref:System.ServiceModel.Description.ServiceContractGenerator> для компиляции измененных сведений контракта в клиентский код Visual Basic и C\#, который можно использовать в Visual Studio для поддержки Intellisense или скомпилировать в XML\-документацию.  
  
```  
/// From WSDL Documentation:  
///   
/// <summary>The ICalculator contract performs basic calculation   
/// services.</summary>   
///   
[System.CodeDom.Compiler.GeneratedCodeAttribute("System.ServiceModel", "3.0.0.0")]  
[System.ServiceModel.ServiceContractAttribute(Namespace="http://Microsoft.ServiceModel.Samples", ConfigurationName="ICalculator")]  
public interface ICalculator  
{  
  
    /// From WSDL Documentation:  
    ///   
    /// <summary>The Add operation adds two numbers and returns the   
    /// result.</summary><returns>The result of adding the two arguments   
    /// together.</returns><param name="n1">The first value to add.</param><param   
    /// name="n2">The second value to add.</param>   
    ///   
    [System.ServiceModel.OperationContractAttribute(Action="http://Microsoft.ServiceModel.Samples/ICalculator/Add", ReplyAction="http://Microsoft.ServiceModel.Samples/ICalculator/AddResponse")]  
    double Add(double n1, double n2);  
  
    /// From WSDL Documentation:  
    ///   
    /// <summary>The Subtract operation subtracts the second argument from the   
    /// first.</summary><returns>The result of the second argument subtracted from the   
    /// first.</returns><param name="n1">The value from which the second is   
    /// subtracted.</param><param name="n2">The value that is subtracted from the   
    /// first.</param>   
    ///   
    [System.ServiceModel.OperationContractAttribute(Action="http://Microsoft.ServiceModel.Samples/ICalculator/Subtract", ReplyAction="http://Microsoft.ServiceModel.Samples/ICalculator/SubtractResponse")]  
    double Subtract(double n1, double n2);  
  
    /// From WSDL Documentation:  
    ///   
    /// <summary>The Multiply operation multiplies two values.</summary><returns>The   
    /// result of multiplying the first and second arguments.</returns><param   
    /// name="n1">The first value to multiply.</param><param name="n2">The second value   
    /// to multiply.</param>   
    ///   
    [System.ServiceModel.OperationContractAttribute(Action="http://Microsoft.ServiceModel.Samples/ICalculator/Multiply", ReplyAction="http://Microsoft.ServiceModel.Samples/ICalculator/MultiplyResponse")]  
    double Multiply(double n1, double n2);  
  
    /// From WSDL Documentation:  
    ///   
    /// <summary>The Divide operation returns the value of the first argument divided   
    /// by the second argument.</summary><returns>The result of dividing the first   
    /// argument by the second.</returns><param name="n1">The numerator.</param><param   
    /// name="n2">The denominator.</param>   
    ///   
    [System.ServiceModel.OperationContractAttribute(Action="http://Microsoft.ServiceModel.Samples/ICalculator/Divide", ReplyAction="http://Microsoft.ServiceModel.Samples/ICalculator/DivideResponse")]  
    double Divide(double n1, double n2);  
}  
```  
  
#### Настройка, построение и выполнение образца  
  
1.  Убедитесь, что выполнены процедуры, описанные в разделе [Процедура однократной настройки образцов Windows Communication Foundation](../../../../docs/framework/wcf/samples/one-time-setup-procedure-for-the-wcf-samples.md).  
  
2.  Чтобы создать выпуск решения на языке C\# или Visual Basic .NET, следуйте инструкциям в разделе [Построение образцов Windows Communication Foundation](../../../../docs/framework/wcf/samples/building-the-samples.md).  
  
3.  Чтобы выполнить образец на одном или нескольких компьютерах, следуйте инструкциям в разделе [Выполнение примеров Windows Communication Foundation](../../../../docs/framework/wcf/samples/running-the-samples.md).  
  
> [!IMPORTANT]
>  Образцы уже могут быть установлены на компьютере.Перед продолжением проверьте следующий каталог \(по умолчанию\).  
>   
>  `<диск_установки>:\WF_WCF_Samples`  
>   
>  Если этот каталог не существует, перейдите на страницу [Образцы Windows Communication Foundation \(WCF\) и Windows Workflow Foundation \(WF\) для .NET Framework 4](http://go.microsoft.com/fwlink/?LinkId=150780), чтобы загрузить все образцы [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] и [!INCLUDE[wf1](../../../../includes/wf1-md.md)].Этот образец расположен в следующем каталоге.  
>   
>  `<диск_установки>:\WF_WCF_Samples\WCF\Extensibility\Metadata\WsdlDocumentation`  
  
## См. также