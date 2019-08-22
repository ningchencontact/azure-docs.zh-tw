---
title: 針對 Azure 付款問題進行疑難排解
description: 解決在 Microsoft Azure 入口網站或帳戶中心更新付款資訊帳戶時的問題。
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657082"
---
# <a name="troubleshoot-azure-payment-issues"></a>針對 Azure 付款問題進行疑難排解

當您嘗試更新 Microsoft Azure 入口網站或 Azure 帳戶中心的付款資訊帳戶時, 可能會遇到問題。 在您針對問題進行疑難排解之前, 請考慮下列指導方針:

- 請確定您為 Azure 帳戶設定檔 (包括連絡人電子郵件地址、街道位址和電話號碼) 提供的資訊正確無誤。
- 請確認信用卡資訊正確。
- 確定您沒有具有相同資訊的 Microsoft 帳戶。

## <a name="issues"></a>問題

若要解決任何錯誤, 請選取您在嘗試註冊 Azure 時所遇到的問題。

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>無法從儲存的帳單付款方法移除信用卡

根據設計, 您無法從有效的訂用帳戶中移除信用卡。

如果要刪除現有的卡片, 必須將新的卡片新增至訂用帳戶, 才能成功刪除舊的付款條件, 或者您必須取消訂用帳戶。 這會永久刪除訂閱並移除卡片。

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>新增新的付款條件之後, 無法刪除舊的付款方法

新的付款方式可能無法與訂用帳戶建立關聯。 若要協助將付款條件與訂用帳戶建立關聯, 請參閱[新增、更新或移除 Azure 的信用卡或轉帳卡](billing-how-to-change-credit-card.md)。

若要針對已拒絕的卡片疑難排解問題, 請參閱[如何在 Azure 註冊時針對已拒絕的卡片進行疑難排解](billing-troubleshoot-declined-card.md)。

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>無法刪除付款條件, 因為*無法刪除付款方法*錯誤

這是因為未完成的餘額所造成。 刪除付款方法之前, 請清除任何未完成的餘額。

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>無法查看我的帳戶下的訂用帳戶來更新付款方法

您可能使用的電子郵件識別碼與訂閱所使用的不同。

若要對此問題進行疑難排解, 請參閱[Azure 入口網站或 Azure 帳戶中心找不到任何訂閱登入錯誤](billing-no-subscriptions-found.md)。

### <a name="unable-to-make-payment-for-a-subscription"></a>無法對訂用帳戶進行付款

如果您收到錯誤訊息:*付款已逾期。您的付款條件*發生問題, 或*抱歉, 無法儲存資訊。關閉瀏覽器, 然後再試一次。* , 因為您的金融機構拒絕了卡片, 所以卡片上有暫止的付款。

請確認信用卡具有足夠的餘額來付款。 如果不是, 請使用另一個卡片來進行付款, 或與您的金融機構聯繫以解決問題。

請洽詢您的銀行以瞭解下列問題:

- 未啟用國際交易。
- 卡片具有信用額度限制, 而且必須進行餘額。
- 卡片上會啟用週期性付款。

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>因為瀏覽器問題而無法變更付款條件 (瀏覽器沒有回應、未載入等等)

登出所有作用中的 Azure 會話, 然後依照在 microsoft edge 中[流覽 inprivate 一文](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate)中的步驟, 在 microsoft Edge 或 Internet Explorer 中啟動 inprivate 會話。

在私人會話中, 依照[如何變更信用卡](billing-how-to-change-credit-card.md)以更新或變更信用卡資訊中的步驟進行。

您也可以嘗試執行下列動作:

- 重新整理瀏覽器
- 使用另一個瀏覽器
- 刪除快取的 cookie

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>更新付款方法後, 我的訂用帳戶仍會停用。

發生此問題的原因是未平衡。 刪除付款方法之前, 請清除任何未完成的餘額。

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>因為 XML 錯誤回應頁面, 所以無法變更付款條件

如果您使用[Azure 入口網站](https://portal.azure.com/)新增信用卡, 則會收到此訊息。

若要新增卡片詳細資料, 請使用帳戶管理員的電子郵件地址登入 Azure 帳戶入口網站。

## <a name="additional-help-resources"></a>其他說明資源

Azure 計費和訂用帳戶的其他疑難排解文章

- [卡片遭拒](billing-troubleshoot-declined-card.md)
- [訂用帳戶登入問題](billing-troubleshoot-sign-in-issue.md)
- [找不到訂用帳戶](billing-no-subscriptions-found.md)
- [已停用企業成本檢視](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>聯絡我們以取得協助

如果您有問題或需要協助，請[建立支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>後續步驟

- [Azure 計費檔](index.md)
