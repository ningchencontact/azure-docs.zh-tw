---
title: 如何在 Azure Active Directory Identity Protection 中關閉作用中的風險偵測 |Microsoft Docs
description: 瞭解您已關閉作用中風險偵測的選項。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126482"
---
# <a name="how-to-close-active-risk-detections"></a>如何：關閉作用中的風險偵測

使用[風險](../reports-monitoring/concept-risk-events.md)偵測時, Azure Active Directory 會偵測可能遭盜用之使用者帳戶的指標。 身為系統管理員, 您想要讓所有風險偵測關閉, 讓受影響的使用者不再有風險。

本文概述您必須關閉作用中風險偵測的其他選項。

## <a name="options-to-close-risk-detections"></a>關閉風險偵測的選項 

風險偵測的狀態為 [作用中 ] 或 [**已關閉**]。 所有作用中的風險偵測都有助於計算名為「使用者風險層級」的值。 使用者風險層級是帳戶遭到入侵的可能性指標 (低、中、高)。 

若要關閉作用中的風險偵測, 您有下列選項:

- 使用者風險原則需要密碼重設
- 手動重設密碼
- 關閉所有風險偵測 
- 手動關閉個別風險偵測

## <a name="require-password-reset-with-a-user-risk-policy"></a>使用者風險原則需要密碼重設

藉由設定[使用者風險條件式存取原則](howto-user-risk-policy.md), 如果已自動偵測到指定的使用者風險層級, 您就可以要求變更密碼。 

![重設密碼](./media/howto-close-active-risk-events/13.png)

密碼重設會關閉相關使用者的所有作用中風險事件，並且讓身分識別回到安全狀態。 使用使用者風險原則是關閉作用中風險偵測的慣用方法, 因為此方法是自動化的。 受影響的使用者與技術支援中心或系統管理員之間不需要進行任何互動。

不過，使用使用者風險原則不一定適用。 例如，這適用於：

- 尚未註冊多重要素驗證 (MFA) 的使用者。
- 具有已刪除之作用中風險偵測的使用者。
- 調查顯示已回報的風險偵測已由合法使用者執行。

## <a name="manual-password-reset"></a>手動重設密碼

如果您無法使用使用者風險原則來要求重設密碼, 您可以透過手動密碼重設來取得已關閉使用者的所有風險偵測。

![重設密碼](./media/howto-close-active-risk-events/04.png)

相關的對話方塊提供兩個不同的方法，可以將密碼重設為︰

![重設密碼](./media/howto-close-active-risk-events/05.png)

**產生臨時密碼** - 藉由產生暫時密碼，您可以立即讓身分識別回到安全狀態。 此方法需要與受影響的使用者互動，因為他們需要知道暫時密碼。 例如，您可以將新的暫時密碼傳送到使用者的備用電子郵件地址，或傳送給使用者的經理。 因為此密碼是暫時的，所以會提示使用者在下次登入時變更密碼。

**要求使用者重設密碼** - 要求使用者重設密碼能夠自行復原，而不需要連絡技術支援中心或系統管理員。 如同在使用者風險原則的範例中，這個方法僅適用於註冊 MFA 的使用者。 位於尚未註冊 MFA 的使用者，無法使用此選項。

## <a name="dismiss-all-risk-detections"></a>關閉所有風險偵測

如果密碼重設不是您的選項, 您也可以解除所有風險偵測。 

![重設密碼](./media/howto-close-active-risk-events/03.png)

當您按一下 [關閉所有事件]，所有事件都已關閉，且受影響的使用者不再有風險。 不過，因為這個方法不會影響現有的密碼，所以不會讓相關的身分識別回到安全狀態。 此方法的慣用使用案例是已刪除的使用者, 具有作用中的風險偵測。 

## <a name="close-individual-risk-detections-manually"></a>手動關閉個別風險偵測

您可以手動關閉個別風險偵測。 藉由手動關閉風險偵測, 您可以降低使用者風險層級。 通常會以手動方式關閉風險偵測, 以回應相關的調查。 例如, 與使用者交談時, 會顯示不再需要作用中的風險偵測。 
 
手動關閉風險偵測時, 您可以選擇採取下列任何動作來變更風險偵測的狀態:

![個動作](./media/howto-close-active-risk-events/06.png)

- **解決**-如果在調查風險偵測之後, 您在 Identity Protection 外部採取適當的補救動作, 而且您認為應該將風險偵測視為已關閉, 請將事件標示為已解決。 已解決的事件會將風險偵測的狀態設定為 [已關閉], 而風險偵測不會再影響到使用者風險。
- **標記為 false-肯定**-在某些情況下, 您可以調查風險偵測, 併發現它不正確地標示為有風險。 您可以藉由將風險偵測標記為「假-正面」, 協助減少這類出現次數。 這可協助機器學習演算法未來改善類似事件的分類。 誤判事件的狀態為 [已關閉]，而且不再算是使用者風險。
- **忽略**-如果您尚未採取任何補救動作, 但想要從作用中清單中移除風險偵測, 您可以將風險偵測標記為 [忽略], 事件狀態將會是 [已關閉]。 忽略的事件不算是使用者風險。 這個選項只能用於不尋常的情況下。
- **重新**啟用-可以重新開機已手動關閉的風險偵測 (藉由選擇 [解析]、[誤報] 或 [忽略]), 將事件狀態設定回 [作用中]。 重新開機的風險偵測會導致使用者風險層級計算。 無法重新啟用透過補救關閉的風險偵測 (例如, 安全密碼重設)。

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview.md)。
