---
title: "Проверяющий элемент управления для имен пользователей и паролей | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 42f03841-286b-42d8-ba58-18c75422bc8e
caps.latest.revision: 18
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 18
---
# Проверяющий элемент управления для имен пользователей и паролей
В этом образце показано, как реализовать пользовательский проверяющий элемент управления UserNamePassword.Это бывает полезно в случаях, когда ни один из встроенных режимов проверки имени пользователя и пароля не соответствует требования приложениям, например, когда пары "имя пользователя\-пароль" хранятся во внешнем хранилище, например в базе данных.В этом образце показана служба, имеющая пользовательский проверяющий элемент управления, который проверяет две конкретных пары "имя пользователя\-пароль".Клиент использует такие пары "имя пользователя\-пароль" для проверки подлинности у службы.  
  
> [!IMPORTANT]
>  Образцы уже могут быть установлены на компьютере.Перед продолжением проверьте следующий каталог \(по умолчанию\).  
>   
>  `<диск_установки>:\WF_WCF_Samples`  
>   
>  Если этот каталог не существует, перейдите на страницу [Образцы Windows Communication Foundation \(WCF\) и Windows Workflow Foundation \(WF\) для .NET Framework 4](http://go.microsoft.com/fwlink/?LinkId=150780), чтобы загрузить все образцы [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] и [!INCLUDE[wf1](../../../../includes/wf1-md.md)].Этот образец расположен в следующем каталоге.  
>   
>  `<диск_установки>:\WF_WCF_Samples\WCF\Extensibility\Security\UserNamePasswordValidator`  
  
> [!NOTE]
>  Поскольку учетные данные имени пользователя, использующие пары "имя пользователя\-пароль", принимаемые пользовательским проверяющим элементом управления, могут создаваться кем угодно, такая служба является менее безопасной по сравнению с поведением по умолчанию, которое предоставляется стандартным проверяющим элементом управления UserNamePassword.Стандартный проверяющий элемент управления UserNamePassword предпринимает попытку сопоставить заданную пару "имя пользователя\-пароль" с учетной записью Windows, и если выполнить такое сопоставление не удается, проверка подлинности завершается неудачно.Пользовательский проверяющий элемент управления UserNamePassword в этом образце НЕ ДОЛЖЕН использоваться в рабочей среде; он предназначен только для иллюстрации.  
  
 Таким образом, в этом образце демонстрируются указанные ниже возможности.  
  
-   Подлинность клиента может проверяться с помощью маркера имени пользователя.  
  
-   Сервер проверяет учетные данные клиента с помощью проверяющего элемента управления, а пользовательские ошибки из логики проверки имени пользователя и пароля распространяются на клиент.  
  
-   Сервер проходит проверку подлинности с использованием сертификата X.509 сервера.  
  
 Служба предоставляет доступ к одной конечной точке для взаимодействия со службой, определенной в файле конфигурации App.config.Конечная точка состоит из адреса, привязки и контракта.Привязка настраивается с помощью стандартного элемента `wsHttpBinding`, который по умолчанию использует WS\-Security и проверку подлинности имени пользователя.В поведении службы задается режим `Custom` проверки пар "имя пользователя\-пароль" клиента, а также тип класса проверяющего элемента управления.Коме того, поведение с помощью элемента `serviceCertificate` задает сертификат сервера.Свойство `SubjectName` сертификата сервера должно совпадать со значением `findValue` в элементе конфигурации [\<serviceCertificate\>](../../../../docs/framework/configure-apps/file-schema/wcf/servicecertificate-of-servicecredentials.md).  
  
```  
<system.serviceModel>  
  <services>  
    <service name="Microsoft.ServiceModel.Samples.CalculatorService"  
             behaviorConfiguration="CalculatorServiceBehavior">  
      <!-- use host/baseAddresses to configure base address provided by host -->  
      <host>  
        <baseAddresses>  
          <add baseAddress ="http://localhost:8001/servicemodelsamples/service" />  
        </baseAddresses>  
      </host>  
      <!-- use base address specified above, provide one endpoint -->  
      <endpoint address="username"  
                binding="wsHttpBinding"  
                bindingConfiguration="Binding"   
                contract="Microsoft.ServiceModel.Samples.ICalculator" />  
    </service>  
  </services>  
  
  <bindings>  
    <wsHttpBinding>  
      <!-- username binding -->  
      <binding name="Binding">  
        <security mode="Message">  
          <message clientCredentialType="UserName" />  
        </security>  
      </binding>  
    </wsHttpBinding>  
  </bindings>  
  
  <behaviors>  
    <serviceBehaviors>  
      <behavior name="CalculatorServiceBehavior">  
        <serviceDebug includeExceptionDetailInFaults ="true"/>  
        <serviceCredentials>  
          <!--   
          The serviceCredentials behavior allows one to   
          specify a custom validator for username/password  
          combinations.  
          -->  
          <userNameAuthentication userNamePasswordValidationMode="Custom"  
                                  customUserNamePasswordValidatorType="Microsoft.ServiceModel.Samples.CalculatorService+MyCustomUserNameValidator, service" />  
          <!--   
          The serviceCredentials behavior allows one to define a service certificate. A service certificate is used by a client to authenticate the service and provide message protection. You must specify a server certificate when passing username/passwords to encrypt the information as it is sent on the wire. Otherwise the username and password information would be sent as clear text. This configuration references the "localhost" certificate installed during the setup instructions.  
          -->  
          <serviceCertificate findValue="localhost" storeLocation="LocalMachine" storeName="My" x509FindType="FindBySubjectName" />  
        </serviceCredentials>  
      </behavior>  
    </serviceBehaviors>  
  </behaviors>  
  
</system.serviceModel>  
  
```  
  
 Конфигурация конечной точки клиента состоит из имени конфигурации, абсолютного адреса конечной точки службы, привязки и контракта.Привязка клиента настраивается с помощью соответствующего режима и `clientCredentialType` сообщения.  
  
```  
<system.serviceModel>  
  
    <client>  
      <!-- Username based endpoint -->  
      <endpoint name="Username"  
address="http://localhost:8001/servicemodelsamples/service/username"   
                binding="wsHttpBinding"   
                bindingConfiguration="Binding"   
                behaviorConfiguration="ClientCertificateBehavior"  
                contract="Microsoft.ServiceModel.Samples.ICalculator">  
      </endpoint>  
    </client>  
  
    <bindings>  
      <wsHttpBinding>  
        <!-- Username binding -->  
        <binding name="Binding">  
          <security mode="Message">  
            <message clientCredentialType="UserName" />  
          </security>  
        </binding>  
      </wsHttpBinding>  
    </bindings>  
    <behaviors>  
      <endpointBehaviors>  
        <behavior name="ClientCertificateBehavior">  
          <clientCredentials>  
            <serviceCertificate>  
              <!--   
            Setting the certificateValidationMode to PeerOrChainTrust means that if the certificate   
            is in the user's Trusted People store, then it will be trusted without performing a  
            validation of the certificate's issuer chain. This setting is used here for convenience so that the   
            sample can be run without having to have certificates issued by a certification authority (CA).  
            This setting is less secure than the default, ChainTrust. The security implications of this   
            setting should be carefully considered before using PeerOrChainTrust in production code.   
            -->  
              <authentication certificateValidationMode="PeerOrChainTrust" />  
            </serviceCertificate>  
          </clientCredentials>  
        </behavior>  
      </endpointBehaviors>  
    </behaviors>  
  
  </system.serviceModel>  
  
```  
  
 Реализация клиента запрашивает у пользователя имя пользователя и пароль.  
  
```  
// Get the username and password  
Console.WriteLine("Username authentication required.");  
Console.WriteLine("Provide a username.");  
Console.WriteLine("   Enter username: (test1)");  
string username = Console.ReadLine();  
Console.WriteLine("   Enter password:");  
string password = "";  
ConsoleKeyInfo info = Console.ReadKey(true);  
while (info.Key != ConsoleKey.Enter)  
{  
    if (info.Key != ConsoleKey.Backspace)  
    {  
        if (info.KeyChar != '\0')  
        {  
            password += info.KeyChar;  
        }  
        info = Console.ReadKey(true);  
    }  
    else if (info.Key == ConsoleKey.Backspace)  
    {  
        if (password != "")  
        {  
            password = password.Substring(0, password.Length - 1);  
        }  
        info = Console.ReadKey(true);  
    }  
}  
for (int i = 0; i < password.Length; i++)  
{  
    Console.Write("*");  
}  
Console.WriteLine();  
// Create a proxy with Certificate endpoint configuration  
CalculatorProxy proxy = new CalculatorProxy("Username")  
try  
{  
  proxy.ClientCredentials.Username.Username = username;  
  proxy.ClientCredentials.Username.Password = password;  
    // Call the Add service operation.  
    double value1 = 100.00D;  
    double value2 = 15.99D;  
    double result = proxy.Add(value1, value2);  
    Console.WriteLine("Add({0},{1}) = {2}", value1, value2, result);  
  }  
  catch (Exception e)  
  {  
      Console.WriteLine("Call failed:");  
      while (e != null)  
      {  
          Console.WriteLine("\t{0}", e.Message);  
          e = e.InnerException;  
      }  
      proxy.Abort();  
  }  
}  
  
```  
  
 В этом образце для проверки пар "имя пользователя\-пароль" используется пользовательский элемент UserNamePasswordValidator.В этом образце реализуется класс `CustomUserNamePasswordValidator`, наследуемый от класса <xref:System.IdentityModel.Selectors.UserNamePasswordValidator>.Дополнительные сведения см. в разделе, посвященном <xref:System.IdentityModel.Selectors.UserNamePasswordValidator>.В данном образце пользовательского элемента управления реализуется метод `Validate`, принимающий две конкретные пары "имя пользователя\-пароль", как показано в следующем фрагменте кода.  
  
```  
public class CustomUserNameValidator : UserNamePasswordValidator  
{  
 // This method validates users. It allows in two users,   
 // test1 and test2 with passwords 1tset and 2tset respectively.  
 // This code is for illustration purposes only and   
 // MUST NOT be used in a production environment because it   
 // is NOT secure.  
 public override void Validate(string userName, string password)  
 {  
  if (null == userName || null == password)  
  {  
   throw new ArgumentNullException();  
  }  
  
  if (!(userName == "test1" && password == "1tset") && !(userName == "test2" && password == "2tset"))  
  {  
   throw new FaultException("Unknown Username or Incorrect Password");  
   }  
  }  
 }  
```  
  
 После реализации в коде службы проверяющего элемента управления необходимо проинформировать узел службы о проверяющем элементе управления, который следует использовать.Для этого можно воспользоваться следующим фрагментом кода.  
  
```  
serviceHost.Credentials.UserNameAuthentication.UserNamePasswordValidationMode = UserNamePasswordValidationMode.Custom;  
serviceHost.Credentials. UserNameAuthentication.CustomUserNamePasswordValidator = new CustomUserNamePasswordValidator();  
```  
  
 Либо можно решить эту же задачу с помощью файла конфигурации, как показано ниже.  
  
```  
<behaviors>  
 <serviceBehaviors>  
  <behavior name="CalculatorServiceBehavior">  
  ...  
   <serviceCredentials>  
    <!--   
    The serviceCredentials behavior allows one to specify authentication constraints on username / password combinations.  
    -->  
    <userNameAuthentication userNamePasswordValidationMode="Custom" customUserNamePasswordValidatorType="Microsoft.ServiceModel.Samples.CalculatorService+CustomUserNameValidator, service" />  
   ...  
  </behavior>  
 </serviceBehaviors>  
</behaviors>  
  
```  
  
 При выполнении образца запросы и ответы операций отображаются в окне консоли клиента.Клиент должен успешно вызывать все методы.Чтобы закрыть клиент, нажмите клавишу ВВОД в окне клиента.  
  
## Пакетный файл Setup  
 Входящий в состав образца файл Setup.bat позволяет настроить для сервера соответствующие сертификаты, необходимые для выполнения резидентного приложения, которое требует обеспечения безопасности на основе сертификата сервера.Этот пакетный файл необходимо изменить, чтобы его можно было использовать на нескольких компьютерах или без резидентного размещения приложения.  
  
 Ниже представлены общие сведения о различных разделах пакетных файлов, позволяющие изменять их для выполнения в соответствующей конфигурации.  
  
-   Создание сертификата сервера.  
  
     Следующие строки из файла Setup.bat создают используемый в дальнейшем сертификат сервера.Переменная %SERVER\_NAME% задает имя сервера.Измените эту переменную, чтобы задать собственное имя сервера.Значением по умолчанию является localhost.  
  
    ```  
    echo ************  
    echo Server cert setup starting  
    echo %SERVER_NAME%  
    echo ************  
    echo making server cert  
    echo ************  
    makecert.exe -sr LocalMachine -ss MY -a sha1 -n CN=%SERVER_NAME% -sky exchange -pe  
  
    ```  
  
-   Установка сертификата сервера в хранилище доверенных сертификатов клиента.  
  
     Следующие строки из файла Setup.bat копируют сертификат сервера в хранилище доверенных лиц клиента.Этот шаг является обязательным, поскольку сертификаты, созданные с помощью программы Makecert.exe, не получают неявного доверия со стороны клиентской системы.Если уже имеется сертификат, имеющий доверенный корневой сертификат клиента, например сертификат, выпущенный корпорацией Майкрософт, выполнять этот шаг по добавлению сертификата сервера в хранилище сертификатов клиента не требуется.  
  
    ```  
    certmgr.exe -add -r LocalMachine -s My -c -n %SERVER_NAME% -r CurrentUser -s TrustedPeople  
    ```  
  
#### Настройка и построение образца  
  
1.  Чтобы построить решение, следуйте инструкциям раздела [Построение образцов Windows Communication Foundation](../../../../docs/framework/wcf/samples/building-the-samples.md).  
  
2.  Чтобы выполнить образец на одном или нескольких компьютерах, выполните следующие инструкции.  
  
#### Запуск образца на том же компьютере  
  
1.  Из командной строки [!INCLUDE[vs_current_long](../../../../includes/vs-current-long-md.md)] запустите файл Setup.bat из папки установки образца.При этом устанавливаются все сертификаты, необходимые для запуска образца.  
  
    > [!NOTE]
    >  Пакетный файл Setup.bat предназначен для запуска из командной строки [!INCLUDE[vs_current_long](../../../../includes/vs-current-long-md.md)].Переменная среды PATH, заданная в командной строке [!INCLUDE[vs_current_long](../../../../includes/vs-current-long-md.md)], указывает на каталог, содержащий исполняемые файлы, необходимые для скрипта Setup.bat.  
  
2.  Запустите программу Service.exe из папки service\\bin.  
  
3.  Запустите программу Client.exe из каталога \\client\\bin.Действия клиента отображаются в консольном приложении клиента.  
  
4.  Если клиенту и службе не удается взаимодействовать, см. раздел [Troubleshooting Tips](http://msdn.microsoft.com/ru-ru/8787c877-5e96-42da-8214-fa737a38f10b).  
  
#### Выполнение образца на нескольких компьютерах  
  
1.  Создайте на компьютере службы каталог для двоичных файлов службы.  
  
2.  Скопируйте файлы служебной программы из каталога службы на компьютер службы.Кроме того, скопируйте файлы Setup.bat и Cleanup.bat на компьютер службы.  
  
3.  Необходимо иметь сертификат сервера с именем субъекта, содержащим полное доменное имя компьютера.Необходимо обновить файл конфигурации сервера, чтобы в нем отражалось это новое имя сертификата.  
  
4.  Скопируйте сертификат сервера в хранилище CurrentUser\-TrustedPeople клиента.Это нужно делать только в том случае, если сертификат сервера не выдан центром выдачи, которому доверяет клиент.  
  
5.  В файле App.config компьютера службы также измените значение базового адреса для указания полного доменного имени компьютера вместо localhost.  
  
6.  На компьютере службы из окна командной строки запустите программу Service.exe.  
  
7.  Скопируйте на клиентский компьютер файлы программы клиента из папки \\client\\bin\\ в языковой папке.  
  
8.  В файле Client.exe.config на клиентском компьютере измените значение адреса конечной точки, чтобы оно соответствовало новому адресу службы.  
  
9. На клиентском компьютере из окна командной строки запустите программу Client.exe.  
  
10. Если клиенту и службе не удается взаимодействовать, см. раздел [Troubleshooting Tips](http://msdn.microsoft.com/ru-ru/8787c877-5e96-42da-8214-fa737a38f10b).  
  
#### Очистка после образца  
  
1.  После завершения работы образца запустите в папке образцов файл Cleanup.bat.Он удалит из хранилища сертификатов сертификат сервера.  
  
## См. также