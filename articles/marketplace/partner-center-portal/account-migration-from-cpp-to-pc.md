---
title: 帳戶從 Cloud Partner 入口網站遷移至合作夥伴中心-適用于 Azure 的商業 Marketplace
description: 如何將您的帳戶從 CPP 遷移至合作夥伴中心。 -適用于 Azure 的商業 Marketplace
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c7680896507cd3738fa3bce0d30a516d08509c4
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383297"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>從 Cloud Partner 入口網站到合作夥伴中心的帳戶遷移

當您的供應專案從 Cloud Partner 入口網站（CPP）遷移至合作夥伴中心（PC）時，會在 CPP 中鎖定以進行編輯。 此時，您的帳戶設定必須遷移至合作夥伴中心。 您的帳戶設定和您的供應專案都可以在合作夥伴中心進行管理。

## <a name="account-migration-process"></a>帳戶遷移程式

當供應專案從 CPP 遷移時，您的帳戶會設定為可進行遷移。 
 
如果您是指定帳戶的 [擁有者] 角色的使用者，則會在您的發行者設定檔頁面面上顯示黃色橫幅。 系統會要求您將帳戶設定移至合作夥伴中心。 

按一下橫幅以起始您的帳戶遷移程式。 您應該輸入下列專案：

### <a name="work-email-address"></a>工作電子郵件地址

在大部分情況下，請使用您用來登入 CPP 的電子郵件地址來登入。 在某些情況下，必須使用不同的電子郵件地址：

* Microsoft 帳戶：如果 CPP 帳戶是 Microsoft 帳戶，則您必須輸入與租使用者相關聯的有效工作電子郵件地址，以 MPN 識別碼註冊。

* 租使用者不符：如果您的公司電子郵件地址不屬於與您的 CPP 帳戶上存在的 Microsoft 合作夥伴網路識別碼相關聯的租使用者，則您會看到錯誤。 若要移動超過此錯誤，請輸入與租使用者相關聯的電子郵件地址。 錯誤訊息會提供租使用者的名稱。

### <a name="sign-up-for-microsoft-partner-network-program"></a>註冊 Microsoft 合作夥伴網路方案

如果您的 CPP 帳戶沒有 Microsoft 合作夥伴網路識別碼，或有一個不正確 ID，您就必須在啟用程式中註冊 Microsoft 合作夥伴網路方案。

## <a name="after-account-migration-is-complete"></a>帳戶遷移完成後

針對指定的帳號，遷移只需要執行一次。 一旦給定的夥伴完成帳戶的遷移，所有擁有者就會在其發行者設定檔頁面面上看到此行為：

1. 您會在 Microsoft 合作夥伴網路中看到 合作夥伴設定 頁面，您現在可以在其中管理帳戶設定。 
2. 在 CPP 的發行者設定檔頁面面上，會向擁有「擁有者」角色的使用者顯示黃色橫幅，要求他們在合作夥伴中心管理其帳戶設定。
3. CPP 中的 [帳戶設定] 頁面會轉換成隻讀模式。

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>將以 Dynamics 365 為基礎的解決方案移至合作夥伴中心

如果您已在一個商業夥伴操作入口網站中為客戶參與或 Dynamics 365 建立 Dynamics 365 for 財務和營運解決方案，**這些解決方案現在應在合作夥伴中心內進行管理**。

**如果您未在2019年8月31日前移動解決方案**，請儘快完成下列步驟。 在您這麼做之前：
- Isv 無法存取行銷權益
- 共同銷售優先順序將會失去其狀態
- 需要雲端內嵌的人員在2019年10月15日之後不符合規範

> [!NOTE]
> 如果您的 MPN 成員資格帳戶最初是在合作夥伴成員中心（PMC）中建立，請登入[合作夥伴中心](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)以確認您的帳戶已遷移，然後再完成下列步驟。 如果您看到具有 MPN 識別碼的設定檔畫面，您就可以繼續進行。 如果沒有，您必須遵循[合作夥伴成員中心](https://partners.microsoft.com/partnerprogram/Welcome.aspx)的提示來啟動帳戶遷移。 如果您需要協助，請造訪[支援](https://partner.microsoft.com/support?issueid=100-0077)。

1. 前往[合作夥伴中心的商業 Marketplace 總覽頁面](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。 如果您在左側流覽窗格中看到 [商業 Marketplace]，表示您已註冊，且應該繼續進行下一個步驟。 如果沒有，請立即[註冊商業 marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) 。
2. [搜尋您的](https://appsource.microsoft.com/)供應專案，以確認您的供應專案已 AppSource。 如果您的供應專案已 AppSource，請繼續進行下一個步驟。 針對不在 AppSource 中的任何供應專案，建立[新的 dynamics 365 For Customer Engagement 供應專案](create-new-customer-engagement-offer.md)或[新的 Dynamics 365 For Operations 供應](create-new-operations-offer.md)專案。
3. 確認您在 Business Applications ISV Connect 計畫中的註冊：
  
   * [在合作夥伴中心的 [合約](https://partner.microsoft.com/dashboard/account/agreements)] 頁面上，確定您已接受**Business Applications ISV**增補以註冊方案。
   * 在 [[帳戶設定](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)] 頁面上，提供您的帳單資訊。

4. 提交每個新的和現有的供應專案，以取得認證，即使您的供應專案先前已通過認證。 如有資格，您可以在此程式期間要求參與進階層。 如果您的供應專案先前已**通過認證，您必須在2019年10月15日完成應用程式重新認證。** 認證或重新認證會要求您的應用程式支援 Business Applications 平臺的最新版本。
5. 前往[一個商用合作夥伴的連線入口網站](https://msgtm.azurewebsites.net/en-US/Profile/SignIn)，並在 [Marketplace 連結] 區段中新增您的 APPSOURCE 清單 URL。 如果您需要此步驟的協助，請傳送電子cosell@microsoft.com郵件給我們。

## <a name="next-steps"></a>後續步驟

- [在合作夥伴中心管理您的商業 Marketplace 帳戶](./manage-account.md) 
