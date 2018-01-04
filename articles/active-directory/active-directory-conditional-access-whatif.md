---
title: "條件式存取該怎麼辦工具-azure Active Directory 預覽 |Microsoft 文件"
description: "了解如何測試您的 Azure Active Directory 條件式存取原則的設定。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/21/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: de6b3dcd77132154e583d7333983d6745c4aa3bd
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>條件式存取該怎麼辦工具-azure Active Directory 預覽

[條件式存取](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 可讓您控制如何功能授權的使用者存取雲端應用程式。 如何知道有何影響表單的條件式存取原則中您的環境？ 若要回答這個問題，您可以使用**條件式存取該怎麼辦工具**。

本文說明如何使用此工具來測試條件式存取原則。

## <a name="what-it-is"></a>內容

**條件式存取假設原則工具**可讓您了解您的環境上的條件式存取原則的影響。 而不是以手動方式執行多個登入來推動您的原則測試，此工具可讓您評估在模擬登入的使用者。 模擬估計的影響，此登入對您的原則，並會產生模擬報表。 報表不會只列出套用條件式存取原則，但也[傳統原則](active-directory-conditional-access-migration.md#classic-policies)如果有的話。    

功能如果工具也可用來快速判斷原則套用至特定的使用者。 您可以使用資訊，例如，如果您需要疑難排解問題。  

## <a name="how-it-works"></a>運作方式

在**條件式存取該怎麼辦工具**，您必須先設定您要模擬的登入案例的設定。 這些設定包括：

- 您想要測試的使用者 

- 使用者會嘗試存取雲端應用程式

- 若要存取的條件約束設定雲端應用程式會執行
     
在下一個步驟中，您可以起始模擬執行評估您的設定。 已啟用的原則是執行評估的一部分。


當評估完成時，此工具會產生一份受影響的原則。


## <a name="running-the-tool"></a>執行工具

您可以找到**該怎麼辦**工具**[條件式存取-原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)**在 Azure 入口網站頁面。

若要啟動工具，在原則清單頂端工具列中，按一下**該怎麼辦**。

![該怎麼辦](./media/active-directory-conditional-access-whatif/01.png)

您可以執行評估版之前，您必須設定的設定。

## <a name="settings"></a>設定

本節為您提供的模擬執行設定的相關資訊。

![該怎麼辦](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>User

您只能選取一位使用者。 這是唯一必要的欄位。

### <a name="cloud-apps"></a>雲端應用程式

這項設定的預設值是**所有雲端應用程式**。 預設設定您的環境中執行所有可用的原則的評估。 您可以縮小原則會影響特定雲端應用程式的範圍。


### <a name="ip-address"></a>IP 位址

IP 位址是單一的 IPv4 位址，以模仿[位置條件](active-directory-conditional-access-azure-portal.md#locations)。 位址代表網際網路對向您的使用者用來登入裝置的 ip 位址。 您可以確認裝置的 IP 位址，例如瀏覽至[什麼是我的 IP 位址](https://whatismyipaddress.com)。    

### <a name="device-platforms"></a>裝置平台

這項設定會模擬[裝置平台條件](active-directory-conditional-access-azure-portal.md#device-platforms)和表示的對等**所有平台 （包括不支援）**。 
### <a name="client-apps"></a>用戶端應用程式

這項設定會模擬[用戶端應用程式條件](active-directory-conditional-access-azure-portal.md#client-apps)。
根據預設，此設定會導致需要的所有原則的評估**瀏覽器**或**行動裝置應用程式和桌面用戶端**可以個別或兩者都選取。 它也會偵測強制原則**Exchange ActiveSync (EAS)**。 您可以選取向下的檔案，以縮小此設定：

- **瀏覽器**評估所有原則，至少需要**瀏覽器**選取。 

- **行動裝置應用程式和桌面用戶端**評估所有原則，至少需要**行動裝置應用程式和桌面用戶端**選取。 


### <a name="sign-in-risk"></a>登入風險

這項設定會模擬[登入危險狀況](active-directory-conditional-access-azure-portal.md#sign-in-risk)。   


## <a name="evaluation"></a>評估 

按一下啟動評估**該怎麼辦**。 評估結果為您提供報表所組成： 

![該怎麼辦](./media/active-directory-conditional-access-whatif/03.png)

- 指標傳統原則是否存在於您的環境
- 套用到您的使用者原則
- 不套用到您的使用者原則


如果[傳統原則](active-directory-conditional-access-migration.md#classic-policies)存在於選取的雲端應用程式中，指標會呈現給您。 按一下指標，您將會重新導向至傳統的原則 頁面。 在傳統的原則 頁面中，您可以將傳統的原則移轉，或只停用。 您可以關閉此頁面，以回到評估結果。

在套用到選取的使用者原則的清單中，您也可以找到一份[授與控制項](active-directory-conditional-access-controls.md#grant-controls)和[工作階段](active-directory-conditional-access-controls.md#session-controls)控制項必須符合您的使用者。

在原則不適用於您的使用者清單中，您可以和也可以找到為何不套用這些原則的原因。 針對每個列出的原則，原因表示第一個不符合的條件。 不會套用原則的可能原因是已停用的原則，因為不會進一步評估。   



## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 

- 如果您想要移轉傳統的原則，請參閱[遷移傳統的原則，在 Azure 入口網站](active-directory-conditional-access-migration.md)  
