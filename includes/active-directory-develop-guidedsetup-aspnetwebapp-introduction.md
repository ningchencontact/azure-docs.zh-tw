---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: bc439cbe5e690077213b5d7953a4e74488988c3b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988503"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>將「使用 Microsoft 登入」新增至 ASP.NET Web 應用程式

本指南示範如何使用 ASP.NET MVC 方案實作「使用 Microsoft 登入」，解決方案中包含使用 OpenID Connect 的傳統網頁瀏覽器型應用程式。

在本指南最後，您的應用程式將能夠接受使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及來自已經整合 Azure Active Directory 之公司或組織的公司與學校帳戶登入。

> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。  沒有嗎？  [免費下載 Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![本指南使用方式](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

依循本指南建立的範例應用程式，是以使用者使用瀏覽器存取 ASP.NET 網站，並要求使用者透過登入按鈕進行驗證的案例為基礎。 在這個案例中，大部分轉譯網頁的工作會在伺服器端執行。

## <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|可讓應用程式使用 OpenIdConnect 進行驗證的中介軟體|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|可讓應用程式使用 Cookie 維持使用者工作階段的中介軟體|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|可讓 OWIN 型應用程式使用 ASP.NET 要求管線在 IIS 上執行|
