---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a00bc7a05af9e329494a11f9bee444827cbebf38
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121704"
---
## <a name="setting-up-your-web-server-or-project"></a>設定您的 Web 伺服器或專案

> 想要改為下載此範例的專案嗎？
> - [下載專案檔](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)以搭配本機網頁伺服器 (例如 Node) 執行
>
> 或
> - (選擇性) [下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)以搭配 IIS 伺服器執行
>
> 然後跳至[設定步驟](#register-your-application)，以在執行之前先設定程式碼範例。

## <a name="prerequisites"></a>必要條件
需要有本機網頁伺服器 (例如 [Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core)，或與 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 的 IIS Express 整合)，才能執行此引導式設定。

如果使用 Node.js 來執行專案，請安裝 [Visual Studio Code](https://code.visualstudio.com/download) \(英文\) 之類的 IDE 來編輯專案檔。

本指南中的指示是以 Node.js 和 Visual Studio 2017 為基礎，但可以自由使用任何其他開發環境或網頁伺服器。

## <a name="create-your-project"></a>建立專案

> ### <a name="option-1-node-other-web-servers"></a>選項 1：Node/其他網頁伺服器
> 請確定您已安裝 [Node.js](https://nodejs.org/en/download/)，然後遵循下列步驟執行：
> - 建立資料夾來裝載您的應用程式。

<p><!-- -->

> ### <a name="option-2-visual-studio"></a>選項 2：Visual Studio
> 如果您使用 Visual Studio 並建立新的專案，請遵循下列步驟來建立新的 Visual Studio 解決方案：
> 1.    在 Visual Studio 中：[檔案] > [新增] > [專案]
> 2.    在 [Visual C#\Web] 底下，選取 [ASP.NET Web 應用程式 (.NET Framework)]
> 3.    輸入應用程式的名稱並選取 [確定]
> 4.    在 [新增 ASP.NET Web 應用程式] 底下，選取 [空白]

## <a name="create-your-single-page-applications-ui"></a>建立單一頁面應用程式的 UI
1. 為您的 JavaScript SPA 建立 `index.html` 檔案。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取：[加入] > [新增項目] > [HTML 頁面]  並將它命名為 index.html。

2. 將下列程式碼新增至您的頁面：
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 您可以使用 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) \(英文\) 下的最新發行版本取代上述指令碼中的 MSAL.js 版本。
