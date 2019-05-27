---
title: 經過電子郵件驗證之使用者帳戶的自助式註冊 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory (Azure AD) 租用戶中使用自助式註冊
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d949b746f05eb440f5ae28f683dfc838217ab47
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956504"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>什麼是自助式 Azure Active Directory 註冊？

這篇文章說明如何使用來填入 Azure Active Directory (Azure AD) 中的組織的自助式註冊。 如果您想要接管網域名稱，從非受控 Azure AD 的組織，請參閱[接管非受控目錄，以系統管理員身分](domains-admin-takeover.md)。

## <a name="why-use-self-service-sign-up"></a>為何使用自助式註冊？
* 讓客戶更快取得他們想要的服務
* 建立服務的電子郵件型供應項目
* 建立電子郵件為基礎的註冊流程可快速地讓使用者建立身分識別使用其容易記住的工作電子郵件別名
* 自助建立的 Azure AD 目錄可以轉換成可用於其他服務的受控目錄

## <a name="terms-and-definitions"></a>詞彙和定義
* **自助式註冊**:這是使用者用以註冊雲端服務的方法，系統會根據其電子郵件網域在 Azure AD 中自動為其建立身分識別。
* **非受控 Azure AD 目錄**：這是建立身分識別的目錄。 非受控目錄是沒有全域管理員的目錄。
* **電子郵件驗證的使用者**：這是 Azure AD 中的使用者帳戶類型。 在註冊自助式供應項目後自動建立身分識別的使用者，就是所謂的電子郵件驗證的使用者。 電子郵件驗證的使用者是加上 creationmethod=EmailVerified 標記之目錄的一般成員。

## <a name="how-do-i-control-self-service-settings"></a>如何控制自助式設定？
系統管理員目前有兩個自助式控制項。 它們可以控制：

* 使用者是否可以透過電子郵件加入目錄
* 使用者是否可以授權自己使用應用程式和服務

### <a name="how-can-i-control-these-capabilities"></a>如何控制這些功能？
管理員可以使用下列 Azure AD Cmdlet Set-MsolCompanySettings 參數來設定這些功能：

* **AllowEmailVerifiedUsers** 控制使用者是否可以建立或加入目錄。 如果您將該參數設定為 $false，則經過電子郵件驗證的使用者都無法加入目錄。
* **AllowAdHocSubscriptions**控制使用者執行自助式註冊的能力。 如果您將該參數為 $false 時，沒有任何使用者可以執行自助式註冊。
  
AllowEmailVerifiedUsers 和 AllowAdHocSubscriptions 是可以套用至受控或非受控目錄的整個目錄的設定。 以下是範例，其中：

* 您要管理具有已驗證網域 (例如 contoso.com) 的目錄
* 您可以使用不同的目錄中的 B2B 共同作業邀請使用者還不存在 (userdoesnotexist@contoso.com) 在 contoso.com 的主目錄
* 主目錄已開啟 AllowEmailVerifiedUsers

如果上述條件都成立，則會在主目錄中建立成員使用者，並在邀請目錄中建立 B2B 來賓使用者。

Flow 和 PowerApps 試用註冊不受**AllowAdHocSubscriptions**設定。 如需詳細資訊，請參閱下列文章：

* [如何防止現有使用者開始使用 Power BI？](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [組織中 Flow 的問與答](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>這些控制項如何一起運作？
這兩個參數可以用於搭配使用，以定義更精確地控制自助式註冊。 例如，下列命令可讓使用者執行自助式註冊，但前提是這些使用者在 Azure AD 中已有帳戶 （換句話說，必須要建立電子郵件驗證帳戶的使用者第一次無法執行自助式註冊）：

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

下列流程圖說明這些參數的不同組合，以及針對目錄和自助式註冊造成的情況。

![自助式註冊控制項的流程圖](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

如需如何使用這些參數的詳細資訊和相關範，請參閱 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)。

## <a name="next-steps"></a>後續步驟

* [將自訂網域名稱新增到 Azure AD](../fundamentals/add-custom-domain.md)
* [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 參考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [關閉您的公司或學校帳戶，在非受控目錄](users-close-account.md)
