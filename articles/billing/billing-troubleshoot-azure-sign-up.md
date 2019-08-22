---
title: 針對 Azure 註冊進行疑難排解
description: 解決在 Microsoft Azure 入口網站帳戶中心內嘗試註冊新帳戶時的問題。
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
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657069"
---
# <a name="troubleshoot-azure-sign-up"></a>針對 Azure 註冊進行疑難排解

當您嘗試在 Microsoft Azure 入口網站或 Azure 帳戶中心註冊新帳戶時, 可能會遇到問題。 在您針對問題進行疑難排解之前, 請先確認下列事項:

- 您為 Azure 帳戶設定檔 (包括連絡人電子郵件地址、街道位址和電話號碼) 提供的資訊正確。
- 信用卡資訊正確。
- 您還沒有具有相同資訊的 Microsoft 帳戶。

## <a name="resolutions"></a>解決方法

若要解決任何錯誤, 請選取您在嘗試註冊 Azure 時所遇到的問題。

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Error:*因為您的帳戶發生問題, 所以無法繼續註冊。請聯絡帳務支援。*

若要解決此問題，請依照下列步驟執行︰

1. 使用帳戶管理員認證登入 [Azure 帳戶中心](https://account.azure.com/Profile)。

2. 選取 [編輯詳細資料]。

3. 確定所有地址欄位皆已填寫完畢且正確無誤。

4. 當您註冊 Azure 訂用帳戶時, 請確定信用卡註冊的帳單位址符合您的銀行記錄。

如果您繼續收到錯誤訊息, 請嘗試使用不同的瀏覽器進行註冊。

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[*依卡片的身分識別驗證*] 區段中的進度列停止回應。

若要使用卡片完成身分識別驗證，您必須允許瀏覽器接受第三方 Cookie。

![依據卡片進行身分識別驗證](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
使用下列步驟來更新瀏覽器的 Cookie 設定。

1. 如果您使用的是 Chrome, 請選取 [**設定** > ] [**顯示高級設定** > ] [**隱私權** > **內容設定**]。 清除 [**封鎖協力廠商 cookie 和網站資料**]。

2. 如果您使用 Microsoft Edge, 請選取 [**設定** > ] [流覽] [**高級設定** > ] [**cookie**  > **不封鎖 cookie**]。

3. 重新整理 Azure 註冊頁面, 然後檢查問題是否已解決。

4. 如果重新整理無法解決問題, 請結束並重新啟動瀏覽器, 然後再試一次。

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡表單不支援我的帳單地址

您的帳單位址必須位於您在 [**關於您**] 區段中選取的國家/地區。 請確定您選取的是正確的國家/地區。

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>註冊帳戶驗證期間沒有文字訊息或呼叫

雖然此程式通常是快速的, 但最多可能需要四分鐘的時間, 才會傳遞驗證碼。 您輸入進行驗證的電話號碼不會儲存為帳戶的連絡人編號。

以下是一些其他的祕訣：

- 電話驗證程式不能使用「語音 over IP」 (VoiP) 電話號碼。
- 再次檢查您輸入的電話號碼, 包括您在下拉式功能表中選取的國家/地區代碼。
- 如果您的手機沒有收到簡訊 (SMS)，請嘗試 [撥號給我] 選項。
- 請確定您的電話可以從以美國為基礎的電話號碼接收電話或 SMS 訊息。

當您取得文字訊息或電話時, 請在文字方塊中輸入您收到的代碼。

### <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒絕或不被接受

不接受虛擬或預付型信用卡或金融卡作為 Azure 訂用帳戶的付款。 若要查看其他可能造成您的卡片遭到拒絕的原因, 請參閱[在 Azure 註冊時](https://support.microsoft.com/help/4042960), 您的轉帳卡或信用卡遭到拒絕。

### <a name="free-trial-is-not-available"></a>免費試用無法使用

過去曾使用 Azure 訂用帳戶嗎？ Azure 使用規定協議限制免費試用啟動僅適用 Azure 的新使用者。 如果您有任何其他類型的 Azure 訂用帳戶，會無法啟用免費試用。 請考慮註冊[隨用隨付訂用帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>我的免費試用帳戶收到一筆費用

註冊後, 您可能會在信用卡帳戶上看到小型的驗證保存。 這會在三到五天內移除。 如果您擔心管理成本，請深入了解[如何避免非預期的成本](billing-getting-started.md)。

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案

請確定您使用的是正確的登入認證。 然後, 請檢查權益計畫, 以確定您符合資格。

- MSDN 
  - 在您的[MSDN 帳戶頁面](https://msdn.microsoft.com/subscriptions/manage/default.aspx)上驗證您的資格狀態。
  - 如果您無法驗證自己的狀態, 請聯絡[MSDN 訂閱客戶服務中心](https://msdn.microsoft.com/library/aa493452.aspx)。
- Microsoft for Startups
  - 登入 Microsoft for 新創公司[入口網站](https://startups.microsoft.com/#start-two), 以驗證您的 microsoft 是否符合新創公司資格狀態。
  - 如果您無法驗證自己的狀態, 您可以在[Microsoft 的](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)新創公司論壇取得協助。
- MPN 
  - 登入[MPN 入口網站](https://mspartner.microsoft.com/Pages/Locale.aspx), 以驗證您的資格狀態。 如有適當的[雲端平台能力](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)，您或許能夠得到額外的好處。
  - 如果您無法驗證您的狀態, 請聯絡[MPN 支援](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)人員。

### <a name="cant-activate-new-azure-in-open-subscription"></a>無法啟動新的 Azure In Open 訂用帳戶

若要建立 Azure In Open 訂用帳戶, 您必須具備有效的線上服務啟用 (OSA) 金鑰, 其中至少有一個與其相關聯的 Azure In Open 權杖。 如果您沒有 OSA 金鑰, 請洽詢[Microsoft 定點](http://pinpoint.microsoft.com/)中列出的其中一個 microsoft 合作夥伴。

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Error:*您不符合 Azure 訂用帳戶的資格*

若要解決此問題, 請再次檢查下列專案是否為 true:

- 您為 Azure 帳戶設定檔 (包括連絡人電子郵件地址、街道位址和電話號碼) 提供的資訊正確。
- 信用卡資訊正確。
- 您還沒有使用相同資訊的 Microsoft 帳戶。

### <a name="error-your-current-account-type-is-not-supported"></a>Error:*不支援您目前的帳戶類型*

如果帳戶是在[非受控 Azure AD 目錄](../active-directory/users-groups-roles/directory-self-service-signup.md)中註冊, 而且不在貴組織的 Azure AD 目錄中, 就會發生此問題。 

若要解決此問題, 請使用另一個帳戶來註冊 Azure 帳戶, 或接管非受控 AD 目錄。 如需詳細資訊, 請參閱[在 Azure Active Directory 中以系統管理員身分接管非受控目錄](../active-directory/users-groups-roles/domains-admin-takeover.md)。
 
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
