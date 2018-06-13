---
title: 什麼是 Azure Active Directory B2B 共同作業？ | Microsoft Docs
description: Azure Active Directory B2B 共同作業讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1eff04cf43260a12a50a08b9145e1975b51af0cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928475"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>什麼是 Azure AD B2B 共同作業？

Azure Active Directory (Azure AD) 企業對企業 (B2B) 共同作業功能可讓任何使用 Azure AD 的組織 (不論規模大小)，安全地與來自任何其他組織的使用者共同作業。 這些組織不一定要具備 Azure AD，甚至不需要具有 IT 部門。

使用 Azure AD 的組織可以將文件、資源及應用程式的存取權提供給它們的合作夥伴，同時又保有其本身公司資料的完整控制權。 開發人員可以使用 Azure AD 企業對企業 API 來撰寫應用程式，以更安全的方式將兩個組織結合在一起。 還可讓使用者輕鬆地瀏覽。

下列影片提供實用的概觀。
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Azure AD B2B 共同作業的主要優勢

### <a name="work-with-any-user-from-any-partner"></a>可與來自任何合作夥伴的任何使用者共同作業

- 合作夥伴可使用他們自己的認證
- 合作夥伴不一定要使用 Azure AD
- 不需要外部目錄或複雜的設定

### <a name="simple-and-secure-collaboration"></a>簡單且安全的共同作業

- 可提供對任何公司應用程式或資料的存取權，同時又套用精密、Azure AD 提供技術的授權原則
- 方便使用者
- 企業級的應用程式與資料安全性

### <a name="no-management-overhead"></a>沒有任何額外的管理負荷

- 不需要進行任何外部帳戶或密碼管理
- 不需要進行任何同步處理或手動帳戶生命週期管理
- 沒有任何額外的外部系統管理負荷

## <a name="easily-add-b2b-collaboration-users"></a>輕鬆新增 B2B 共同作業使用者

系統管理員可以在 [Azure 入口網站](https://portal.azure.com)中將 B2B 共同作業 (來賓) 使用者輕鬆地新增到您的組織。

![新增來賓使用者](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>讓您的共同作業者使用自己的身分識別

B2B 共同作業者可以使用自己選擇的身分識別來登入。 如果使用者沒有 Microsoft 帳戶或 Azure AD 帳戶 – 系統就會在他們兌換提供項目時為他們建立一個帳戶。

### <a name="delegate-to-application-and-group-owners"></a>委派給應用程式和群組擁有者

應用程式和群組擁有者可以將 B2B 使用者直接新增到您在意的任何應用程式，不論它是否為 Microsoft 應用程式。 系統管理員可以將新增 B2B 使用者的權限委派給非系統管理員。 非系統管理員可以使用 [Azure AD 應用程式存取面板](https://myapps.microsoft.com)，將 B2B 共同作業使用者新增到應用程式或群組。

![存取面板](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![新增成員](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>授權原則可保護您的公司內容

可以強制執行條件式存取原則，例如多重要素驗證：
- 在租用戶層級
- 在應用程式層級
- 針對特定使用者來保護公司應用程式和資料

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>使用 API 和範例程式碼來輕鬆建置要上線的應用程式

讓您的外部合作夥伴以針對您組織需求量身打造的方式上線使用

使用 [B2B 共同作業邀請 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 來自訂入門訓練體驗，包括建立自助式註冊入口網站。 我們[在 Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) 提供自助式入口網站的範例程式碼。

![註冊入口網站](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

使用 Azure AD B2B 共同作業，您便可以運用 Azure AD 的完整強大功能，以使用者覺得簡單且直覺的方式來保護您的合作夥伴關係。

## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者？](active-directory-b2b-admin-add-users.md)
- [資訊工作者如何新增 B2B 共同作業使用者？](active-directory-b2b-iw-add-users.md)
- [B2B 共同作業邀請兌換](active-directory-b2b-redemption-experience.md)
- [Azure AD B2B 共同作業授權](active-directory-b2b-licensing.md)
- 一如往常，如有任何意見反應、討論及建議，請透過我們的 [Microsoft 技術社群 (英文)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)，與產品小組聯繫。