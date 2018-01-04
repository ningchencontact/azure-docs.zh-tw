---
title: "Azure 入口網站中的 Migrate 傳統原則 | Microsoft Docs"
description: "了解您需要知道要在 Azure 入口網站的傳統原則移轉。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 16628bd4fa41d2e7697e1c2501f2ccd31dbd0496
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Azure 入口網站中的 Migrate 傳統原則 


[條件式存取](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 的功能，可讓您控制授權使用者如何存取您的雲端應用程式。 目的仍然是相同，而新的 Azure 入口網站的發行版本導入了解條件式存取如何運作的重大增強功能。

您應該考慮將移轉因為時，您已不在 Azure 入口網站中建立的原則：

- 您現在可以解決無法處理之前的案例。

- 您可以合併它們以減少必須管理的原則數目。   

- 您可以管理您所有單一中央位置的條件式存取原則。

- Azure 傳統入口網站將會停用。   

這篇文章會說明您需要知道要將現有的條件式存取原則移轉到新的架構。
 
## <a name="classic-policies"></a>傳統原則

在[Azure 入口網站](https://portal.azure.com)、[條件式存取-原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)頁面是您進入點，您的條件式存取原則。 不過，在您環境中，您可能也有您不建立使用此頁面的條件式存取原則。 這些原則稱為*傳統原則*。 傳統的原則是條件式存取原則，您已建立在：

- Azure 傳統入口網站
- Intune 傳統入口網站
- Intune 應用程式保護入口網站


在**條件式存取**頁面，即可存取您的傳統原則[**傳統原則 （預覽）** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)中**管理**一節。 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


**傳統原則**檢視為您提供的選項：

- 篩選傳統原則。
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- 停用傳統的原則。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- 檢閱設定的傳統的原則 （和將其停用）。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


如果您已停用傳統的原則，您也無法再還原此步驟。 這就是為什麼您可以修改在傳統的原則使用的群組成員資格**詳細資料**檢視。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

藉由變更所選的群組或排除特定群組，您可以測試一些測試使用者的已停用傳統原則的效果，然後再停用所有包含的使用者和群組原則。 



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 條件式存取原則

您可以使用 Azure 入口網站中的條件式存取，來管理您的所有原則在單一中央位置。 因為解條件式存取的實作已經大幅變更，您應該熟悉基本概念移轉您的傳統原則之前。

請參閱：

- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)深入了解基本概念與術語。

- [Azure Active Directory 中的條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)取得部署條件式存取您組織中的一些指引。

- [開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)讓自己熟悉如何使用 Azure 入口網站中的使用者介面。


 
## <a name="migration-considerations"></a>移轉考量

在本文中，Azure AD 條件式存取原則也稱為*新原則*。
傳統原則繼續與新的原則並存運作，直到您停用或刪除它們。 

在下列方面是重要原則彙總的內容中：

- 雖然傳統的原則所繫結到特定雲端應用程式，您可以選取當您需要在新的原則中的雲端應用程式數目。

- 傳統的原則與雲端應用程式的新原則的控制項需要所有的控制項 (*AND*) 來完成。 


- 在新的原則，您可以：
 
    - 如果您的案例需要，結合多個條件。 

    - 選取數個授與需求與存取控制，並將它們結合使用邏輯*OR* （需要其中一個選取的控制項） 或邏輯*AND* （需要所有選取的控制項）。

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 的 Exchange online

如果您想要移轉的傳統原則**Office 365 Exchange online**包括**Exchange Active Sync**做為用戶端應用程式的條件，您可能無法將其合併成一個新的原則。 

看到這個憑證，例如，如果您想要支援所有的用戶端應用程式類型。 在新的原則具有**Exchange Active Sync**做為用戶端應用程式的條件，您無法選取其他用戶端應用程式。

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

彙總成一個新的原則，也不可以如果傳統原則包含數個條件。 新的原則具有**Exchange Active Sync**成用戶端應用程式設定的條件不支援其他條件：   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

如果您有新的原則具有**Exchange Active Sync**成用戶端應用程式設定的條件，您必須確定所有其他條件未設定。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

[應用程式為基礎](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)傳統 Office 365 Exchange Online 的原則包含**Exchange Active Sync**做為用戶端應用程式條件允許**支援**和**不受支援**[裝置平台](active-directory-conditional-access-technical-reference.md#device-platform-condition)。 雖然您無法設定個別的裝置平台相關的新原則中，您可以限制來支援[支援裝置平台](active-directory-conditional-access-technical-reference.md#device-platform-condition)只。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

您可以合併多個包含的傳統原則**Exchange Active Sync**做為用戶端應用程式條件有：

- 只有**Exchange Active Sync**做為條件 

- 授與存取設定的幾項需求

常見的案例之一是彙總：

- 從 Azure 傳統入口網站，以裝置為基礎的傳統原則 
- Intune 應用程式保護入口網站中，應用程式為基礎的傳統原則 
 
在此情況下，您可以將傳統原則合併成一個新的原則有兩個選取的需求。

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>裝置平台

與傳統原則[應用程式為基礎的控制項](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)會預先設定 iOS 和 Android 為[裝置平台條件](active-directory-conditional-access-technical-reference.md#device-platform-condition)。 

在新的原則中，您必須選取[裝置平台](active-directory-conditional-access-technical-reference.md#device-platform-condition)您想要個別支援。

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 
