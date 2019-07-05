---
title: 什麼是如果工具在 Azure Active Directory 條件式存取？
description: 了解如何在您的環境了解您的條件式存取原則的影響。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97d2ec4099045e17b8482fcde313d31720083583
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506748"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>什麼是如果工具在 Azure Active Directory 條件式存取？

[條件式存取](../active-directory-conditional-access-azure-portal.md)是一項功能的 Azure Active Directory (Azure AD)，可讓您控制如何授權使用者存取您雲端應用程式。 您知道預期您的環境中的條件式存取原則目標為何？ 若要回答這個問題，您可以使用**條件式存取那如果工具**。

這篇文章說明如何使用這項工具來測試您的條件式存取原則。

## <a name="what-it-is"></a>內容

**條件式存取那如果原則工具**可讓您了解您的環境上的條件式存取原則的影響。 這個工具並非要讓您以手動方式執行多個登入來測試原則，而是要讓您評估使用者的模擬登入。 該模擬可評估此登入對原則所造成的影響，並產生模擬報告。 報表不會只列出所套用的條件式存取原則，但也[傳統原則](policy-migration.md#classic-policies)如果有的話。    

**要是**工具可用來快速判斷套用至特定使用者的原則。 例如，如果您需要針對問題進行疑難排解，就可以使用這項資訊。    

## <a name="how-it-works"></a>運作方式

在 **條件式存取那如果工具**，您必須先設定您想要模擬的登入案例的設定。 這些設定包括：

- 您要測試的使用者 
- 使用者會嘗試存取的雲端應用程式
- 要在什麼樣的條件下執行雲端應用程式的存取
     
在下一個步驟中，您可以起始模擬執行來評估設定。 只有已啟用的原則才會參與評估執行。

評估完成時，此工具會產生一份受影響原則的報告。

## <a name="running-the-tool"></a>執行工具

您可以找到**要是**工具 **[條件式存取-原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** 在 Azure 入口網站中的頁面。

若要啟動工具，在原則清單頂端的工具列中，按一下**要是**。

![What If](./media/what-if-tool/01.png)

您必須先進行設定，才能執行評估。

## <a name="settings"></a>設定

本節為您提供模擬執行設定的相關資訊。

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>使用者

您只能選取一個使用者。 這是唯一的必要欄位。

### <a name="cloud-apps"></a>雲端應用程式

這項設定的預設值是 [所有雲端應用程式]  。 此預設設定會對環境中的所有可用原則進行評估。 您可以將此範圍縮小至會影響特定雲端應用程式的原則。

### <a name="ip-address"></a>IP 位址

IP 位址是用來模擬[位置條件](location-condition.md)的單一 IPv4 位址。 此位址代表使用者用來登入之裝置的網際網路對向位址。 例如，您可以瀏覽至[我的 IP 位址為何](https://whatismyipaddress.com)來確認裝置的 IP 位址。    

### <a name="device-platforms"></a>裝置平台

這項設定會模擬[裝置平台條件](conditions.md#device-platforms)，並代表**所有平台 (包括不支援平台)** 的同等項目。 

### <a name="client-apps"></a>用戶端應用程式

這項設定會模擬[用戶端應用程式條件](conditions.md#client-apps)。
根據預設，此設定會導致系統評估已選取 [瀏覽器]  和 (或) [行動裝置應用程式和桌面用戶端]  的所有原則。 此外，還會偵測強制執行 **Exchange ActiveSync (EAS)** 的原則。 您可以選取下列選項來縮小此設定的範圍：

- **瀏覽器**：可評估所有至少已選取 [瀏覽器]  的原則。 
- **行動裝置應用程式和桌面用戶端**：可評估所有至少已選取 [行動裝置應用程式和桌面用戶端]  的原則。 

### <a name="sign-in-risk"></a>登入風險

這項設定會模擬[登入風險條件](conditions.md#sign-in-risk)。   

## <a name="evaluation"></a>評估 

按一下即可開始評估**要是**。 評估結果所提供的報告中會包含： 

![What If](./media/what-if-tool/03.png)

- 環境中是否存在傳統原則的指標
- 套用至使用者的原則
- 未套用至使用者的原則

如果選取的雲端應用程式中存在[傳統原則](policy-migration.md#classic-policies)，系統就會向您顯示指標。 按一下指標，系統就會將您重新導向至傳統原則頁面。 在傳統原則頁面中，您可以移轉傳統原則，也可以直接加以停用。 關閉此頁面就會回到評估結果。

在套用至所選取使用者的原則清單中，您還可以找到使用者必須符合之[授與控制項](controls.md#grant-controls)和[工作階段](controls.md#session-controls)控制項的清單。

在未套用至使用者的原則清單中，您也可以找到未套用這些原則的原因。 每個所列出原則的原因各代表其第一個未符合的條件。 原則已停用是之所以未套用原則的可能原因之一，因為已停用的原則不會進行進一步的評估。   

## <a name="next-steps"></a>後續步驟

- 如果您想要了解如何設定條件式存取原則，請參閱[需要 MFA 的特定應用程式與 Azure Active Directory 條件式存取](app-based-mfa.md)。
- 如果您已準備好設定您的環境的條件式存取原則，請參閱[Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)。 
- 如果您要移轉傳統原則，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)  
