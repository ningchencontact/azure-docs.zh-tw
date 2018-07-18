---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 23b7ca44b72b8840579f369954f41f554d4c8852
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943414"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>從 Android 應用程式登入使用者並呼叫 Microsoft Graph API

本指南示範原生 Android 應用程式如何取得存取權杖，以及如何呼叫 Microsoft Graph API 或需要來自 Azure Active Directory v2 端點之存取權杖的其他 API。

完成本指南後，您的應用程式就能夠接受使用個人帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及來自採用 Azure Active Directory 之公司或組織的公司與學校帳戶登入。 應用程式會接著呼叫受 Azure Active Directory v2 端點保護的 API。  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南產生之範例應用程式的運作方式
![這個範例的運作方式](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

您使用本指南建立的應用程式範例是以下列案例為基礎：Android 應用程式用來查詢接受來自 Azure Active Directory v2 端點之權杖的 Web API (在此例中為 Microsoft Graph API)。 在這個案例中，您的應用程式會透過授權標頭將所需的權杖新增到 HTTP 要求。 Microsoft Authentication Library (MSAL) 會為您處理權杖取得和更新作業。

## <a name="prerequisites"></a>先決條件
* 本引導式設定著重於 Android Studio，但也可以接受任何其他 Android 應用程式開發環境。 
* 需要 Android SDK 21 或更新版本 (建議使用 SDK 25)。
* 適用於 Android 的這個 MSAL 版本需要 Google Chrome 或使用「自訂索引標籤」的網頁瀏覽器。

> [!NOTE]
> Visual Studio 的 Android 模擬器不包含 Google Chrome。 建議您在使用 API 25 的模擬器，或使用 API 21 或更新版本且已經安裝 Google Chrome 的映像中，測試此程式碼。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者經過驗證之後，應用程式範例就會收到一個存取權杖，其可用來查詢 Microsoft Graph API 或由 Azure Active Directory v2 保護的 Web API。

例如，Microsoft Graph 需要可允許存取特定資源的存取權杖。 例如，需要存取權杖才能讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件。 您的應用程式可以使用 MSAL 要求存取權杖，以透過指定 API 範圍來存取這些資源。 此存取權杖接著會新增到 HTTP 授權標頭，這是針對受保護資源進行之每個呼叫的授權標頭。 

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。

## <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[com.microsoft.identity.client (英文)](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
