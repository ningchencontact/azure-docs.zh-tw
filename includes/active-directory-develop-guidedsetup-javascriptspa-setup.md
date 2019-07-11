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
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133312"
---
## <a name="set-up-your-web-server-or-project"></a>設定您的網頁伺服器或專案

> 想要改為下載此範例的專案嗎？ 執行下列其中一個動作：
> 
> - 若要搭配本機網頁伺服器 (例如 Node.js) 來執行專案，請[下載專案檔](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。
>
> - (選擇性) 若要搭配 IIS 伺服器來執行專案，請[下載 Visual Studio 專案](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。
>
> 然後，若要在您執行之前設定程式碼範例，請跳至[設定步驟](#register-your-application)。

## <a name="prerequisites"></a>必要條件

* 若要執行本教學課程，您需要本機網頁伺服器，例如 [Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core) 或與 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 的 IIS Express 整合。

* 如果您使用 Node.js 來執行專案，請安裝整合式開發環境 (IDE)，例如 [Visual Studio Code](https://code.visualstudio.com/download)，以編輯專案檔。

* 本指南中的指示是以 Node.js 和 Visual Studio 2017 為基礎，但是您可以使用任何其他開發環境或網頁伺服器。

## <a name="create-your-project"></a>建立專案

> ### <a name="option-1-nodejs-or-other-web-servers"></a>選項 1：Node.js 或其他網頁伺服器
> 請確定您已安裝 [Node.js](https://nodejs.org/en/download/)，然後執行下列作業：
> - 建立資料夾來裝載您的應用程式。
>
> ### <a name="option-2-visual-studio"></a>選項 2：Visual Studio
> 如果您使用 Visual Studio 並且建立新專案，請執行下列作業：
> 1. 在 Visual Studio 中，選取 [檔案]   >  [新增]   >  [專案]  。
> 1. 在 [Visual C#\Web]  底下，選取 [ASP.NET Web 應用程式 (.NET Framework)]  。
> 1. 輸入應用程式的名稱，然後選取 [確定]  。
> 1. 在 [新增 ASP.NET Web 應用程式]  底下，選取 [空白]  。

## <a name="create-the-spa-ui"></a>建立 SPA UI
1. 為您的 JavaScript SPA 建立 *index.html* 檔案。 如果您使用 Visual Studio，請選取專案 (專案根資料夾)，以滑鼠右鍵按一下並選取 [新增]   >  [新增項目]   >  [HTML 頁面]  ，然後將檔案命名為 index.html  。

1. 在 index.html  檔案中，新增下列程式碼：

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
   > 您可以使用 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 下的最新發行版本取代上述指令碼中的 MSAL.js 版本。
