---
title: Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶 |Microsoft Docs
description: 描述如何在 Azure 訂用帳戶的計費擁有權轉移給另一個帳戶，以及一些常見問題 (FAQ) 的程序
keywords: 轉移 azure 訂用帳戶，azure 轉移訂用帳戶，將 azure 訂用帳戶移至另一個帳戶，azure 變更訂用帳戶擁有者，azure 訂用帳戶轉移到另一個帳戶，azure 轉移帳單
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff3c57c31526119ab81225a1c70b163173be937
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514434"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶

您可能想要轉移 Azure 訂用帳戶的帳單擁有權，如果您離開您的組織，或您想要以另一個帳戶來支付訂用帳戶。 移轉帳單擁有權至另一個帳戶提供新的帳戶權限執行計費的工作，例如系統管理員變更付款方法，檢視費用，並取消訂用帳戶。

如果您想要保留帳單擁有權，但會變更您的訂用帳戶的類型，請參閱[您的 Azure 訂用帳戶切換到其他優惠](billing-how-to-switch-azure-offer.md)。 如果您想要控制可以管理訂用帳戶中的資源，請參閱[適用於 Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

如果您是 Enterprise 合約 （ea） 客戶，您的企業系統管理員可以帳戶之間傳輸訂用帳戶帳單擁有的權。 如需詳細資訊，請參閱 <<c0> [ 的 Enterprise 合約 (EA) 訂用帳戶帳單擁有權移轉](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>轉移 Azure 訂用帳戶的帳單擁有權

1. 登入[Azure 入口網站](https://portal.azure.com)具有您想要傳輸的訂用帳戶的計費帳戶的系統管理員身分。 若要了解您是否為系統管理員，請參閱[常見問題集](#faq)。

1. 針對 [成本管理 + 帳單]  進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 選取 **訂用帳戶**左側窗格中。 根據您的存取權，您可能需要選取計費的範圍，然後選取**訂用帳戶**或是**Azure 訂用帳戶**。

1. 選取 **傳輸到其他帳戶**您想要傳輸的訂用帳戶。 

   ![選取訂用帳戶轉移](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 輸入是會是新的擁有者的訂用帳戶，然後選取之帳戶的計費系統管理員之使用者的電子郵件地址**傳送傳輸要求**。

    > [!IMPORTANT]
    >
    > 如果將訂用帳戶的帳單擁有權轉移給使用者的帳戶的另一個 Azure AD 中租用戶，所有[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)指派來管理訂用帳戶中的資源會永久移除。 只有新的擁有者將可在訂用帳戶中管理資源的存取。 如需詳細資訊，請參閱 <<c0> [ 移轉至另一個 Azure AD 租用戶中的使用者訂用帳戶](../active-directory/managed-identities-azure-resources/known-issues.md)。
  
    ![傳送頁面傳送](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 使用者會收到一封電子郵件以檢閱您的轉移要求的指示。

   ![給接受者的訂用帳戶移轉電子郵件](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 若要核准的轉移要求，使用者的電子郵件中選取的連結，並遵循指示進行。 使用者必須選取將用來支付訂用帳戶的付款方法。 此外，如果使用者沒有 Azure 帳戶，他們必須註冊新帳戶。 

   ![第一個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功！ 現在已移轉訂用帳戶。

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>移轉至另一個 Azure AD 租用戶中的帳戶的訂用帳戶

當您註冊 Azure 時，會為您建立的 Azure Active Directory (AD) 租用戶。 租用戶代表您的帳戶。 您可以使用租用戶來管理您的訂用帳戶和資源的存取。

當您建立新的訂用帳戶時，會將它裝載於 Azure AD 租用戶，您的帳戶。 如果您想要提供其他人存取您的訂用帳戶或資源，您要邀請他們加入您的租用戶。 它能夠協助您控制存取您的訂用帳戶和資源。

當您的訂用帳戶的帳單擁有權轉移到另一個 Azure AD 租用戶中的帳戶時，會將訂用帳戶移至新帳戶的租用戶。 所有的使用者、 群組或有服務主體[的角色型存取 (RBAC)](../role-based-access-control/overview.md)來管理訂用帳戶中的資源會遺失其存取權。 只接受您的轉移要求者的新帳戶中的使用者能夠管理資源的存取。 若要為使用者提供存取原本有存取權，新的擁有者必須[手動將這些使用者新增至訂用帳戶](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Visual Studio、 Microsoft 合作夥伴網路 (MPN) 和付開發/測試訂用帳戶轉移

Visual Studio 和 Microsoft 合作夥伴網路訂用帳戶有與其相關聯的每月週期性的 Azure 點數。 當您將傳輸這些訂用帳戶時，您的點數不適用於目的地的計費帳戶。 訂用帳戶會用於目的地帳單帳戶中的信用額度。 例如，如果 Bob 將 Visual Studio Enterprise 訂用帳戶轉移給 Jane 的帳戶在 9 年 9 月和 Jane 接受轉移。 移轉完成之後，會啟動訂用帳戶，使用 Jane 的帳戶中的信用額度。 信用額度將會重設的每個月 9 日。 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Enterprise 合約 (EA) 訂用帳戶的帳單擁有權轉移

企業系統管理員可以註冊的帳戶之間傳輸訂用帳戶的擁有的權。 如需詳細資訊，請參閱 <<c0> [ 傳輸的帳戶擁有權](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)EA 入口網站中。

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>接受的帳單擁有權後的後續步驟

如果您已接受的 Azure 訂用帳戶帳單擁有權，我們建議您檢閱後續步驟：

1. 請檢閱並更新服務管理員、共同管理員和其他 RBAC 角色。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。
1. 更新與此訂用帳戶服務相關聯的認證，包括：
   1. 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱 [建立和上傳 Azure 的管理憑證](../cloud-services/cloud-services-certs-create.md)
   1. 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)
   1. 服務 (例如 Azure 虛擬機器) 的遠端存取認證。
1. 如果您正與合作夥伴協力作業，請考慮更新此訂用帳戶的合作夥伴 ID。 您可以在 [Azure 入口網站](https://portal.azure.com)更新合作夥伴識別碼。 如需詳細資訊，請參閱[的合作夥伴識別碼連結至您的 Azure 帳戶](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

使用下面所列的訂用帳戶類型在 Azure 入口網站中的訂用帳戶轉移。 目前傳輸不支援[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)或是[Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/)訂用帳戶。 如需因應措施，請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。 若要傳送其他訂用帳戶，像是[贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)支援計劃，或[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [透過 EA 入口網站](#EA)。

\*\* 只有註冊期間建立，Azure 網站上的帳戶。 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>常見問題集 (FAQ) 寄件者

這些常見問題集適用於使用者的 Azure 訂用帳戶帳單擁有權傳送給另一個帳戶。

### <a name="whoisaa"></a> 帳戶的計費系統管理員是誰？

計費管理員是有權管理帳戶的計費的人員。 他們已獲得授權存取上的計費[Azure 入口網站](https://portal.azure.com)及執行各種計費工作，像是建立訂用帳戶、 檢視和支付發票或更新付款方法。

若要識別您帳單的系統管理員的訂用帳戶，請使用下列步驟：

1. 請瀏覽[成本管理 + Azure 入口網站中的 [帳單] 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。
1. 選取 **訂用帳戶**左側窗格中。 根據您的存取權，您可能需要選取計費的範圍，然後選取**訂用帳戶**或**Azure 訂用帳戶**
1. 訂用帳戶頁面會列出所有您是計費管理員的訂用帳戶。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有項目都會移轉嗎？ 包括資源群組、VM、磁碟和其他執行中的服務？

您的資源，例如 Vm、 磁碟和網站傳輸到新的帳戶。 不過，如果您的帳戶轉移訂用帳戶中另一個 Azure AD 租用戶，任何[系統管理員角色](billing-add-change-azure-subscription-administrator.md)並[角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)訂用帳戶指派[不這麼做傳輸](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)。 此外，[應用程式註冊](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他租用戶特定服務不會移轉以及訂用帳戶。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>可以移轉擁有權至另一個國家/地區中的帳戶嗎？
不幸的是，跨國家/地區傳輸無法在 Azure 入口網站中執行。 您的訂用帳戶轉移國家/地區，跨[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>我是兩個帳戶的系統管理員。 可以訂用帳戶將從傳輸其中一個我帳戶到另一個嗎？
是，您可以在您的帳戶之間傳輸的訂用帳戶。 因此您可以使用上述步驟，將您的帳戶之間的訂用帳戶，您的帳戶在概念上會被視為兩個不同的使用者帳戶。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>移轉訂閱會造成服務中斷嗎？

如果您的訂用帳戶移轉至相同的 Azure AD 租用戶中的使用者時，就不會影響到執行訂用帳戶中的資源。  不過，如果您的訂用帳戶轉移給使用者，以在另一個租用戶，所有的使用者、 群組和服務主體有[的角色型存取 (RBAC)](../role-based-access-control/overview.md)來管理訂用帳戶中的資源會遺失其存取權。 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>接受者可以存取使用和帳單記錄嗎？

可用的收件者的唯一資訊是您的訂用帳戶的上個月的成本。 其餘的使用量及帳單記錄不會傳輸與訂用帳戶

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何將 Azure 訂閱的資料與服務移轉到新的訂閱？

如果您無法轉移訂用帳戶的擁有權，則可手動移轉您的資源。 請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>如果我移轉 Visual Studio 或 Microsoft 合作夥伴網路訂用帳戶，沒有我的點數會夾帶與新的帳戶在訂用帳戶嗎？

否，您的信用額度不適用於新的帳戶。 接受傳輸要求的使用者必須擁有 Visual Studio 的授權，才能接受傳輸要求。 訂用帳戶會使用所提供的使用者帳戶的 Visual Studio 信用額度。 如需詳細資訊，請參閱[傳輸的 Visual Studio、 Microsoft 合作夥伴網路 (MPN) 和付開發/測試訂用帳戶](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>常見問題集 (FAQ) 收件者

這些常見問題集適用於使用者進行從另一個帳戶的 Azure 訂用帳戶帳單擁有權。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>如果我接管另一個帳戶的訂用帳戶的帳單擁有權時，使用者時該怎麼辦帳戶繼續存取我的資源嗎？

如果訂用帳戶轉移到在相同的 Azure AD 租用戶、 所有使用者、 群組和有服務主體帳戶[的角色型存取 (RBAC)](../role-based-access-control/overview.md)來管理訂用帳戶中的資源會保留其存取權。 若要檢視具有訂用帳戶的 RBAC 存取權限的使用者，請使用下列步驟：

1. 請瀏覽[在 Azure 入口網站中的訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查，然後選取 訂用的帳戶**存取控制 (IAM)** 左側窗格中。
1. 選取 **角色指派**從頁面頂端。 角色指派頁面會列出所有訂用帳戶具有 RBAC 存取權的使用者。

如果訂用帳戶轉移到另一個 Azure AD 租用戶、 所有使用者、 群組和有服務主體中的帳戶[的角色型存取 (RBAC)](../role-based-access-control/overview.md)來管理訂用帳戶中的資源會遺失其存取權。 不過，即使它們不不再需要的 RBAC 存取權，他們仍可能透過一些安全性機制，包括訂用帳戶的存取：

* 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](../cloud-services/cloud-services-certs-create.md)。
* 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。
* 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果收件者需要限制，請將其資源的存取權，則應考慮更新與服務相關聯的任何密碼。 您可以使用下列步驟來更新大部分的資源：

  1. 登入 [Azure 入口網站](https://portal.azure.com)。
  2. 在 [中樞] 功能表中，選取 [所有資源]  。
  3. 選取資源。
  4. 在 [資源] 頁面中，按一下**設定**。 您可以在這裡檢視並更新現有的密碼。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>如果我在計費週期中途的訂用帳戶帳單擁有權，我沒有支付整個計費週期嗎？

您的帳戶會負責支付報告的傳輸及更新版本的任何使用量。 可能有某些使用量是在移轉之前發生，但在之後才報告。 使用量會包含您的帳戶的帳單中。

### <a name="can-i-use-a-different-payment-method"></a>我可以使用不同的付款方法嗎？

是。 同時接受轉移要求，您可以選取現有的付款方式，連結到您的帳戶，或加入新的付款方式。

## <a name="troubleshooting"></a>疑難排解

### <a id="no-button"></a> 為什麼看不到 [移轉訂用帳戶] 按鈕？

不幸的是，自助式訂用帳戶傳輸不適用於您的帳單帳戶。 目前，我們不支援傳送出在 Azure 入口網站中的 Enterprise 合約 (EA) 帳戶的帳單擁有權。 此外，透過 Microsoft 業務的工作時建立的 Microsoft 客戶合約帳戶不支援移轉的訂用帳戶帳單擁有權。 

### <a id="no-button"></a> 為什麼我的訂用帳戶輸入支援傳輸？ 

不幸的是，並非所有類型的訂用帳戶都支援帳單擁有權移轉。 若要檢視的訂用帳戶類型，可支援傳輸的清單，請參閱[支援的訂用帳戶類型](#supported-subscription-types)

### <a id="no-button"></a> 為什麼我會收到拒絕存取錯誤當我嘗試將訂用帳戶的帳單擁有權移轉呢？ 

如果您想要將 Microsoft Azure 方案的訂用帳戶，而且您沒有必要的權限，您會看到此錯誤。 若要傳輸 Microsoft Azure 方案訂用帳戶，您必須是擁有者或參與者的計費訂用帳戶的 [發票] 區段。 如需詳細資訊，請參閱 <<c0> [ 管理訂用帳戶發票區段](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 請檢閱並更新服務管理員、共同管理員和其他 RBAC 角色。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。
