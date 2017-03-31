---
title: "Консольное приложение"
description: "Это руководство раскроет для вас некоторые возможности .NET Core и языка C#."
keywords: .NET, .NET Core
author: BillWagner
ms.author: wiwagn
ms.date: 03/06/2017
ms.topic: article
ms.prod: .net-core
ms.technology: devlang-csharp
ms.devlang: csharp
ms.assetid: 883cd93d-50ce-4144-b7c9-2df28d9c11a0
translationtype: Human Translation
ms.sourcegitcommit: a06bd2a17f1d6c7308fa6337c866c1ca2e7281c0
ms.openlocfilehash: 41e8976e7b133380687a65265fd5ebe9a810a4ff
ms.lasthandoff: 03/13/2017

---

# <a name="console-application"></a>Консольное приложение

## <a name="introduction"></a>Вступление
Это руководство раскроет для вас некоторые возможности .NET Core и языка C#. Вы познакомитесь со следующими аспектами:
*    работа с интерфейсом командной строки (CLI) в .NET Core;
*    структура консольного приложения C#;
*    консольный ввод-вывод;
*    основные сведения об интерфейсах API файлового ввода-вывода в .NET Core;
*    основные сведениях о модели асинхронного программирования задач в .NET Core.

Вам предстоит создать приложение, которое считывает текстовый файл и выводит его содержимое в консоль. Вывод в консоль будет осуществляться с такой скоростью, которая позволяет читать текст вслух. Скорость можно будет увеличивать или уменьшать клавишами "<" и ">".

В этом руководстве описано множество функций. Давайте начнем поочередно разбирать их. 
## <a name="prerequisites"></a>Предварительные требования
Компьютер должен быть настроен для выполнения .NET Core. Инструкции по установке см. на странице [.NET Core](https://www.microsoft.com/net/core). Это приложение можно запустить в ОС Windows, Linux, macOS или в контейнере Docker. Вам потребуется редактор кода, но вы можете выбрать любой привычный для вас. 
## <a name="create-the-application"></a>Создание приложения
Первым шагом является создание нового приложения. Откройте командную строку и создайте новый каталог для приложения. Перейдите в этот каталог. В командной строке введите команду `dotnet new console`. Она создает начальный набор файлов для базового приложения Hello World.

Прежде чем вносить изменения в это приложение, давайте разберем процедуру его запуска. Когда вы создадите приложение, наберите в командной строке команду `dotnet restore`. Она запускает процесс восстановления из пакета NuGet. NuGet представляет собой диспетчер пакетов для .NET. Эта команда загружает все отсутствующие зависимости для проекта. Поскольку мы имеем дело с новым проектом, для него пока не существует зависимостей, поэтому при первом запуске будет загружена только платформа .NET Core. После этого первого запуска команду `dotnet restore` нужно будет запускать только после добавления новых зависимых пакетов или при обновлении версий используемых зависимостей. Этот процесс также создает файл блокировки проекта (project.lock.json) в каталоге проекта. Этот файл помогает управлять зависимостями проекта. Он указывает локальное расположение всех зависимостей проекта. Этот файл не обязательно помещать в систему управления версиями. Он будет автоматически создан при выполнении команды `dotnet restore`. 

Когда завершится восстановление пакетов, запустите `dotnet build`. Эта команда запускает подсистему сборки и создает исполняемый файл приложения. Теперь можно выполнить команду `dotnet run`, которая запустит ваше приложение.  

Весь код простого приложения Hello World размещается в файле Program.cs. Откройте этот файл в любом текстовом редакторе. Сейчас мы внесем в него первые изменения.
В верхней части файла вы видите инструкцию using:

```csharp
using System;
```

Эта инструкция указывает компилятору, что в области действия находятся все типы из пространства имен `System`. Как и другие объектно ориентированные языки, с которыми вы могли работать ранее, C# использует пространства имен для организации типов. В нашей программе Hello World все точно так же. Как вы видите, программа заключена в пространство имен `ConsoleApplication`. Это не очень понятное имя, давайте изменим его на `TeleprompterConsole`:

```csharp
namespace TeleprompterConsole
```

## <a name="reading-and-echoing-the-file"></a>Чтение и вывод файла
Первая функция, которую мы добавим, это чтение данных из текстового файла и вывод полученного текста в консоль. Сначала нам нужно добавить текстовый файл. Скопируйте в каталог проекта файл [sampleQuotes.txt](https://raw.githubusercontent.com/dotnet/docs/master/samples/csharp/getting-started/console-teleprompter/sampleQuotes.txt) из репозитория GitHub для этого [примера](https://github.com/dotnet/docs/tree/master/samples/csharp/getting-started/console-teleprompter). Он будет источником текста для вашего приложения.

Теперь добавьте в класс Program (он расположен сразу за методом `Main`) следующий метод:

```csharp
static IEnumerable<string> ReadFrom(string file)
{
    string line;
    using (var reader = File.OpenText(file))
    {
        while ((line = reader.ReadLine()) != null)
        {
            yield return line;
        }
    }
}
```

Этот метод использует типы из двух новых пространств имен. Чтобы такая программа успешно скомпилировалась, нужно добавить в начало файла следующие две строки:

```csharp
using System.Collections.Generic;
using System.IO;
```

Интерфейс `IEnumerable<T>` определен в пространстве имен `System.Collections.Generic`. Класс @System.IO.File определен в пространстве имен `System.IO`.

Этот метод является специализированным подтипом стандартного *метода перечислителя* C#. Метод перечислителя возвращает последовательности, для которых применяется отложенное вычисление. Это означает, что каждый элемент в последовательности создается только в тот момент, когда к нему выполняется обращение в коде обработки последовательности. Методы перечислителя содержат одну или несколько инструкций `yield return`. Возвращаемый методом `ReadFrom` объект содержит код для создания каждого элемента последовательности. В нашем примере он читает следующую строку текста из исходного файла и возвращает эту строку. Каждый раз, когда вызывающий код запрашивает следующий элемент из последовательности, код считывает из файла и возвращает следующую строку текста. Когда файл закончится, последовательность сообщает, что в ней больше нет элементов.

Здесь используются еще два элемента синтаксиса C#, которые могут быть для вас новыми. Инструкция `using` в этом методе управляет освобождением ресурсов. Переменная, которая инициализируется в инструкции `using` (в нашем примере это `reader`) должна реализовывать интерфейс `IDisposable`. Интерфейс @System.IDisposable определяет единственный метод (`Dispose`), который вызывается для освобождения ресурса. Компилятор создает такой вызов, когда выполнение кода достигает закрывающей скобки инструкции `using`. Созданный компилятором код гарантирует освобождение ресурса даже в том случае, если в блоке кода, определенном инструкцией using, будет создано исключение.

Переменная `reader` определена с ключевым словом `var`. Ключевое слово `var` определяет *неявно типизированную локальную переменную*. Это означает, что тип переменной определяется во время компиляции по типу объекта, присвоенного этой переменной. Здесь в переменной сохраняется объект @System.IO.StreamReader, возвращаемый методом @System.IO.File.OpenText.
 
Теперь давайте создадим в методе `Main` код для чтения файла: 

```csharp
var lines = ReadFrom("sampleQuotes.txt");
foreach (var line in lines)
{
    Console.WriteLine(line); 
}
```

Запустите программу командой `dotnet run` и убедитесь, что все текстовые строки выводятся в консоль.  

## <a name="adding-delays-and-formatting-output"></a>Добавление задержек и форматирование выходных данных
Сейчас данные отображаются слишком быстро для чтения. Поэтому нам нужно добавить задержку в процесс вывода. Для этого вы создадите несложный код, выполняющий асинхронную обработку. Но первые наши действия будут нарушать стандартные рекомендации. Эти нарушения мы укажем в комментариях при создании кода, а затем заменим этот код в последующих шагах.

В этом разделе описаны два действия. Во-первых, обновите метод итератора, чтобы он возвращал не всю строку целиком, а каждое слово отдельно. Для этого внесите такие изменения. Замените инструкцию `yield return line;` следующим кодом:

```csharp
var words = line.Split(' ');
foreach (var word in words)
{
    yield return word + " ";
}
yield return Environment.NewLine;
```

Теперь следует изменить код обработки строк файла, добавив задержку после вывода каждого слова. Замените инструкцию `Console.WriteLine(line)` в методе `Main` на такой блок кода:

```csharp
Console.Write(line);
if (!string.IsNullOrWhiteSpace(line))
{
    var pause = Task.Delay(200);
    // Synchronously waiting on a task is an
    // anti-pattern. This will get fixed in later
    // steps.
    pause.Wait();
}
```

Класс `Task` находится в пространства имен `System.Threading.Tasks`, поэтому эту инструкцию `using` нужно добавить в верхней части файла:

```csharp
using System.Threading.Tasks;
```

Запустите пример и проверьте выходные данные. Теперь слова появляются по одному и с задержками по 200 мс. Но пока с выводом сохраняются некоторые проблемы, поскольку в исходном текстовом файле есть несколько строк длиной более 80 символов, и они выводятся без перевода строки. Это не очень удобно читать с прокруткой. Но эту проблему легко исправить. Вам нужно лишь отслеживать длину каждой строки и создавать перевод строки каждый раз, когда эта длина достигает определенного порога. После объявления `words` объявите локальную переменную для хранения длины строки.

```csharp
var lineLength = 0;
```
 
Теперь добавьте следующий код после инструкции `yield return word + " ";` (перед закрывающей фигурной скобкой):

```csharp
lineLength += word.Length + 1;
if (lineLength > 70)
{
    yield return Environment.NewLine;
    lineLength = 0;
}
```
 
Запустите пример, и теперь вы сможете читать текст вслух в заданном темпе.

## <a name="async-tasks"></a>Асинхронные задачи
И на последнем этапе мы добавим код, который будет выполнять две асинхронные задачи, одна из которых осуществляет вывод текста, а вторая ожидает ввод от пользователя для ускорения или замедления вывода текста. Этот этап разделяется на несколько шагов, по завершении которых вы получите все необходимые обновления.
Первым шагом является создание асинхронной задачи (@System.Threading.Tasks.Task), которая возвращает метод с тем кодом, который вы создали ранее для чтения и отображения файла.

Добавьте следующий метод в класс `Program`. Этот текст основан на тексте метода `Main`:

```csharp
private static async Task ShowTeleprompter()
{
    var words = ReadFrom("sampleQuotes.txt");
    foreach (var line in words)
    {
        Console.Write(line);
        if (!string.IsNullOrWhiteSpace(line))
        {
            await Task.Delay(200);
        }
    }
}
```

Но вы можете заметить два изменения. Во-первых, в тексте нет вызова @System.Threading.Tasks.Task.Wait, который в синхронном режиме ожидает завершения задачи. Вместо него в этой версии используется ключевое слово `await`. Чтобы это работало, в сигнатуру метода нужно добавить модификатор `async`. Этот метод возвращает `Task`. Обратите внимание, что здесь нет инструкции для возвращения объекта `Task`. Вместо этого объект `Task` создается в коде, который компилятор предоставляет в точке использования оператора `await`. Представьте, что метод завершает выполнение при достижении `await`. Он возвращает `Task` в знак того, что работа еще не завершена.
Метод возобновит свою работу, когда завершится ожидаемая задача. Когда работа метода завершится, это будет отражено в возвращаемом объекте `Task`.
Вызывающий код может отслеживать состояние полученного `Task`, чтобы определить момент завершения метода.

Теперь наш новый метод можно вызвать из метода `Main`:

```csharp
ShowTeleprompter().Wait();
```

Здесь, в методе `Main`, код синхронно ожидает завершения. Всегда, когда это возможно, следует использовать оператор `await` вместо синхронного ожидания. Но в методе `Main` консольного приложения запрещено использовать оператор `await`. В противном случае приложение завершит работу раньше, чем выполнит все свои задачи.

Теперь следует создать второй асинхронный метод, который будет читать ввод из консоли и реагировать на клавиши "<" и ">". Для выполнения этой задачи добавьте такой метод:

```csharp
private static async Task GetInput()
{
    var delay = 200;
    Action work = () =>
    {
        do {
            var key = Console.ReadKey(true);
            if (key.KeyChar == '>')
            {
                delay -= 10;
            }
            else if (key.KeyChar == '<')
            {
                delay += 10;
            }
        } while (true);
    };
    await Task.Run(work);
}
```

Здесь создается лямбда-выражение, представляющее делегат @System.Action, который считывает нажатие клавиши из консоли и изменяет локальную переменную с длительностью задержки в том случае, если пользователь нажал клавишу "<" или ">". Этот метод использует метод @System.Console.ReadKey, чтобы блокировать выполнение и ожидать нажатия клавиши.

Чтобы завершить создание этой функции, нам нужна новая инструкция `async Task`, которая вернет метод, запускающий обе задачи (`GetInput` и `ShowTeleprompter`) и управляющий обменом данными между этими задачами.
 
Пришло время создать класс, который может обрабатывать совместное использование данных двумя задачами. Этот класс содержит два открытых свойства: delay (задержка) и flag (флаг), который обозначает, что файл прочитан полностью:

```csharp
namespace TeleprompterConsole
{
    internal class TelePrompterConfig
    {
        private object lockHandle = new object();
        public int DelayInMilliseconds { get; private set; } = 200;

        public void UpdateDelay(int increment) // negative to speed up
        {
            var newDelay = Min(DelayInMilliseconds + increment, 1000);
            newDelay = Max(newDelay, 20);
            lock (lockHandle)
            {
                DelayInMilliseconds = newDelay;
            }
        }
    }
}
```

Поместите этот класс в отдельный новый файл и заключите в пространство имен `TeleprompterConsole`, как показано выше. Также следует добавить инструкцию `using static`, чтобы использовать ссылки на методы `Min` и `Max` без указания имени внешнего класса или пространства имен. Инструкция `using static` импортирует все методы из одного класса. В этом она отличается от использованной ранее инструкции `using`, которая импортирует все классы из пространства имен.

```csharp
using static System.Math;
```

Еще одна новая для вас функция языка — это инструкция `lock`. Она гарантирует, что в этом коде в любой момент может выполняться только один поток. Если любой из потоков выполняет заблокированный раздел, все остальные потоки должны ожидать, пока он не выйдет из этого раздела. Инструкция `lock` использует объект, который защищает заблокированный раздел. Этот класс соответствует стандартному принципу блокировки частного объекта в пределах класса.

Теперь вам нужно обновить методы `ShowTeleprompter` и `GetInput` для использования нового объекта `config`. И еще одна инструкция `Task`, которая возвращает метод `async`, запускающий обе задачи и завершающий работу после окончания первой задачи:

```csharp
private static async Task RunTeleprompter()
{
    var config = new TelePrompterConfig();
    var displayTask = ShowTeleprompter(config);

    var speedTask = GetInput(config);
    await Task.WhenAny(displayTask, speedTask);
}
```

Здесь мы используем вызов нового метода @System.Threading.Tasks.Task.WhenAny(System.Threading.Tasks.Task[]). Этот метод создает задачу (`Task`), которая завершается сразу, как только завершится любая из задач в списке аргументов.

Теперь вам нужно обновить методы `ShowTeleprompter` и `GetInput`, чтобы они использовали объект `config` для задержки:

```csharp
private static async Task ShowTeleprompter(TelePrompterConfig config)
{
    var words = ReadFrom("sampleQuotes.txt");
    foreach (var line in words)
    {
        Console.Write(line);
        if (!string.IsNullOrWhiteSpace(line))
        {
            await Task.Delay(config.DelayInMilliseconds);
        }
    }
    config.SetDone();
}

private static async Task GetInput(TelePrompterConfig config)
{

    Action work = () =>
    {
        do {
            var key = Console.ReadKey(true);
            if (key.KeyChar == '>')
                config.UpdateDelay(-10);
            else if (key.KeyChar == '<')
                config.UpdateDelay(10);
        } while (!config.Done);
    };
    await Task.Run(work);
}
```

Новая версия метода `ShowTeleprompter` вызывает новый метод из класса `TeleprompterConfig`. Сейчас нужно изменить метод `Main`, чтобы вместо `ShowTeleprompter` он вызывал `RunTeleprompter`:

```csharp
RunTeleprompter().Wait();
```

И наконец, добавьте в класс `TelePrompterConfig` метод `SetDone` и свойство `Done`:

```csharp
public bool Done => done;

private bool done;

public void SetDone()
{
    done = true;    
}
```

## <a name="conclusion"></a>Заключение
В этом учебнике мы продемонстрировали вам ряд функций языка C# и библиотек .NET Core, связанных с работой в консольных приложениях.
На основе полученных знаний вы сможете развивать свои представления о языке и представленных здесь классах. Вы увидели базовые примеры использования файлового и консольного ввода-вывода, асинхронного программирования на основе задач с блокировкой и без блокировки. Вы получили информацию о языке C#, структуре программ на C#, а также об интерфейсе командной строки и средствах .NET Core.
 
