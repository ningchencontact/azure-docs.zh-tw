---
title: 適用於 Azure 中變更您的信用卡
description: 描述如何變更用於支付 Azure 訂用帳戶的信用卡。
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 7c04e33d6199d3930be28ce84458e9c3a743eb8a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491282"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>新增、 更新或移除適用於 Azure 的信用卡

在 Azure 入口網站中，您可以新增新的信用卡、 更新現有的信用卡或刪除您未使用的信用卡。 您必須是[帳戶管理員](billing-subscription-transfer.md#whoisaa)才能進行這些變更。

如果您有[Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)，您的付款方法是與帳單設定檔相關聯。 了解如何[變更帳單的設定檔的預設付款方式](#change-payment-method-for-a-billing-profile)。 只有已註冊 Azure 的使用者可以更新付款方法。

**想要切換至 按發票付款 （核取/寫入傳輸） 嗎？** 請參閱[按發票支付 Azure 訂閱費](billing-how-to-pay-by-invoice.md)。

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>將新的信用卡新增至 Azure 訂用帳戶

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋**成本管理 + 計費**。

    ![顯示搜尋螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 選取您想要新增信用卡的訂用帳戶。
1. 選取 [付款方式]  。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 在左上角選取 [+] 以新增卡片。 信用卡表單會出現在右側。
1. 輸入信用卡詳細資料。

    ![如果螢幕擷取畫面顯示如何加入新的卡片。](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. 若要將此卡您作用中的付款方式，請核取方塊旁**將此我使用中付款方式**表單上方。 對於所有與選取的訂用帳戶使用相同卡片的訂用帳戶，這張卡片都將成為使用中的付款方式。

1. 選取 [下一步]  。

如果在新增信用卡之後收到錯誤，請參閱[在 Azure 註冊時信用卡遭拒](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="update-existing-credit-card"></a>更新現有的信用卡

如果您的信用卡換卡，而且數字會維持相同，更新現有信用卡的詳細資料，例如到期日。 如果您的信用卡號碼因為卡片遺失、遭竊或過期而變更，請遵循[將信用卡新增為付款方式](#addcard)一節中的步驟。 您不需要更新 CVV。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋**成本管理 + 計費**。

    ![顯示搜尋螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 選取 [付款方式]  。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 按一下您想要編輯的信用卡。 信用卡表單會出現在右側。

    ![如果螢幕擷取畫面顯示選取的信用卡。](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. 更新信用卡的詳細資料。
1. 選取 [ **儲存**]。

## <a name="use-a-different-credit-card"></a>使用不同的信用卡

如果一個以上的訂用帳戶有相同的作用中的付款方法，然後變更任何這些訂用帳戶的使用中付款方式也會更新其他使用中的付款方式。

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋**成本管理 + 計費**。

    ![顯示搜尋螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 選取您想要新增信用卡的訂用帳戶。
1. 選取 [付款方式]  。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 選取您想要讓使用中付款方式卡旁的方塊。
1. 按一下 **設定使用**。
    ![如果螢幕擷取畫面會顯示已選取且設為作用中的信用卡。](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>從帳戶中移除信用卡

1. 以帳戶管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **成本管理 + 計費**頁面的左邊。

    ![顯示搜尋螢幕擷取畫面](./media/billing-how-to-change-credit-card/search.png)

1. 底下**計費**，選取**付款方式**。

    ![顯示已選取 [管理付款方式] 選項的螢幕擷取畫面。](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. 選取您想要移除 「 卡旁的方塊。
1. 按一下 [刪除]  。

如果您的信用卡任何 Microsoft 訂用帳戶的作用中的付款方法，您無法從您的 Azure 帳戶中移除它。 變更所有連結至此信用卡的訂用帳戶的使用中付款方式，並再試一次
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>變更計費的設定檔的付款方式

若要變更計費的設定檔的付款方法，您必須是已註冊 Azure 的人員。

如果您想要切換預設付款方式，以檢查/連線傳輸，了解如何[切換計費的設定檔，以檢查/網路傳輸](billing-how-to-pay-by-invoice.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。
1. 在左側功能表中，按一下**帳單設定檔**。

    ![顯示計費的設定檔功能表中的螢幕擷取畫面](./media/billing-how-to-change-credit-card/billing-profile.png)

1. 選取計費的設定檔。
1. 在左側功能表中，選取**付款方法**。

   ![在功能表中顯示的付款方法的螢幕擷取畫面](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. 上方的預設付款方法中，按一下**變更**。

    ![螢幕擷取畫面顯示變更按鈕](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. 選取的現有卡，或新增帳戶。

## <a name="frequently-asked-questions"></a>常見問題集
下列區段會回答有關變更您的信用卡或金融卡資訊的常見問題集。

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>我的訂閱已停用。 為什麼無法立即移除信用卡？

停用或取消訂閱後，需要 90 天系統才會將其永久刪除。 我們在保留期間會保留您的付款方式，以防您想重新啟用訂閱。 在那之後，會永久刪除訂用帳戶。

如果您需要先在 90 天保留期結束時，移除您的信用卡[重新啟用訂用帳戶](billing-subscription-become-disable.md)。 如需無法重新啟用，請[連絡 Azure 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>為什麼我一直收到「您的登入工作階段已過期。 請按一下這裡重新登入」錯誤訊息？

如果您已登出並重新登入，但還是一直收到此錯誤訊息，請用私密瀏覽工作階段再試一次。

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>如何針對我的每個訂閱使用不同的信用卡/轉帳卡？

很抱歉，如果您已經為所有的訂閱設定使用相同的信用卡/轉帳卡，就不能改用其他信用卡。 不過，註冊新訂閱時，可以為該訂閱選擇新的付款方式。

### <a name="how-do-i-make-payments"></a>如何付款？

如果您設定信用卡為您的付款方法，我們會自動向您的信用卡在每個計費期間。 您不需要進行任何動作。

如果您使用[按發票付款](billing-how-to-pay-by-invoice.md)，請將您的款項寄到發票底部所列的地址。

### <a name="how-do-i-change-the-tax-id"></a>如何變更統一編號？

若要新增或更新統一編號，更新您的設定檔中[Azure 帳戶中心](https://account.azure.com/Profile)，然後選取**稅務記錄**。 此統一編號會用於計算免稅金額，並出現在您的發票上。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 深入了解[Azure 保留的項目](billing-save-compute-costs-reservations.md)查看如果它們可以節省成本。
