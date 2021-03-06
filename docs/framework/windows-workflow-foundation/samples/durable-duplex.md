---
title: "Сохраняемый дуплекс | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 4e76d1a1-f3d8-4a0f-8746-4a322cdff6eb
caps.latest.revision: 10
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 10
---
# Сохраняемый дуплекс
В данном образце показана организация и настройка устойчивого дуплексного обмена сообщениями с помощью действий по обмену сообщениями в [!INCLUDE[wf](../../../../includes/wf-md.md)].Устойчивый дуплексный обмен сообщениями — это двусторонний обмен сообщениями в течение длительного времени.Длительность обмена сообщениями может превышать время существования коммуникационного канала и время существования экземпляров службы в памяти.  
  
## Подробные сведения об образце  
 В данном образце две службы [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)], реализованные с помощью [!INCLUDE[wf2](../../../../includes/wf2-md.md)] настроены на устойчивый дуплексный обмен сообщениями.Устойчивый дуплексный обмен сообщениями состоит из двух односторонних обменов сообщениями по MSMQ, которые сопоставляются с помощью [обмена контекстом .NET](http://go.microsoft.com/fwlink/?LinkID=166059).Сообщения отправляются посредством действий по обмену сообщениями <xref:System.ServiceModel.Activities.Send> и <xref:System.ServiceModel.Activities.Receive>.Обмен контекстом .NET используется для указания адреса обратного вызова в отправленных сообщениях.Обе службы размещаются при помощи служб активации процессов Windows WAS и настраиваются на включение сохраняемости экземпляров служб.  
  
 Первая служба \(Service1.xamlx\) отправляет в службу отправки \(Service2.xamlx\) запрос на выполнение задания.После выполнения задания служба Service2.xamlx уведомляет об этом службу Service1.xamlx.Приложение командной строки рабочего процесса настраивает прослушиваемые службами очереди и отправляет исходное сообщение "Start", активирующее службу Service1.xamlx.Получив уведомление о выполнения задания от службы Service2.xamlx, служба Service1.xamlx сохраняет результат в виде XML\-файла.В ожидании сообщения обратного вызова служба Service1.xamlx сохраняет состояние своего экземпляра при помощи <xref:System.ServiceModel.Activities.Description.WorkflowIdleBehavior> по умолчанию.Служба Service1.xamlx сохраняет состояние своего экземпляра при завершении задания, выполнение которого запрошено службой Service1.xamlx.  
  
 Чтобы обе службы могли использовать обмен контекстом .NET по MSMQ, они настраиваются на использование пользовательской привязки, состоящей из элементов <xref:System.ServiceModel.Channels.ContextBindingElement> и <xref:System.ServiceModel.Channels.MsmqTransportBindingElement>.При помощи элемента <xref:System.ServiceModel.Channels.ContextBindingElement> задается адрес обратного вызова, включаемый в соответствующий заголовок всех сообщений, отправляемых при помощи пользовательской привязки.Пользовательская привязка определяется в следующем примере кода.  
  
```xml  
<configuration>  
     <system.serviceModel>  
          …  
          <bindings>  
               <customBinding>  
                    <binding name="netMsmqContextBinding">  
                         <context clientCallbackAddress="net.msmq://localhost/private/DurableDuplex/Service1.xamlx"/>  
                         <msmqTransport exactlyOnce="False">  
                              <msmqTransportSecurity msmqAuthenticationMode="None" msmqProtectionLevel="None"/>  
                         </msmqTransport>  
                    </binding>  
               </customBinding>  
          </bindings>  
          …  
     </system.serviceModel>  
</configuration>  
  
```  
  
> [!NOTE]
>  Такая привязка не безопасна.При развертывании приложения следует настроить пользовательскую привязку, исходя из требований к безопасности приложения.  
  
> [!NOTE]
>  Используемые в данном образце очереди не являются транзакционными.Образец, показывающий, как настроить обмен сообщениями [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] при помощи транзакционных очередей, см. по адресу [Активация MSMQ](../../../../docs/framework/wcf/samples/msmq-activation.md).  
  
 Сообщение от службы Service1.xamlx службе Service2.xamlx отправляется через конечную точку клиента, настроенную на адрес службы Service2.xamlx и на определенную ранее пользовательскую привязку.Обратный вызов от Service2.xamlx службе Service1.xamlx отправляется через конечную точку клиента, не настроенную явно на адрес, так как адрес получается из контекста обратного вызова, отправленного службой Service1.xamlx.Конечные точки клиента определяются в следующем примере кода.  
  
```xml  
<?xml version="1.0"?>  
<configuration>  
     <system.serviceModel>  
          …  
          <client>  
               <endpoint address="net.msmq://localhost/private/DurableDuplex/Service2.xamlx" binding="customBinding" bindingConfiguration="netMsmqContextBinding" contract="IDoWork"/>  
               <endpoint binding="customBinding" bindingConfiguration="netMsmqContextBinding" contract="INotify"/>  
          </client>  
          …  
     </system.serviceModel>  
</configuration>  
  
```  
  
 В следующем примере кода конечные точки, использующие данную пользовательскую привязку, предоставляются посредством изменения стандартного сопоставления протокола для базовых адресов net.msmq, которые будут использовать данную пользовательскую привязку.  
  
```  
<configuration>  
     <system.serviceModel>  
          <protocolMapping>  
               <add scheme="net.msmq" binding="customBinding" bindingConfiguration="netMsmqContextBinding"/>  
          </protocolMapping>  
          …  
     </system.serviceModel>  
</configuration>  
  
```  
  
 В следующем примере кода активируется сохраняемость для обеих служб путем добавления к ним поведения <xref:System.ServiceModel.Activities.Description.SqlWorkflowInstanceStoreBehavior> и указания строки подключения к базе данных сохраняемости.  
  
```xml  
<?xml version="1.0"?>  
<configuration>  
    <system.serviceModel>  
          …  
          <behaviors>  
               <serviceBehaviors>  
                    <behavior>  
                         <serviceDebug includeExceptionDetailInFaults="True"/>  
                         <serviceMetadata httpGetEnabled="True"/>  
                         <sqlWorkflowInstanceStore connectionString="Data Source=.\SQLEXPRESS;Initial Catalog=DefaultSampleStore;Integrated Security=True"/>  
                    </behavior>  
               </serviceBehaviors>  
          </behaviors>  
     </system.serviceModel>  
</configuration>  
  
```  
  
## Системные требования  
 Для данного образца требуются следующие компоненты.  
  
1.  Службы IIS.  
  
2.  Службы IIS \-\> Совместимость управления IIS 6.0 \-\> Метабаза IIS и совместимость конфигурации IIS 6.0.  
  
3.  Службы Интернета \> Компоненты разработки приложений \> ASP.NET.  
  
4.  Сервер очереди сообщений \(Майкрософт\) \(MSMQ\).  
  
#### Использование этого образца  
  
1.  Настройте базы данных сохраняемости и каталог результатов.  
  
    1.  Откройте окно командной строки [!INCLUDE[vs2010](../../../../includes/vs2010-md.md)].  
  
    2.  Перейдите в каталог образцов и запустите команду Setup.cmd.  
  
2.  Настройте виртуальное приложение.  
  
    1.  Введите в командной строке [!INCLUDE[vs2010](../../../../includes/vs2010-md.md)] следующую команду, чтобы зарегистрировать ASP.NET.  
  
        ```  
        aspnet_regiis -i  
        ```  
  
    2.  Запустите [!INCLUDE[vs2010](../../../../includes/vs2010-md.md)] с правами администратора. Для этого щелкните правой кнопкой мыши значок [!INCLUDE[vs2010](../../../../includes/vs2010-md.md)] и выберите команду **Запуск с правами администратора**.  
  
    3.  Откройте в среде [!INCLUDE[vs2010](../../../../includes/vs2010-md.md)] файл DurableDuplex.sln.  
  
3.  Настройте очереди обслуживания.  
  
    1.  Чтобы запустить клиент DurableDuplex, нажмите кнопку F5.  
  
    2.  Откройте консоль **Управление компьютером**, выполнив команду `Compmgmt.msc` из командной строки.  
  
    3.  В разделе **Службы и приложения** разверните раздел **Очередь сообщений**.**Частные очереди**.  
  
    4.  Щелкните правой кнопкой мыши очереди durableduplex\/service1.xamlx и durableduplex\/service2.xamlx и выберите в раскрывающемся меню пункт **Свойства**.  
  
    5.  Откройте вкладку **Безопасность** и предоставьте обоим очередям права **Получение сообщения**, **Просмотр сообщения** и **Отправка сообщения**.  
  
    6.  Откройте Диспетчер служб IIS.  
  
    7.  Перейдите на вкладку **Сервер**, **Узлы**, **Веб\-узел по умолчанию**, **Частный**, **Устойчивый дуплекс** и выберите пункт **Дополнительные параметры**.  
  
    8.  Измените список **Разрешенные протоколы**, добавив в него **http,net.msmq**.  
  
4.  Выполните образец.  
  
    1.  Откройте страницы http:\/\/localhost\/private\/durableduplex\/service1.xamlx и http:\/\/localhost\/private\/durableduplex\/service2.xamlx и проверьте, запущены ли обе службы.  
  
    2.  Чтобы запустить DurableDuplexClient, нажмите кнопку F5.  
  
         По завершению устойчивого дуплексного обмена сообщениями в каталоге C:\\Inbox сохраняется файл result.xml с результатами обмена.  
  
#### Очистка \(необязательно\)  
  
1.  Запустите команду Cleanup.cmd.  
  
    1.  Откройте окно командной строки [!INCLUDE[vs2010](../../../../includes/vs2010-md.md)].  
  
    2.  Перейдите в каталог образцов и запустите команду Cleanup.cmd.  
  
2.  Удалите виртуальное приложение служб.  
  
    1.  Откройте диспетчер служб IIS, запустив из командной строки `Inetmgr.exe`.  
  
    2.  Откройте веб\-сайт по умолчанию и удалите виртуальный каталог **private**.  
  
3.  Удалите заданные для данного образца очереди.  
  
    1.  Откройте консоль «Управление компьютером», выполнив команду `Compmgmt.msc` из командной строки.  
  
    2.  Разверните узлы **Служба и приложения**, **Очередь сообщений**, **Частные очереди**.  
  
    3.  Удалите очереди durableduplex\/service1.xamlx и durableduplex\/service2.xamlx.  
  
4.  Удалите каталог C:\\Inbox.  
  
> [!IMPORTANT]
>  Образцы уже могут быть установлены на компьютере.Перед продолжением проверьте следующий каталог \(по умолчанию\).  
>   
>  `<диск_установки>:\WF_WCF_Samples`  
>   
>  Если этот каталог не существует, перейдите на страницу [Образцы Windows Communication Foundation \(WCF\) и Windows Workflow Foundation \(WF\) для .NET Framework 4](http://go.microsoft.com/fwlink/?LinkId=150780), чтобы загрузить все образцы [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] и [!INCLUDE[wf1](../../../../includes/wf1-md.md)].Этот образец расположен в следующем каталоге.  
>   
>  `<диск_установки>:\WF_WCF_Samples\WF\Basic\Services\DurableDuplex`  
  
## См. также