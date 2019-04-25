---
title: 新增原生用戶端應用程式 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何將原生用戶端應用程式新增至您的 Active Directory B2C 租用戶。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3f988e61c152be820e7e490e13908fb4ab54e6c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60314594"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>將原生用戶端應用程式新增至您的 Azure Active Directory B2C 租用戶

原生用戶端應用程式必須註冊於您的租用戶中，應用程式才能與 Azure Active Directory B2C 通訊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下頂端功能表中的 [目錄和訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式]，然後選取 [新增]。
2. 輸入應用程式的名稱。 例如，*nativeapp1*。
3. 針對 [包含 Web 應用程式/Web API]，選取 [否]。
4. 針對 [包含原生用戶端]，選取 [是]。
5. 針對 [重新導向 URI]，輸入具有自訂配置的有效重新導向 URI。 選擇重新導向 URI 時，有兩個重要考量︰

    - **唯一** - 每個應用程式的重新導向 URI 的配置都應該是唯一。 在範例 `com.onmicrosoft.contoso.appname://redirect/path`中，`com.onmicrosoft.contoso.appname` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者做出錯誤的選擇，登入便會失敗。
    - **完成** - 重新導向 URI 必須有配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//contoso/` 可正常運作，而 `//contoso` 會失敗。 確定重新導向 URI 未包含特殊字元 (例如底線)。

6. 按一下頁面底部的 [新增] 。
7. 在 [屬性] 頁面上，記錄您在設定原生用戶端應用程式時所將使用的應用程式識別碼。
