---
title: Azure Active Directory 條件式存取假設工具 - 預覽 | Microsoft Docs
description: 了解如何測試 Azure Active Directory 條件式存取原則的設定。
services: active-directory
keywords: 應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 2c4ca3685d15ddd2446590e1ef297362f9aa52ee
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723825"
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Azure Active Directory 條件式存取假設工具 - 預覽

[條件式存取](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 的功能，可讓您控制授權使用者存取雲端應用程式的方式。 要如何知道環境中的條件式存取原則會有什麼影響？ 若要回答這個問題，您可以使用**條件式存取假設工具**。

本文說明如何使用此工具來測試條件式存取原則。

## <a name="what-it-is"></a>內容

**條件式存取假設原則工具**可讓您了解環境中的條件式存取原則會有什麼影響。 這個工具並非要讓您以手動方式執行多個登入來測試原則，而是要讓您評估使用者的模擬登入。 該模擬可評估此登入對原則所造成的影響，並產生模擬報告。 此報告不只會列出所套用的條件式存取原則，還會列出[傳統原則](active-directory-conditional-access-migration.md#classic-policies) (如果有的話)。    

假設工具也可讓您快速判斷套用至特定使用者的原則。 例如，如果您需要針對問題進行疑難排解，就可以使用這項資訊。  

## <a name="how-it-works"></a>運作方式

在**條件式存取假設工具**中，您必須先設定您要模擬之登入案例的設定。 這些設定包括：

- 您要測試的使用者 

- 使用者會嘗試存取的雲端應用程式

- 要在什麼樣的條件下執行雲端應用程式的存取
     
在下一個步驟中，您可以起始模擬執行來評估設定。 只有已啟用的原則才會參與評估執行。


評估完成時，此工具會產生一份受影響原則的報告。


## <a name="running-the-tool"></a>執行工具

您可以在 Azure 入口網站的 [[條件式存取 - 原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)] 頁面中找到**假設**工具。

若要啟動此工具，請在原則清單頂端的工具列中按一下 [假設]。

![假設](./media/active-directory-conditional-access-whatif/01.png)

您必須先進行設定，才能執行評估。

## <a name="settings"></a>設定

本節為您提供模擬執行設定的相關資訊。

![假設](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>使用者

您只能選取一個使用者。 這是唯一的必要欄位。

### <a name="cloud-apps"></a>雲端應用程式

這項設定的預設值是 [所有雲端應用程式]。 此預設設定會對環境中的所有可用原則進行評估。 您可以將此範圍縮小至會影響特定雲端應用程式的原則。


### <a name="ip-address"></a>IP 位址

IP 位址是用來模擬[位置條件](active-directory-conditional-access-locations.md)的單一 IPv4 位址。 此位址代表使用者用來登入之裝置的網際網路對向位址。 例如，您可以瀏覽至[我的 IP 位址為何](https://whatismyipaddress.com)來確認裝置的 IP 位址。    

### <a name="device-platforms"></a>裝置平台

這項設定會模擬[裝置平台條件](active-directory-conditional-access-conditions.md#device-platforms)，並代表**所有平台 (包括不支援平台)** 的同等項目。 
### <a name="client-apps"></a>用戶端應用程式

這項設定會模擬[用戶端應用程式條件](active-directory-conditional-access-conditions.md#client-apps)。
根據預設，此設定會導致系統評估已選取 [瀏覽器] 和 (或) [行動裝置應用程式和桌面用戶端] 的所有原則。 此外，還會偵測強制執行 **Exchange ActiveSync (EAS)** 的原則。 您可以選取下列選項來縮小此設定的範圍：

- **瀏覽器**：可評估所有至少已選取 [瀏覽器] 的原則。 

- **行動裝置應用程式和桌面用戶端**：可評估所有至少已選取 [行動裝置應用程式和桌面用戶端] 的原則。 


### <a name="sign-in-risk"></a>登入風險

這項設定會模擬[登入風險條件](active-directory-conditional-access-conditions.md#sign-in-risk)。   


## <a name="evaluation"></a>評估 

按一下 [假設] 即可開始評估。 評估結果所提供的報告中會包含： 

![假設](./media/active-directory-conditional-access-whatif/03.png)

- 環境中是否存在傳統原則的指標
- 套用至使用者的原則
- 未套用至使用者的原則


如果選取的雲端應用程式中存在[傳統原則](active-directory-conditional-access-migration.md#classic-policies)，系統就會向您顯示指標。 按一下指標，系統就會將您重新導向至傳統原則頁面。 在傳統原則頁面中，您可以移轉傳統原則，也可以直接加以停用。 關閉此頁面就會回到評估結果。

在套用至所選取使用者的原則清單中，您還可以找到使用者必須符合之[授與控制項](active-directory-conditional-access-controls.md#grant-controls)和[工作階段](active-directory-conditional-access-controls.md#session-controls)控制項的清單。

在未套用至使用者的原則清單中，您也可以找到未套用這些原則的原因。 每個所列出原則的原因各代表其第一個未符合的條件。 原則已停用是之所以未套用原則的可能原因之一，因為已停用的原則不會進行進一步的評估。   



## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 

- 如果您要移轉傳統原則，請參閱[在 Azure 入口網站中移轉傳統原則](active-directory-conditional-access-migration.md)  
