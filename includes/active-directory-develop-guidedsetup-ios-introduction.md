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
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 8c7f11d74d0a0b81f9f0c40871b2eaa3eb25f51f
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988225"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>從 iOS 應用程式呼叫 Microsoft Graph API

本指南說明原生 iOS 應用程式 (Swift) 如何呼叫需要來自 Microsoft Azure Active Directory (Azure AD) v2.0 端點之存取權杖的 API。 本指南說明如何取得存取權杖，並將其用於呼叫 Microsoft Graph API 和其他 API。

完成本指南中的練習之後，您的應用程式可以從具有 Azure AD 的任何公司或組織呼叫受保護的 API。 您的應用程式可以藉由使用個人帳戶 (例如 outlook.com、live.com 等等) 和公司或學校帳戶來呼叫受保護的 API。

## <a name="prerequisites"></a>必要條件

- 在此指南中建立的範例需要 XCode 版本 10.x。 您可以從 [iTunes 網站](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode 下載 URL") 下載 XCode。
- 需要 [Carthage](https://github.com/Carthage/Carthage) 相依性管理員以進行套件管理。

## <a name="how-this-guide-works"></a>本指南使用方式

![本指南使用方式](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

在本指南中，範例應用程式可讓 iOS 應用程式查詢 Microsoft Graph API，或查詢可接受來自 Azure AD v2.0 端點之權杖的 Web API。 針對這個案例，系統會透過使用**授權**標頭將一個權杖新增到 HTTP 要求。 權杖取得和更新作業是由 Microsoft Authentication Library (MSAL) 處理。

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>處理權杖取得以存取受保護的 Web API

使用者驗證之後，範例應用程式會收到權杖。 權杖是用來查詢 Microsoft Graph API 或受到 Azure AD v2.0 端點保護的 Web API。

例如，Microsoft Graph 需要可允許存取特定資源的存取權杖。 需要權杖才能讀取使用者的設定檔、存取使用者的行事曆，或傳送電子郵件等等。 您的應用程式可以藉由使用 MSAL 和指定 API 範圍來要求存取權杖。 存取權杖會新增到 HTTP **授權**標頭，這是針對受保護資源進行之每個呼叫的授權標頭。

MSAL 會為您管理快取和重新整理存取權杖，因此您的應用程式不需要執行這些作業。

## <a name="libraries"></a>程式庫

本指南使用下列程式庫：

|程式庫|說明|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|適用於 iOS 的 Microsoft Authentication Library 預覽|
