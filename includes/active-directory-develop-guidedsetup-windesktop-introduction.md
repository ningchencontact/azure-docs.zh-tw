---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121626"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>從 Windows 傳統型應用程式呼叫 Microsoft 圖形 API

本指南示範原生 Windows Desktop .NET (XAML) 應用程式如何取得存取權杖，以及如何呼叫 Microsoft Graph API，或呼叫需要從適用於開發人員的 Microsoft 身分識別平台 (先前稱為 Azure AD) v2.0 端點取得存取權杖的其他 API。

當您完成本指南之後，您的應用程式就可以呼叫受保護的 API (使用個人帳戶，包括 outlook.com、live.com 等等)。 應用程式也可以使用任何公司或組織 (使用 Azure Active Directory) 的公司和學校帳戶。  

> [!NOTE]
> 本指南需要 Visual Studio 2015 Update 3 或 Visual Studio 2017。 沒有上述任一版本嗎？ [免費下載 Visual Studio 2017](https://www.visualstudio.com/downloads/)。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式

![示範本教學課程所產生的應用程式範例如何運作](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

本指南建立的應用程式範例，可讓 Windows 傳統型應用程式查詢 Microsoft Graph API，或查詢從 Microsoft 身分識別平台端點接受權杖的 Web API。 針對這個案例，您會透過授權標頭將一個權杖新增到 HTTP 要求。 Microsoft Authentication Library (MSAL) 會處理權杖取得和更新作業。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者經過驗證之後，應用程式範例就會收到一個權杖，其可用來查詢 Microsoft Graph API 或由開發人員所用 Microsoft 身分識別平台保護的 Web API。

例如，Microsoft Graph 需要可允許存取特定資源的權杖。 例如，需要權杖才能讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以使用 MSAL 要求存取權杖，以透過指定 API 範圍來存取這些資源。 此存取權杖接著會新增到 HTTP 授權標頭，這是針對受保護資源進行之每個呼叫的授權標頭。

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。

## <a name="nuget-packages"></a>NuGet 套件

本指南會使用以下 NuGet 套件：

|程式庫|說明|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|
