---
title: ASP.NET Core Blazor 模板
author: guardrex
description: 了解 ASP.NET Core Blazor 应用模板和 Blazor 项目结构。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: ce46d562285b95ff656ed43b3a63ca5e7315f4c8
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243208"
---
# <a name="aspnet-core-blazor-templates"></a>ASP.NET Core Blazor 模板

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型开发应用：

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。

有关基于模板创建 Blazor 应用的分步说明，请参阅 <xref:blazor/get-started>。

通过将 `--help` 选项传递给 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 命令，可提供模板选项：

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor 项目结构

以下文件和文件夹构成了基于 Blazor 模板生成的 Blazor 应用：

* `Program.cs`：应用入口点，用于设置以下各项：

  * ASP.NET Core [主机](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly 主机 (Blazor WebAssembly)：此文件中的代码对于通过 Blazor WebAssembly 模板 (`blazorwasm`) 创建的应用是唯一的。
    * 作为应用根组件的 `App` 组件被指定为 `Add` 方法的 `app` DOM 元素。
    * 可以在主机生成器上使用 `ConfigureServices` 方法配置服务（例如，`builder.Services.AddSingleton<IMyDependency, MyDependency>();`）。
    * 可以通过主机生成器提供配置 (`builder.Configuration`)。

* `Startup.cs` (Blazor Server)：包含应用的启动逻辑。 `Startup` 类定义两个方法：

  * `ConfigureServices`：配置应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务。 在 Blazor Server 应用中，通过调用 <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> 添加服务，并将 `WeatherForecastService` 添加到服务容器以供示例 `FetchData` 组件使用。
  * `Configure`：配置应用的请求处理管道：
    * 调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> 可以为与浏览器的实时连接设置终结点。 使用 [SignalR](xref:signalr/introduction) 创建连接，该框架用于向应用添加实时 Web 功能。
    * 调用 [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) 以设置应用的根页面 (`Pages/_Host.cshtml`) 并启用导航。

* `wwwroot/index.html` (Blazor WebAssembly)：实现为 HTML 页面的应用的根页面：
  * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
  * 此页面指定根 `App` 组件的呈现位置。 `App` 组件 (`App.razor`) 在 `Startup.Configure` 中被指定为 `AddComponent` 方法的 `app` DOM 元素。
  * 加载 `_framework/blazor.webassembly.js` JavaScript 文件，该文件用于：
    * 下载 .NET 运行时、应用和应用依赖项。
    * 初始化运行时以运行应用。

* `App.razor`：应用的根组件，用于使用 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件来设置客户端路由。 <xref:Microsoft.AspNetCore.Components.Routing.Router> 组件会截获浏览器导航并呈现与请求的地址匹配的页面。

* `Pages` 文件夹：包含构成 Blazor 应用的可路由组件/页面 (`.razor`) 和 Blazor Server 应用的根 Razor 页面。 每个页面的路由都是使用 [`@page`](xref:mvc/views/razor#page) 指令指定的。 该模板包括以下组件：
  * `_Host.cshtml` (Blazor Server)：实现为 Razor 页面的应用的根页面：
    * 最初请求应用的任何页面时，都会呈现此页面并在响应中返回。
    * 加载 `_framework/blazor.server.js` JavaScript 文件，该文件用于在浏览器和服务器之间建立实时 SignalR 连接。
    * 此主机页面指定根 `App` 组件 (`App.razor`) 的呈现位置。
  * `Counter` (`Pages/Counter.razor`)：实现“计数器”页面。
  * `Error`（`Error.razor`，仅限 Blazor Server 应用）：当应用中发生未经处理的异常时呈现。
  * `FetchData` (`Pages/FetchData.razor`)：实现“提取数据”页面。
  * `Index` (`Pages/Index.razor`)：实现主页。

* `Shared` 文件夹：包含应用使用的其他 UI 组件 (`.razor`)：
  * `MainLayout` (`MainLayout.razor`)：应用的布局组件。
  * `NavMenu` (`NavMenu.razor`)：实现边栏导航。 包括 [`NavLink`](xref:blazor/fundamentals/routing#navlink-component) 组件 (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>)，该组件可向其他 Razor 组件呈现导航链接。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> 组件会在系统加载其组件时自动指示选定状态，这有助于用户了解当前显示的组件。

* `_Imports.razor`：包括要包含在应用组件 (`.razor`) 中的常见 Razor 指令，如用于命名空间的 [`@using`](xref:mvc/views/razor#using) 指令。

* `Data` folder (Blazor Server)：包含 `WeatherForecast` 类和 `WeatherForecastService` 的实现，它们向应用的 `FetchData` 组件提供示例天气数据。

* `wwwroot`：应用的 [Web 根目录](xref:fundamentals/index#web-root)文件夹，其中包含应用的公共静态资产。

* `appsettings.json` (Blazor Server)：应用的配置设置。
