---
title: "Команда dotnet run — CLI .NET Core"
description: "Команда dotnet run — это удобное средство для запуска приложения из исходного кода."
author: mairaw
ms.author: mairaw
ms.date: 08/14/2017
ms.topic: article
ms.prod: .net-core
ms.technology: dotnet-cli
ms.translationtype: HT
ms.sourcegitcommit: b37d1d7ff75aebfcdf3e849931a5d2b3924d5d7a
ms.openlocfilehash: c98a69ced3c309da0ff035efb5c76e7034d54e79
ms.contentlocale: ru-ru
ms.lasthandoff: 09/06/2017

---
# <a name="dotnet-run"></a>dotnet run

[!INCLUDE [topic-appliesto-net-core-all](../../../includes/topic-appliesto-net-core-all.md)]

## <a name="name"></a>Имя

`dotnet run` — выполняет исходный код без дополнительных явных команд компиляции или запуска.

## <a name="synopsis"></a>Краткий обзор

# <a name="net-core-2xtabnetcore2x"></a>[.NET Core 2.x](#tab/netcore2x)

```
dotnet run [-c|--configuration] [-f|--framework] [--force] [--launch-profile] [--no-build] [--no-dependencies] [--no-launch-profile] [--no-restore] [-p|--project] [--runtime] [[--] [application arguments]]
dotnet run [-h|--help]
```

# <a name="net-core-1xtabnetcore1x"></a>[.NET Core 1.x](#tab/netcore1x)

```
dotnet run [-c|--configuration] [-f|--framework] [-p|--project] [[--] [application arguments]]
dotnet run [-h|--help]
```

---

## <a name="description"></a>Описание

`dotnet run` — это удобное средство для запуска приложения из исходного кода одной командой. Это полезно для быстрой последовательной разработки из командной строки. В отношении сборки кода эта команда зависима от команды [`dotnet build`](dotnet-build.md). Любые требования к сборке, например, то, что проект сначала нужно восстановить, применяются и к `dotnet run`. 

Выходные файлы записываются в расположение по умолчанию, которым является `bin/<configuration>/<target>`. Например, если у вас есть приложение `netcoreapp1.0` и вы запускаете `dotnet run`, выходные данные помещаются в `bin/Debug/netcoreapp1.0`. При необходимости файлы перезаписываются. Временные файлы помещаются в каталог `obj`. 

Когда в проекте задано несколько платформ, выполнение `dotnet run` приводит к ошибке, если только для указания платформы не используется параметр `-f|--framework <FRAMEWORK>`.

Команда `dotnet run` используется в контексте проектов, а не созданных сборок. Если вместо этого вы пытаетесь запустить библиотеку DLL платформозависимого приложения, следует использовать [dotnet](dotnet.md) без команды. Например, для выполнения `myapp.dll` используйте:

```
dotnet myapp.dll
```

Дополнительные сведения о драйвере `dotnet` см. в разделе [Средства интерфейса командной строки (CLI) .NET Core](index.md).

Для запуска приложения команда `dotnet run` разрешает зависимости приложения, выходящие за пределы общей среды выполнения, из кэша NuGet. Из-за использования кэшированных зависимостей не рекомендуется применять команду `dotnet run` для запуска приложений в рабочей среде. Вместо этого [создайте развертывание](../deploying/index.md) с помощью команды [`dotnet publish`](dotnet-publish.md) и разверните опубликованные выходные данные.

## <a name="options"></a>Параметры

# <a name="net-core-2xtabnetcore2x"></a>[.NET Core 2.x](#tab/netcore2x)

`--`

Отделяет аргументы, предназначенные для `dotnet run`, от аргументов для выполняемого приложения. Все аргументы после этого передаются выполняемому приложению.

`-c|--configuration {Debug|Release}`

Определяет конфигурацию сборки. Значение по умолчанию — `Debug`.

`-f|--framework <FRAMEWORK>`

Выполняет сборку и запуск приложения с использованием указанной [платформы](../../standard/frameworks.md). Эта платформа должна быть указана в файле проекта.

`-h|--help`

Выводит краткую справку по команде.

`--launch-profile <NAME>`

Имя профиля запуска (при его наличии), который следует использовать при запуске приложения. Профили запуска обычно определяются в файле *launchSettings.json* и, как правило, называются `Development`, `Staging` и `Production`. Дополнительные сведения см. в разделе [Работа с несколькими средами](/aspnet/core/fundamentals/environments).

`--no-build`

Не выполняет сборку проекта перед запуском.

`--no-launch-profile`

Не пытается использовать файл *launchSettings.json* для настройки приложения.

`--no-restore`

Не выполняет неявное восстановление при выполнении команды.

`-p|--project <PATH>`

Задает путь к запускаемому файлу проекта (имя папки или полный путь). Если значение не задано, по умолчанию используется текущий каталог.

`--runtime <RUNTIME_IDENTIFIER>`

Задает целевую среду выполнения для восстановления пакетов. Список идентификаторов сред выполнения (RID) см. в [каталоге RID](../rid-catalog.md).

# <a name="net-core-1xtabnetcore1x"></a>[.NET Core 1.x](#tab/netcore1x)

`--`

Отделяет аргументы, предназначенные для `dotnet run`, от аргументов для выполняемого приложения. Все аргументы после этого передаются выполняемому приложению.

`-c|--configuration {Debug|Release}`

Определяет конфигурацию сборки. Значение по умолчанию — `Debug`.

`-f|--framework <FRAMEWORK>`

Выполняет сборку и запуск приложения с использованием указанной [платформы](../../standard/frameworks.md). Эта платформа должна быть указана в файле проекта.

`-h|--help`

Выводит краткую справку по команде.

`-p|--project <PATH/PROJECT.csproj>`

Указывает путь и имя файла проекта. (См. примечание.) Если значение не задано, по умолчанию используется текущий каталог.

> [!NOTE]
> Используйте путь и имя файла проекта с параметром `-p|--project`. Регрессия в интерфейсе командной строки не позволяет указать путь к папке в пакете SDK для .NET Core 1.x. Дополнительные сведения об этой проблеме см. в разделе [dotnet run -p, не удается запустить проект (dotnet/cli #5992)](https://github.com/dotnet/cli/issues/5992).

---

## <a name="examples"></a>Примеры

Выполнение проекта в текущем каталоге:

`dotnet run`

Выполнение указанного проекта:

`dotnet run --project /projects/proj1/proj1.csproj`

Выполнение проекта в текущем каталоге (аргумент `--help` в этом примере передается приложению, так как используется аргумент `--`):

`dotnet run --configuration Release -- --help`

