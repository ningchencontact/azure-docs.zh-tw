---
title: 什麼是 Azure Active Directory 條件式存取原則移轉？ | Microsoft Docs
description: 了解如何在 Azure 入口網站中移轉傳統原則中。
services: active-directory
keywords: 條件式存取應用程式，與 Azure AD，安全地存取公司資源，條件式存取原則的條件式存取
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/24/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25161a6317392274ccce8865f7cc0071f0ec89b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112173"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>什麼是 Azure Active Directory 條件式存取原則移轉？ 


[條件式存取](../active-directory-conditional-access-azure-portal.md)是一項功能的 Azure Active directory (Azure AD) 可讓您控制如何授權使用者存取您雲端應用程式。 雖然目的仍相同，但新的 Azure 入口網站已導入了條件式存取如何運作的重大改進。

您應該考慮移轉尚未在 Azure 入口網站中建立的原則，因為：

- 您現在可以處理之前無法處理的案例。

- 您可以合併它們以減少必須管理的原則數目。   

- 您可以管理所有的條件式存取原則，在單一中央位置。

- Azure 傳統入口網站將被淘汰。   

這篇文章說明您需要知道要將您現有的條件式存取原則移轉到新的架構。
 
## <a name="classic-policies"></a>傳統原則

在  [Azure 入口網站](https://portal.azure.com)，則[條件式存取-原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)頁面是您的條件式存取的進入點的原則。 不過，在您環境中，您可能也有條件式存取原則，您還沒有建立使用此頁面。 這些原則也稱為「傳統原則」  。 傳統原則是條件式存取原則，您已建立在：

- Azure 傳統入口網站
- Intune 傳統入口網站
- Intune 應用程式防護入口網站


在 **條件式存取**頁面上，您可以按一下來存取您的傳統原則[**傳統原則 （預覽）** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)中**管理**一節。 


![Azure Active Directory](./media/policy-migration/71.png)


**傳統原則**檢視會提供選項以便：

- 篩選傳統原則。
 
    ![Azure Active Directory](./media/policy-migration/72.png)

- 停用傳統原則。

    ![Azure Active Directory](./media/policy-migration/73.png)
   
- 檢閱傳統原則的設定 (並將其停用)。

    ![Azure Active Directory](./media/policy-migration/74.png)


如果您已停用傳統原則，再也無法還原此步驟。 這就是為什麼您可以使用 [詳細資料]  檢視來修改傳統原則中的群組成員資格。 

![Azure Active Directory](./media/policy-migration/75.png)

藉由變更所選的群組或排除特定群組，即可先測試一些測試使用者的已停用傳統原則效果，然後再針對所有包含的使用者和群組停用此原則。 



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 條件式存取原則

使用 Azure 入口網站中的條件式存取，您可以管理您在單一中央位置的所有原則。 因為實作條件式存取如何大幅變更，您應該熟悉基本的概念之前移轉傳統原則。

請參閱：

- [什麼是 Azure Active Directory 中的條件式存取](../active-directory-conditional-access-azure-portal.md)若要了解基本概念與術語。

- [Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)以取得您組織中部署條件式存取的一些指引。

- [使用 Azure Active Directory 條件式存取所需的特定應用程式 MFA](app-based-mfa.md)以熟悉 Azure 入口網站中的使用者介面。


 
## <a name="migration-considerations"></a>移轉考量

在本文中，Azure AD 條件式存取原則也稱為*新的原則*。
您的傳統原則會繼續與新原則並存運作，直到您停用或刪除它們為止。 

下列是原則彙總內容中的重要層面：

- 雖然傳統原則會繫結至特定雲端應用程式，但您可以在一個新原則中選取您所需要數量的雲端應用程式。

- 雲端應用程式的傳統原則與新原則的控制項要求滿足所有的控制項 (*AND*)。 


- 在新原則中，您可以：
 
    - 結合多個條件 (如果您的案例所需)。 

    - 選取數個授與需求作為存取控制，並將它們與邏輯 *OR* (需要其中一個選取的控制項) 或與邏輯*AND* (需要所有選取的控制項)結合。

        ![Azure Active Directory](./media/policy-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

如果您想要移轉的 **Office 365 Exchange Online** 傳統原則將 **Exchange Active Sync** 納入為用戶端應用程式條件，您可能無法將其彙總成一個新原則。 

例如，如果您想要支援所有的用戶端應用程式類型，就是這種情況。 在以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則中，您無法選取其他用戶端應用程式。

![Azure Active Directory](./media/policy-migration/64.png)

如果傳統原則包含數個條件，也不可能彙總成一個新的原則。 以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則不支援其他條件：   

![Azure Active Directory](./media/policy-migration/08.png)

如果您已設定以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則，您必須確定並未設定其他條件。 

![Azure Active Directory](./media/policy-migration/16.png)
 

Office 365 Exchange Online [以應用程式為基礎](technical-reference.md#approved-client-app-requirement)並將 **Exchange Active Sync** 納入為用戶端應用程式條件的傳統原則可允許**支援**和**不支援**[的裝置平台](technical-reference.md#device-platform-condition)。 雖然您無法在相關的新原則中設定個別裝置平台，但您可以讓支援僅限於[支援的裝置平台](technical-reference.md#device-platform-condition)。 

![Azure Active Directory](./media/policy-migration/65.png)

您可以彙總多個將 **Exchange Active Sync** 納入為用戶端應用程式條件的傳統原則，前提是您：

- 只有 **Exchange Active Sync** 作為條件 

- 已設定授與存取的幾項需求

常見的案例之一：

- 從 Azure 傳統入口網站彙總以裝置為基礎的傳統原則 
- 在 Intune 應用程式防護入口網站中彙總以應用程式為基礎的傳統原則 
 
在此情況下，您可以將傳統原則彙總成一個已選取兩項需求的新原則。

![Azure Active Directory](./media/policy-migration/62.png)



### <a name="device-platforms"></a>裝置平台

具有[以應用程式為基礎的控制項](technical-reference.md#approved-client-app-requirement)的傳統原則會將 iOS 和 Android 預先設定為[裝置平台條件](technical-reference.md#device-platform-condition)。 

在新的原則中，您必須選取想要個別支援的[裝置平台](technical-reference.md#device-platform-condition)。

![Azure Active Directory](./media/policy-migration/41.png)



 
 


## <a name="next-steps"></a>後續步驟

- 如果您想要了解如何設定條件式存取原則，請參閱[需要 MFA 的特定應用程式與 Azure Active Directory 條件式存取](app-based-mfa.md)。

- 如果您已準備好設定您的環境的條件式存取原則，請參閱[Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)。 
