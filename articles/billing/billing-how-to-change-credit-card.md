---
title: 變更您用於 Azure 的信用卡
description: 描述如何變更用來支付 Azure 訂用帳戶的信用卡。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "68383672"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>新增、更新或移除用於 Azure 的信用卡

在 Azure 入口網站中，您可以新增信用卡、更新現有的信用卡或刪除不再使用的信用卡。 您必須是[帳戶管理員](billing-subscription-transfer.md#whoisaa)才能進行這些變更。

如果您有 [Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)，付款方式會與帳單設定檔相關聯。 了解如何[變更帳單設定檔的預設付款方式](#change-payment-method-for-a-billing-profile)。 只有註冊 Azure 的使用者可以更新付款方式。

**想要改成按發票 (支配/電匯) 付款嗎？** 請參閱[按發票支付 Azure 訂閱費](billing-how-to-pay-by-invoice.md)。

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>將新的信用卡新增至 Azure 訂用帳戶

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示搜尋的螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 選取要新增信用卡的訂用帳戶。
1. 選取 [**付款方式**]。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 在左上角選取 [+] 以新增卡片。 信用卡表單會出現在右側。
1. 輸入信用卡詳細資料。

    ![顯示新增卡片的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. 若要讓此卡片成為您的有效付款方式，請在表單上方勾選 [將此方式設定成我的有效付款方式]  旁的方塊。 對於所有與選取的訂用帳戶使用相同卡片的訂用帳戶，這張卡片都將成為使用中的付款方式。

1. 選取 [下一步]  。

如果在新增信用卡之後收到錯誤，請參閱[在 Azure 註冊時信用卡遭拒](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="update-existing-credit-card"></a>更新現有的信用卡

如果您的信用卡換卡，但號碼維持不變，請更新現有信用卡的詳細資料即可，例如有效期限。 如果您的信用卡號碼因為卡片遺失、遭竊或過期而變更，請遵循[將信用卡新增為付款方式](#addcard)一節中的步驟。 您不需要更新 CVV。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示搜尋的螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 選取 [**付款方式**]。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 按一下您要編輯的信用卡。 信用卡表單會出現在右側。

    ![顯示已選取信用卡的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. 更新信用卡詳細資料。
1. 選取 [ **儲存**]。

## <a name="use-a-different-credit-card"></a>使用不同的信用卡

如果您有一個以上的訂用帳戶具有相同的有效付款方式，則變更任一訂用帳戶的有效付款方式也會更新其他訂用帳戶的有效付款方式。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。

    ![顯示搜尋的螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 選取要新增信用卡的訂用帳戶。
1. 選取 [**付款方式**]。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 選取要設為有效付款方式的卡片旁邊的方塊。
1. 按一下 [設為有效]  。
    ![顯示已選取信用卡和設為有效的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>從帳戶中移除信用卡

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頁面左側的 [成本管理 + 帳單]  。

    ![顯示搜尋的螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 在 [帳單]  下方選取 [付款方式]  。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 選取要移除的卡片旁邊的方塊。
1. 按一下 [刪除]  。

如果您的信用卡是任何 Microsoft 訂用帳戶的有效付款方式，您就無法從 Azure 帳戶中將它移除。 請變更連結至此信用卡之所有訂用帳戶的有效付款方式，然後再試一次
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>變更帳單設定檔的付款方式

若要變更帳單設定檔的付款方式，您必須是註冊 Azure 的人員。

如果您想要將預設付款方式切換為支票/電匯，請了解如何[將帳單設定檔切換為支票/電匯](billing-how-to-pay-by-invoice.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。
1. 在左側功能表中，按一下 [帳單設定檔]  。

    ![在功能表中顯示帳單設定檔的螢幕擷取畫面](./media/billing-how-to-change-credit-card/billing-profile.png)

1. 選取帳單設定檔。
1. 在左側功能表中，選取 [付款方式]  。

   ![在功能表中顯示付款方式的螢幕擷取畫面](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. 在預設付款方式上方，按一下 [變更]  。

    ![顯示變更按鈕的螢幕擷取畫面](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. 選取現有的卡片或新增一個。

## <a name="frequently-asked-questions"></a>常見問題集
下列各節回答有關變更信用卡資訊的常見問題。

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>我的訂閱已停用。 為什麼無法立即移除信用卡？

停用或取消訂閱後，需要 90 天系統才會將其永久刪除。 我們在保留期間會保留您的付款方式，以防您想重新啟用訂閱。 該期間過後，訂用帳戶就會永久刪除。

如果您在 90 天的保留期結束前需要移除信用卡，請[重新啟用訂用帳戶](billing-subscription-become-disable.md)。 如需無法重新啟用，請[連絡 Azure 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>為什麼我一直收到「您的登入工作階段已過期。 請按一下這裡重新登入」錯誤訊息？

如果您已登出並重新登入，但還是一直收到此錯誤訊息，請用私密瀏覽工作階段再試一次。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何針對我的每個訂閱使用不同的信用卡/轉帳卡？

很抱歉，如果您已經為所有的訂閱設定使用相同的信用卡/轉帳卡，就不能改用其他信用卡。 不過，註冊新訂閱時，可以為該訂閱選擇新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果您將信用卡設定為付款方式，我們便會在每次計費期間結束後自動向您的信用卡/轉帳卡扣款。 您不需要進行任何動作。

如果您使用[按發票付款](billing-how-to-pay-by-invoice.md)，請將您的款項寄到發票底部所列的地址。

### <a name="how-do-i-change-the-tax-id"></a>如何變更統一編號？

若要新增或更新統一編號，請在 [Azure 帳戶中心](https://account.azure.com/Profile)更新您的設定檔，然後選取 [稅務記錄]  。 此統一編號會用於計算免稅金額，並出現在您的發票上。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 了解 [Azure 保留](billing-save-compute-costs-reservations.md)，以查看是否能為您節省費用。
