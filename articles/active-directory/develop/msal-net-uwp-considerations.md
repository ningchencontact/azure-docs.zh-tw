---
title: 通用 Windows 平台考量 (Microsoft Authentication Library for.NET) |Azure
description: 使用 Microsoft Authentication Library for.NET (MSAL.NET) 使用通用 Windows 平台時，請了解特定的考量。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb8076df5482c91942349e0a052794f3fe945a5f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076506"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>使用 MSAL.NET 的通用 Windows 平台專屬考量
在 Xamarin iOS 上有使用 MSAL.NET 時，您必須考慮到幾項考量。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 屬性
在 WinRT 平台中`PublicClientApplication`具有下列布林值屬性``UseCorporateNetwork``。 這個屬性允許 Win8.1 和 UWP 應用程式可受益於整合式 Windows 驗證 （並因此與使用者的 SSO 登入作業系統） 的使用者是否登入的帳戶在同盟 Azure AD 租用戶。 這會運用 WAB （Web 驗證代理人）。 

> [!IMPORTANT]
> 此屬性設定為 true 時，假設應用程式開發人員，具有應用程式中啟用整合式 Windows 驗證 (IWA)。 這個項目：
> - 在  ``Package.appxmanifest`` UWP 應用程式中**功能**索引標籤上，啟用下列功能：
>   - 企業驗證
>   - 私人網路 (用戶端和伺服器)
>   - 共用的使用者憑證

因為要求的企業驗證] 或 [共用使用者憑證的功能的應用程式需要較高層級的驗證，以接受 [Windows 存放區]，預設未啟用 IWA，而且並非所有開發人員可能想要執行較高驗證層級。 

在 UWP 平台 (WAB) 上的基礎實作無法正常運作在企業案例中已啟用條件式存取的位置。 徵兆是，使用者會嘗試使用 Windows hello 登入，並建議選擇未找到憑證，但是 pin 的憑證，或在使用者選擇，但永遠不會收到 Pin 提示。 因應措施為使用替代方法 (使用者名稱/密碼 + 電話驗證)，但並不好的經驗。 

## <a name="next-steps"></a>後續步驟
在下列範例中提供更多詳細資料：

範例 | 平台 | 描述 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 使用 msal.net，存取 Microsoft Graph 進行使用者驗證與 Azure AD v2.0 端點的通用 Windows 平台用戶端應用程式。 <br>![拓撲](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 展示如何使用 MSAL 來驗證 MSA 與 Azure AD 透過 AAD v2.0 端點，並存取 Microsoft Graph 與產生的語彙基元的簡單 Xamarin Forms 應用程式。 <br>![拓撲](media/msal-net-uwp-considerations/topology-xamarin-native.png)|