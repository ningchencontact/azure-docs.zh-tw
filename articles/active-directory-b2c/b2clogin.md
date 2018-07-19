---
title: 使用 b2clogin.com | Microsoft Docs
description: 了解如何使用 b2clogin.com，而不使用 login.microsoftonline.com。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1d42d9a97244eeff501b9d02b0f143d6ef0c91b2
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440624"
---
# <a name="using-b2clogincom"></a>使用 b2clogin.com

>[!IMPORTANT]
>這項功能處於公開預覽狀態 
>

您現在可以選擇搭配使用 Azure AD B2C 服務與 `<YourTenantName>.b2clogin.com`，而不使用 `login.microsoftonline.com`。  這有許多優點：
* 您不會再與其他 Microsoft 產品共用相同的 Cookie 標頭大小限制。
* 您可以在 URL 中移除所有對 Microsoft 的參考 (您可以將 `<YourTenantName>.onmicrosoft.com` 取代為您的租用戶識別碼)。 例如： `https://<tenantname>.b2clogin.com/tfp/<tenantname>/<policyname>/v2.0/.well-known/openid-configuration` 。

 若要使用 b2clogin.com，您必須以下部分設定：

1. 對於您的**立即執行端點**，請確定您使用的是 `<YourTenantName>.b2clogin.com`，而不是 `login.microsoftonline.com`。
2. 如果您使用 MSAL，則必須設定 `validateauthority=false`。  這是因為權杖簽發者會成為 `<YourTenantName>.b2clogin.com`。