---
title: "Команда dotnet nuget-delete — CLI .NET Core"
description: "Команда dotnet-nuget-delete удаляет пакет с сервера или из списка."
author: karann-msft
ms.author: mairaw
ms.date: 08/14/2017
ms.topic: article
ms.prod: .net-core
ms.technology: dotnet-cli
ms.translationtype: HT
ms.sourcegitcommit: a19ab54a6cc44bd7acd1e40a4ca94da52bf14297
ms.openlocfilehash: 65fe52f07ed823b4f7518c5b1f2da1f7a61b0371
ms.contentlocale: ru-ru
ms.lasthandoff: 08/14/2017

---
# <a name="dotnet-nuget-delete"></a>dotnet nuget delete

[!INCLUDE [topic-appliesto-net-core-all](../../../includes/topic-appliesto-net-core-all.md)]

## <a name="name"></a>Имя

Команда `dotnet nuget delete` удаляет пакет с сервера или из списка.

## <a name="synopsis"></a>Краткий обзор

`dotnet nuget delete [<PACKAGE_NAME> <PACKAGE_VERSION>] [-s|--source] [--non-interactive] [-k|--api-key] [--force-english-output] [-h|--help]`

## <a name="description"></a>Описание

Команда `dotnet nuget delete` удаляет пакет с сервера или из списка. Для [nuget.org](https://www.nuget.org/) команда удаляет пакет из списка.

## <a name="arguments"></a>Аргументы

`PACKAGE_NAME`

Удаляемый пакет.

`PACKAGE_VERSION`

Версия удаляемого пакета.

## <a name="options"></a>Параметры

`-h|--help`

Выводит краткую справку по команде.

`-s|--source <SOURCE>`

Определяет URL-адрес сервера. Поддерживаемые URL-адреса для nuget.org: `http://www.nuget.org`, `http://www.nuget.org/api/v3` и `http://www.nuget.org/api/v2/package`. Для частных каналов замените имя узла (например, `%hostname%/api/v3`).

`--non-interactive`

Не запрашивает у пользователя данные или подтверждения.

`-k|--api-key <API_KEY>`

Ключ API для сервера.

`--force-english-output`

Принудительно отображает выходные данные командной строки на английском языке.

## <a name="examples"></a>Примеры

Удаляет пакет `Microsoft.AspNetCore.Mvc` версии 1.0:

`dotnet nuget delete Microsoft.AspNetCore.Mvc 1.0` 

Удаляет пакет `Microsoft.AspNetCore.Mvc` версии 1.0, не запрашивая учетные данные или другие входные данные, предоставляемые пользователем:

`dotnet nuget delete Microsoft.AspNetCore.Mvc 1.0 --non-interactive`
