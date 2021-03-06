---
title: "Набор средств внешнего набора правил | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: a306d283-a031-475e-aa01-9ae86e7adcb0
caps.latest.revision: 7
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 7
---
# Набор средств внешнего набора правил
Обычно, когда правила используются в приложении рабочего процесса, они являются частью сборки.  В некоторых случаях, возможно, потребуется создать наборы правил отдельно от сборки, чтобы можно было обновлять их, без повторного построения и развертывания сборки рабочего процесса.  В этом образце показано, как управлять и изменять наборы правил в базе данных, а также как получить доступ к этим наборам правил из рабочего процесса в среде выполнения.  Это позволяет запускать экземпляры рабочего процесса, чтобы автоматически включать изменения в наборах правил.  
  
 В примере набора средств "External RuleSet Toolkit" содержится средство на основе Windows Forms, которое можно использовать для управления и изменения версий набора правил в базе данных.  Также включены действие и служба размещения для выполнения этих правил.  
  
> [!NOTE]
>  Для этого примера требуется [Microsoft SQL Server](http://go.microsoft.com/fwlink/?LinkId=96181).  
  
 В [!INCLUDE[vsprvsext](../../../../includes/vsprvsext-md.md)] редактор набора правил предоставляется как часть Windows Workflow Foundation \(WF\).  Редактор запускается двойным щелчком действия `Policy` в рабочем процессе; редактор выполняет сериализацию объекта набора правил в файл с расширением RULES, связанный с рабочим процессом \(действие `Policy` запускает экземпляр набора правил из рабочего процесса\).  При построении проекта рабочего процесса выполняет компиляция в сборку файла с расширением RULES как ресурса.  
  
 Компоненты данного образца включают следующее:  
  
-   Средство пользовательского графического интерфейса набора правил, которое используется для редактирования и управления версиями набора правил в базе данных.  
  
-   Служба набора правил, настройка которой выполняется в ведущем приложении, осуществляет доступ к наборам правил из базы данных.  
  
-   Действие `ExternalPolicy` запрашивает набор правил из службы набора правил и запускает набор правил из рабочего процесса.  
  
 Взаимодействие компонентов показано на рисунке 1.  В следующих разделах приведено описание каждого из компонентов.  
  
 ![Обзорная схема примера External RuleSet](../../../../docs/framework/windows-workflow-foundation/samples/media/rulesettoolkitsampleoverview.gif "RuleSetToolkitSampleOverview")  
  
 Рисунок 1. Обзор примеров  
  
> [!IMPORTANT]
>  Образцы уже могут быть установлены на компьютере.  Перед продолжением проверьте следующий каталог \(по умолчанию\).  
>   
>  `<ДискУстановки>:\WF_WCF_Samples`  
>   
>  Если этот каталог не существует, перейдите на страницу [Примеры Windows Communication Foundation \(WCF\) и Windows Workflow Foundation \(WF\) для .NET Framework 4](http://go.microsoft.com/fwlink/?LinkId=150780), чтобы скачать все примеры [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)] и [!INCLUDE[wf1](../../../../includes/wf1-md.md)].  Этот образец расположен в следующем каталоге.  
>   
>  `<ДискУстановки>:\WF_WCF_Samples\WF\Scenario\ExternalRuleSetToolKit`  
  
## Средство "RuleSet"  
 Снимок экрана средства RuleSet показан на рисунке 2.  Из меню **Хранилище правил** можно загрузить доступные наборы правил из базы данных и сохранить отредактированные наборы правил обратно в хранилище.  Файл конфигурации приложения предоставляет строку подключения базы данных для базы данных набора правил.  При запуске это средство автоматически загружает наборы правил из настроенной базы данных.  
  
 ![Выходные данные примера External RuleSet Toolkit](../../../../docs/framework/windows-workflow-foundation/samples/media/rulesetbrowser.gif "RuleSetBrowser")  
  
 Рисунок 2. Средство просмотра набора правил  
  
 Средство "RuleSet" служит для применения основного и вспомогательного номеров версий к наборам правил, что позволяет одновременно создавать и хранить несколько версий \(средство не предоставляет функции блокировки или другие функции управления конфигурацией, помимо поддержки версий\).  С помощью данного средства можно создавать новые версии набора правил или удалять существующие версии.  При выборе команды **Создать** средство создает новое имя набора правил и применяет номер версии 1.0.  При копировании версии средство создает копию выбранной версии набора правил, включая содержащиеся правила, и присваивает новые уникальные номера версий.  Эти номера версий основаны на номерах версий существующих наборов правил.  Имя и номера версий набора правил можно изменить с помощью связанных полей на форме.  
  
 При выборе команды **Редактировать правила** запускается редактор набора правил, как показано на рисунке 3.  
  
 ![Выходные данные примера External RuleSet Toolkit](../../../../docs/framework/windows-workflow-foundation/samples/media/ruleseteditor.gif "RuleSetEditor")  
  
 Рисунок 3. Редактор набора правил  
  
 Это повторное размещение диалогового окна редактора, которое является частью надстройки Windows Workflow Foundation [!INCLUDE[vsprvs](../../../../includes/vsprvs-md.md)].  Оно предоставляет тот же набор функциональных возможностей, включая поддержку Intellisense.  Правила создаются по конечному типу \(например, рабочий процесс\), связанному с набором правил в средстве. При выборе команды **Просмотр** в главном диалоговом окне средства отображается диалоговое окно **Выбор рабочего процесса или типа**, как показано на рисунке 4.  
  
 ![Выбор типа &#47; рабочего процесса](../../../../docs/framework/windows-workflow-foundation/samples/media/71f08d57-e8f2-499e-8151-ece2cbdcabfd.gif "71f08d57\-e8f2\-499e\-8151\-ece2cbdcabfd")  
  
 Рисунок 4. Выбор рабочего процесса или типа  
  
 Диалоговое окно **Выбор рабочего процесса или типа** можно использовать для указания сборки и конкретного типа в данной сборке.  Этот тип является конечным типом, по которому создаются \(и запускаются\) правила.  В большинстве случаев конечным типом является рабочий процесс или какой\-либо другой тип действия.  Тем не менее можно запустить набор правил по любому типу .NET.  
  
 Путь к файлу сборки и имя типа `name are stored with the` хранятся с набором правил в базе данных, поэтому, когда набор правил извлекается из базы данных, средство пытается автоматически загрузить конечный тип.  
  
 При нажатии кнопки **ОК** в диалоговом окне **Выбор рабочего процесса или типа** выполняется проверка выбранного типа на соответствие с набором правил, чтобы убедиться, что конечный тип содержит все члены, на которые ссылаются правила.  Ошибки отображаются в диалоговом окне **Ошибки при проверке** \(см. рисунок 5\).  Можно продолжить изменение несмотря на ошибки или нажать кнопку **Отмена**.  В меню **Инструменты** главного диалогового окна средства можно выбрать команду **Проверка**, чтобы выполнить повторную проверку версии набора правил на соответствие конечному действию.  
  
 ![Ошибки проверки, выдаваемые примером External RuleSet](../../../../docs/framework/windows-workflow-foundation/samples/media/validationerrorsruleset.png "ValidationErrorsRuleSet")  
  
 Рисунок 5. Ошибки при проверке  
  
 Импортировать и экспортировать наборы правил можно из меню **Данные** в средстве.  При выборе команды **Импорт** отображается диалоговое окно выбора файла, в котором можно выбрать RULES\-файл.  Этот файл может являться или не являться файлом, изначально созданным в [!INCLUDE[vsprvs](../../../../includes/vsprvs-md.md)].  В RULES\-файле должен содержаться сериализованный экземпляр `RuleDefinitions` с коллекцией условий и коллекцией наборов правил.  Средство не использует коллекцию условий, однако использует формат `RuleDefinitions` RULES, чтобы обеспечить взаимодействие со средой [!INCLUDE[vsprvs](../../../../includes/vsprvs-md.md)].  
  
 После выбора RULES\-файла отображается диалоговое окно **Выбор набора правил** \(см. рисунок 6\).  Можно использовать диалоговое окно для выбора наборов правил из файла, который требуется импортировать \(по умолчанию указаны все наборы правил\).  Наборы правил в RULES\-файле не содержат номеров версии, поскольку их версии в рамках проекта WF совпадают с версией сборки.  В процессе импорта средство автоматически присваивает следующий доступный основной номер версии \(который может быть изменен после импорта\). Присвоенные номера версий можно просмотреть в списке **Выбор набора правил**.  
  
 Для каждого импортируемого набора правил средство пытается поместить связанный тип из папки bin\\Debug в местоположение RULES\-файла \(если есть\), в зависимости от членов, используемых в наборе правил.  Если средство обнаруживает несколько соответствующих типов, оно пытается выбирать тип в соответствии с совпадением между именем RULES\-файла и именем типа \(например, тип `Workflow1` соответствует файлу "Workflow1.rules"\).  При наличии нескольких совпадений предлагается выбрать тип.  Если найти соответствующую сборку или тип с помощью данного механизма автоматической идентификации не удается, после импорта можно выбрать команду **Просмотр** в главном диалоговом окне средства и перейти к связанному типу.  
  
 ![Выбор набора правил](../../../../docs/framework/windows-workflow-foundation/samples/media/rulesetselector.gif "RuleSetSelector")  
  
 Рисунок 6. Выбор набора правил  
  
 При выборе команды **Экспорт данных** в главном меню средства снова отображается диалоговое окно **Выбор набора правил**, в котором можно определить наборы правил из базы данных, которые требуется экспортировать.  При нажатии кнопки **ОК** отображается диалоговое окно **Сохранение файла**, в котором можно задать имя и местоположение конечного RULES\-файла.  Поскольку в RULES\-файл не содержатся сведения о версии, для заданного имени набора правил можно выбрать только одну версию набора правил.  
  
## Действие "PolicyFromService".  
 Код для действия `PolicyFromService` является прямолинейным.  Функционирование этого кода во многом аналогично действию `Policy`, предоставляемому в WF, однако вместо извлечения конечного набора правил из RULES\-файла данный код выполняет вызов службы размещения, чтобы получить экземпляр набора правил.  Затем код запускает набор правил из экземпляра действия корневого рабочего процесса.  
  
 Чтобы использовать данное действие в рабочем процессе, следует добавить ссылку на сборки `PolicyActivities` и `RuleSetService` из проекта рабочего процесса.  Обсуждение способов добавления действия на панель инструментов см. в процедуре в конце данного раздела.  
  
 После того, как действие помещено в рабочий процесс, необходимо указать имя набора правил, который требуется запустить.  Можно ввести имя как буквенное значение, либо выполнить привязку к переменной рабочего процесса или свойству другого действия.  Дополнительно можно задать номера версий для конкретного набора правил, который требуется запустить.  Если оставить значение по умолчанию "0" для основного и вспомогательного номеров версий, действию будет автоматически присвоен номер последней версии в базе данных.  
  
## Служба "RuleSet"  
 Эта служба отвечает за извлечение заданной версии набора правил из базы данных и ее возврат в вызывающее действие.  Как уже обсуждалось ранее, если значения основной и вспомогательной версии, переданные в вызов `GetRuleSet` оба равны "0", служба выполняет извлечение последней версии.  На этой стадии не выполняется кэширование определений или экземпляров набора правил; также, отсутствуют функции пометки версий набора правил как "развертываемых", чтобы отличить их от наборов правил в процессе выполнения.  
  
 База данных, доступ к которой осуществляется службой, должна быть настроена на узле с помощью файла конфигурации приложения.  
  
#### Запуск средства  
  
1.  В папке, в которой задана таблица набора правил, используемая средством и службой, также содержится файл "Setup.sql".  Можно запустить пакетный файл "Setup.cmd", чтобы создать базу данных правил в SQL Express и задать таблицу набора правил.  
  
2.  При редактировании пакетного файла или файла "Setup.sql", если использование SQL Express или размещение таблицы в базе данных с именем, отличным от `Rules`, не задано, файлы конфигурации приложения в средстве "RuleSet" и проекты `UsageSample` следует редактировать, используя одинаковые сведения.  
  
3.  После запуска скрипта "Setup.sql" можно построить решение `ExternalRuleSetToolkit`, а затем запустить средство "RuleSet" из проекта "ExternalRuleSetTool".  
  
4.  Решение консольного приложения последовательного рабочего процесса `RuleSetToolkitUsageSample` включает образец рабочего процесса.  Рабочий процесс включает действие `PolicyFromService` и две переменные: `orderValue` и `discount`, для которых выполняется запуск конечного набора правил.  
  
5.  Чтобы использовать этот образец, необходимо построить решение `RuleSetToolkitUsageSample`.  Затем из главного меню средства "RuleSet" выберите команду **Импорт данных** и укажите файл "DiscountRuleSet.rules" в папке "RuleSetToolkitUsageSample".  Выберите команду **Хранилище правил \- Сохранить** в меню, чтобы сохранить импортированный набор правил в базу данных.  
  
6.  Поскольку ссылка на сборку `PolicyActivities` дана из образца проекта рабочего процесса, действие `PolicyFromService` используется в рабочем процессе.  Тем не менее, это оно не отображается на панели инструментов по умолчанию.  Чтобы добавить действие на панель инструментов, необходимо сделать следующее.  
  
    -   Щелкните правой кнопкой мыши панель инструментов и выберите команду **Выбрать элементы** \(может занять некоторое время\).  
  
    -   Когда откроется диалоговое окно **Выбор элементов панели инструментов**, перейдите на вкладку **Действие**.  
  
    -   Перейдите к сборке `PolicyActivities` в решении `ExternalRuleSetToolkit` и выберите команду **Открыть**.  
  
    -   Убедитесь, что действие `PolicyFromService` выделено в диалоговом окне **Выбор элементов панели инструментов**, и нажмите кнопку **ОК**.  
  
    -   Действие должно появиться на панели инструментов в категории **Компоненты "RuleSetToolkitUsageSample"**.  
  
7.  Служба "RuleSet" уже настроена на узле консольного приложения с помощью приведенного ниже оператора в файле "Program.cs".  
  
    ```  
    workflowRuntime.AddService(new RuleSetService());  
    ```  
  
8.  Можно также настроить службу на узле с помощью файла конфигурации. Дополнительные сведения см. в документации по пакету SDK.  
  
9. Файл конфигурации приложения добавляется в проект рабочего процесса, чтобы указать строку подключения для базы данных, которая будет использоваться службой.  Средством "RuleSet" должна использоваться та же строка подключения, что указывает на базу данных, содержащую таблицу набора правил.  
  
10. Теперь можно выполнить проект `RuleSetToolkitUsageSample`, а также остальные консольные приложения рабочего процесса.  Нажмите "F5" или "Ctrl\+F5" в [!INCLUDE[vsprvs](../../../../includes/vsprvs-md.md)] либо запустите непосредственно файл "RuleSetToolkitUsageSample.exe".  
  
    > [!NOTE]
    >  Чтобы повторную скомпилировать выборку использования, необходимо закрыть средство "RuleSet", поскольку средство загружает сборку выборки использования.  
  
## См. также