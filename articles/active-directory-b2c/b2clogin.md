---
title: 使用 b2clogin.com| Microsoft Docs
description: 了解如何使用 b2clogin.com，而不使用 login.microsoftonline.com。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660416"
---
#<a name="using-b2clogincom"></a>使用 b2clogin.com

>[!IMPORTANT]
>這項功能處於公開預覽狀態 
>

您現在可以選擇搭配使用 Azure AD B2C 服務與 `<YourTenantName>.b2clogin.com`，而不使用 `login.microsoftonline.com`。  這有許多優點：
* 您將不會再與其他 Microsoft 產品共用相同的 Cookie 標頭大小限制
* 您可以在 URL 中移除所有對 Microsoft 的參考 (您可以將 `<YourTenantName>.onmicrosoft.com` 取代為您的租用戶識別碼)

 若要使用 b2clogin.com，您必須以下部分設定：

1. 對於您的**立即執行端點**，請確定您使用的是 `<YourTenantName>.b2clogin.com`，而不是 `login.microsoftonline.com`。
2. 如果您使用 MSAL，則必須設定 `validateauthority=false`。  這是因為權杖簽發者會成為 `<YourTenantName>.b2clogin.com`。