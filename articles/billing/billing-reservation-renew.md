---
title: 自動更新 Azure 保留專案
description: 瞭解您可以如何自動更新 Azure 保留, 以繼續取得保留折扣。
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679454"
---
# <a name="automatically-renew-reservations"></a>自動更新保留

您可以更新保留專案, 以在現有保留期到期時自動購買取代專案。 自動續訂提供了一種簡單的方式, 可繼續取得保留折扣。 它也能讓您不必密切監視保留的到期時間。 透過自動更新, 您不需要手動更新, 即可避免節省效益。 [續約] 設定預設為關閉。 隨時啟用或停用更新設定, 直到現有保留的到期為止。

更新保留區會在現有保留期過期時, 建立新的保留區。 它不會延伸現有保留的期限。

隨時選擇自動續約。 續約價格會在現有保留的到期前30天內提供。 當您在保留到期前的30天內啟用更新時, 系統會傳送一封電子郵件, 詳述在到期前30天的續訂成本。 保留價格可能會在您鎖定更新價格和續約時間的時間之間變更。 若是如此, 您的續約成本就是這兩項成本的較低。 您可以對保留數量進行變更。 如果您這樣做, 則更新會在數量變更時, 更新為使用市場價格集合。

您不需要更新任何義務, 而且可以在現有保留期到期之前隨時退出宣告更新。

## <a name="set-up-renewal"></a>設定更新

移至 Azure 入口網站 >**保留**。

1. 選取保留。
2. 按一下 [**更新**]。
3. 選取 [**在到期時自動購買新的保留**區]。  
  ![顯示保留更新的範例](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>如果您沒有更新

您的服務會繼續正常執行。 在保留期到期後, 您將以隨用隨付費率向您收取使用量費用。

## <a name="required-renewal-permissions"></a>必要的更新許可權

若要更新保留, 必須符合下列條件:

- 您必須是現有保留的擁有者。
- 如果保留範圍設定為單一訂用帳戶或資源群組, 您必須是訂用帳戶的擁有者。
- 如果訂用帳戶具有共用範圍, 您必須是該訂閱的擁有者。

## <a name="default-renewal-settings"></a>預設的更新設定

根據預設, 更新會從到期的保留中繼承所有屬性。 保留更新購買具有相同的 SKU、區域、範圍、計費訂用帳戶、條款和數量。

不過, 您可以更新續約保留購買數量, 以優化您的節省成本。

## <a name="when-the-new-reservation-is-purchased"></a>購買新的保留時

當現有的保留過期時, 就會購買新的保留。 我們會嘗試防止兩個保留之間的任何延遲。 持續性可確保您的成本是可預測的, 而且您會繼續取得折扣。

## <a name="changing-parent-reservation-after-setting-renewal"></a>設定更新後變更父保留

如果您對即將到期的保留進行下列任何變更, 保留更新將會取消:

- 分割
- 合併
- 將保留從一個帳戶轉移至另一個帳戶
- 將保留從 WebDirect 訂用帳戶轉移至 enterprise 合約 (EA) 訂用帳戶或任何其他購買方法
- 更新註冊

新的保留區會在更新期間, 從到期的保留區繼承 [範圍] 和 [實例大小] 彈性設定。

## <a name="new-reservation-permissions"></a>新的保留許可權

Azure 會將到期保留的許可權複製到新的保留區。 此外, 保留購買的訂閱帳戶管理員可以存取新的保留。

## <a name="potential-renewal-problems"></a>可能的更新問題

在下列情況中, Azure 可能不會處理續約:

- 無法收集付款
- 更新期間發生系統錯誤
- 更新期間的到期 SKU 不在作用中
- EA 已更新為不同的 EA

如果發生上述任何一種情況, 並停用更新, 您將會收到電子郵件通知。

## <a name="renewal-notification"></a>續約通知

根據您的購買方法, 將電子郵件傳送給不同的人員:

- EA 客戶-電子郵件會傳送到 EA 入口網站上設定的通知連絡人。
- 使用隨用隨付費率的個別訂用帳戶客戶-會將電子郵件傳送給設定為帳戶系統管理員的使用者。
- 雲端解決方案提供者客戶-將電子郵件傳送給合作夥伴通知連絡人。

## <a name="next-steps"></a>後續步驟
- 若要深入瞭解 Azure 保留專案, 請參閱[什麼是 Azure 保留？](billing-save-compute-costs-reservations.md)
