---
title: ASP.NET Core SignalR 入门
author: bradygaster
description: 在本教程中，可以创建使用 ASP.NET Core SignalR 的聊天应用。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417664"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a>教程：ASP.NET Core SignalR 入门

::: moniker range=">= aspnetcore-3.0"

本教程介绍使用 SignalR 生成实时应用的基础知识。 您将学习如何：

> [!div class="checklist"]
> * 创建 Web 项目。
> * 添加 SignalR 客户端库。
> * 创建 SignalR 中心。
> * 配置项目以使用 SignalR。
> * 添加可将消息从任何客户端发送到所有连接客户端的代码。

最终将创建一个正常运行的聊天应用：

![SignalR 示例应用](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a>创建 Web 应用项目

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* 从菜单中选择“文件”>“新建项目”。

* 在“创建新项目”对话框中，选择“ASP.NET Core Web 应用程序”，然后选择“下一步”  。

* 在“配置新项目”对话框中，将项目命名为“SignalRChat”，然后选择“创建”。

* 在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择 .NET Core 和 ASP.NET Core 3.1  。 

* 选择“Web 应用程序”以创建使用 Razor Pages 的项目，然后选择“创建” 。

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。

* 运行以下命令：

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 从菜单中选择“文件”>“新建解决方案”。

* 选择“.NET Core”>“应用”>“Web 应用程序”（不要选择“Web 应用程序(Model-View-Controller)”），然后选择“下一步”  。

* 确保“目标框架”设置为 .NET Core 3.1，然后选择“下一步”  。

* 将项目命名为“SignalRChat”，然后选择“创建”。

---

## <a name="add-the-no-locsignalr-client-library"></a>添加 SignalR 客户端库

ASP.NET Core 3.1 共享框架中包含 SignalR 服务器库。 JavaScript 客户端库不会自动包含在项目中。 对于此教程，使用库管理器 (LibMan) 从 unpkg 获取客户端库。 unpkg 是一个内容分发网络 (CDN)，可分发在 npm（即 Node.js 包管理器）中找到的任何内容。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

* 在“解决方案资源管理器”中，右键单击项目，然后选择“添加”>“客户端库”  。

* 在“添加客户端库”对话框中，对于“提供程序”，选择“unpkg”。

* 对于“库”，输入 `@microsoft/signalr@latest`。

* 选择“选择特定文件”，展开“dist/browser”文件夹，然后选择“signalr.js”和“signalr.min.js”。

* 将“目标位置”设置为 wwwroot/js/signalr/，然后选择“安装”。

  ![“添加客户端库”对话框 - 选择库](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  LibMan 创建 wwwroot/js/signalr 文件夹并将所选文件复制到该文件夹。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* 在集成终端中，运行以下命令以安装 LibMan。

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 使用 LibMan 运行以下命令，以获取 SignalR 客户端库。 可能需要等待几秒钟的时间才能看到输出。

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  参数指定以下选项：
  * 使用 unpkg 提供程序。
  * 将文件复制到 wwwroot/js/signalr 目标。
  * 仅复制指定的文件。

  输出如下所示：

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在“终端”中，运行以下命令以安装 LibMan。

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* 导航到项目文件夹（包含 SignalRChat.csproj 文件的文件夹）。

* 使用 LibMan 运行以下命令，以获取 SignalR 客户端库。

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  参数指定以下选项：
  * 使用 unpkg 提供程序。
  * 将文件复制到 wwwroot/js/signalr 目标。
  * 仅复制指定的文件。

  输出如下所示：

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a>创建 SignalR 中心

*Hub* 是一个类，用作处理客户端 - 服务器通信的高级管道。

* 在 SignalRChat 项目文件夹中，创建 Hubs 文件夹。

* 在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件 ：

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  `ChatHub` 类继承自 SignalR `Hub` 类。 `Hub` 类管理连接、组和消息。

  可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。 本教程后面部分将显示调用该方法的 JavaScript 客户端代码。 SignalR 代码是异步模式，可提供最大的可伸缩性。

## <a name="configure-no-locsignalr"></a>配置 SignalR

必须将 SignalR 服务器配置为将 SignalR 请求传递给 SignalR。

* 将以下突出显示的代码添加到 Startup.cs 文件。

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入和路由系统。

## <a name="add-no-locsignalr-client-code"></a>添加 SignalR 客户端代码

* 使用以下代码替换 Pages\Index.cshtml 中的内容：

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  前面的代码：

  * 创建名称以及消息文本的文本框和“提交”按钮。
  * 使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。
  * 包含对 SignalR 的脚本引用以及在下一步中创建的“chat.js”应用程序代码。

* 在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件 ：

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  前面的代码：

  * 创建并启动连接。
  * 向“提交”按钮添加一个用于向中心发送消息的处理程序。
  * 向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。

## <a name="run-the-app"></a>运行应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 Ctrl+F5 可运行应用而不进行调试。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 在集成终端中，运行以下命令：

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 从菜单中选择“运行”>“开始执行(不调试)”。

---

* 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

* 选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮。

  两个页面上立即显示名称和消息。

  ![SignalR 示例应用](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * 如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。 可能会看到与 HTML 和 JavaScript 代码相关的错误。 例如，假设将 signalr.js 放在不同于系统指示的文件夹中。 在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。
>   ![未找到 signalr.js 错误](signalr/_static/3.x/f12-console.png)
> * 如果 Chrome 中出现 ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY 错误，请运行这些命令以更新开发证书：
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本教程介绍使用 SignalR 生成实时应用的基础知识。 您将学习如何： 

> [!div class="checklist"]  
> * 创建 Web 项目。   
> * 添加 SignalR 客户端库。   
> * 创建 SignalR 中心。 
> * 配置项目以使用 SignalR。 
> * 添加可将消息从任何客户端发送到所有连接客户端的代码。  
最后，你将拥有一个工作聊天应用：![SignalR 示例应用](signalr/_static/2.x/signalr-get-started-finished.png)   

## <a name="prerequisites"></a>先决条件    

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>创建 Web 项目 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* 从菜单中选择“文件”>“新建项目”。 

* 在“新建项目”对话框中，选择“已安装”>“Visual C#”>“Web”>“ASP.NET Core Web 应用” 。 将项目命名为“SignalRChat”。   

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/2.x/signalr-new-project-dialog.png)    

* 选择“Web 应用”，以创建使用 Razor Pages 的项目。   

* 选择“.NET Core”目标框架，选择“ASP.NET Core 2.2”，然后单击“确定”  。    

  ![Visual Studio 中的“新建项目”对话框](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* 在将要在其中创建新项目文件夹的文件夹中打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。  

* 运行以下命令：   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)   

* 从菜单中选择“文件”>“新建解决方案”。    

* 选择“.NET Core”>“应用”>“ASP.NET Core Web 应用”（请勿选择 ASP.NET Core Web 应用 (MVC)） 。  

* 选择“下一步”。  

* 将项目命名为“SignalRChat”，然后选择“创建”。 

--- 

## <a name="add-the-no-locsignalr-client-library"></a>添加 SignalR 客户端库 

`Microsoft.AspNetCore.App` 元包中包括 SignalR 服务器库。 JavaScript 客户端库不会自动包含在项目中。 对于此教程，使用库管理器 (LibMan) 从 unpkg 获取客户端库。 unpkg 是一个内容分发网络 (CDN)，可分发在 npm（即 Node.js 包管理器）中找到的任何内容。   

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* 在“解决方案资源管理器”中，右键单击项目，然后选择“添加”>“客户端库”  。  

* 在“添加客户端库”对话框中，对于“提供程序”，选择“unpkg”。 

* 对于“库”，输入 `@microsoft/signalr@3`，然后选择不是预览版的最新版本。  

  ![“添加客户端库”对话框 - 选择库](signalr/_static/2.x/libman1.png)   

* 选择“选择特定文件”，展开“dist/browser”文件夹，然后选择“signalr.js”和“signalr.min.js”。 

* 将“目标位置”设置为 wwwroot/lib/signalr/，然后选择“安装”。    

  ![“添加客户端库”对话框 - 选择文件和目标](signalr/_static/2.x/libman2.png) 

  LibMan 创建 wwwroot/lib/signalr 文件夹并将所选文件复制到该文件夹。    

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* 在集成终端中，运行以下命令以安装 LibMan。  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* 使用 LibMan 运行以下命令，以获取 SignalR 客户端库。 可能需要等待几秒钟的时间才能看到输出。 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  参数指定以下选项： 
  * 使用 unpkg 提供程序。 
  * 将文件复制到 wwwroot/lib/signalr 目标。    
  * 仅复制指定的文件。  

  输出如下所示：  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)   

* 在“终端”中，运行以下命令以安装 LibMan。 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* 导航到项目文件夹（包含 SignalRChat.csproj 文件的文件夹）。   

* 使用 LibMan 运行以下命令，以获取 SignalR 客户端库。    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  参数指定以下选项： 
  * 使用 unpkg 提供程序。 
  * 将文件复制到 wwwroot/lib/signalr 目标。    
  * 仅复制指定的文件。  

  输出如下所示：  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a>创建 SignalR 中心   

*中心* 是一个类，用作处理客户端 - 服务器通信的高级管道。   

* 在 SignalRChat 项目文件夹中，创建 Hubs 文件夹。  

* 在 Hubs 文件夹中，使用以下代码创建 ChatHub.cs 文件 ： 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  `ChatHub` 类继承自 SignalR `Hub` 类。 `Hub` 类管理连接、组和消息。  

  可通过已连接客户端调用 `SendMessage`，以向所有客户端发送消息。 本教程后面部分将显示调用该方法的 JavaScript 客户端代码。 SignalR 代码是异步模式，可提供最大的可伸缩性。    

## <a name="configure-no-locsignalr"></a>配置 SignalR  

必须将 SignalR 服务器配置为将 SignalR 请求传递给 SignalR。    

* 将以下突出显示的代码添加到 Startup.cs 文件。  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  这些更改将 SignalR 添加到 ASP.NET Core 依赖关系注入系统和中间件管道。  

## <a name="add-no-locsignalr-client-code"></a>添加 SignalR 客户端代码    

* 使用以下代码替换 Pages\Index.cshtml 中的内容：  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  前面的代码：   

  * 创建名称以及消息文本的文本框和“提交”按钮。  
  * 使用 `id="messagesList"` 创建一个列表，用于显示从 SignalR 中心接收的消息。   
  * 包含对 SignalR 的脚本引用以及在下一步中创建的“chat.js”应用程序代码。    

* 在 wwwroot/js 文件夹中，使用以下代码创建 chat.js 文件 ：  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  前面的代码：   

  * 创建并启动连接。    
  * 向“提交”按钮添加一个用于向中心发送消息的处理程序。 
  * 向连接对象添加一个用于从中心接收消息并将其添加到列表的处理程序。  

## <a name="run-the-app"></a>运行应用  

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)   

* 按 Ctrl+F5 可运行应用而不进行调试。   

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* 在集成终端中，运行以下命令：    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 从菜单中选择“运行”>“开始执行(不调试)”。

---

* 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

* 选择任一浏览器，输入名称和消息，然后选择“发送消息”按钮。  

  两个页面上立即显示名称和消息。   

  ![SignalR 示例应用](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> 如果应用不起作用，请打开浏览器开发人员工具 (F12) 并转到控制台。 可能会看到与 HTML 和 JavaScript 代码相关的错误。 例如，假设将 signalr.js 放在不同于系统指示的文件夹中。 在这种情况下，对该文件的引用将不起作用，并且你将在控制台中看到 404 错误。   
> ![未找到 signalr.js 错误](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>其他资源 
* [本教程的 YouTube 版本](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
