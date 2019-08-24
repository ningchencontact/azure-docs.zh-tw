---
title: 將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶 |Microsoft Docs
description: 說明如何將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶, 以及有關此程式的一些常見問題 (FAQ)
keywords: 轉移 azure 訂用帳戶、azure 轉移訂用帳戶、將 azure 訂用帳戶移至另一個帳戶、azure 變更訂用帳戶擁有者、將 azure 訂用帳戶轉移至另一個帳戶
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
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012522"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>將 Azure 訂用帳戶的帳單擁有權轉移給另一個帳戶

如果您離開組織, 或想要向另一個帳戶收取您的訂用帳戶, 您可能會想要轉移 Azure 訂用帳戶的帳單擁有權。 將帳單擁有權轉移至另一個帳戶, 會提供新帳戶的系統管理員執行計費工作的許可權, 例如變更付款條件、查看費用, 以及取消訂閱。

如果您想要保留帳單擁有權, 但要變更訂用帳戶的類型, 請參閱將[您的 Azure 訂用帳戶切換至其他供應](billing-how-to-switch-azure-offer.md)專案。 如果您想要控制誰可以管理訂用帳戶中的資源, 請參閱[Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

如果您是 Enterprise 合約 (EA) 客戶, 您的企業系統管理員可以在帳戶之間轉移訂用帳戶的帳單擁有權。 如需詳細資訊, 請參閱[轉讓 Enterprise 合約 (EA) 訂用帳戶的帳單擁有權](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions)。

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>轉移 Azure 訂用帳戶的帳單擁有權

1. 以帳單帳戶的系統管理員身分, 登入您想要轉移的訂閱[Azure 入口網站](https://portal.azure.com)。 若要找出您是否為系統管理員, 請參閱[常見問題](#faq)。

1. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 從左側窗格中選取 [**訂閱**]。 根據您的存取權, 您可能需要選取計費範圍, 然後選取 [訂用帳戶] 或 [ **Azure**訂用帳戶]。

1. 針對您要轉移的訂用帳戶, 選取 [**傳送帳單擁有權**]。 

   ![選取要傳送的訂用帳戶](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 輸入使用者的電子郵件地址, 這是將成為訂用帳戶新擁有者之帳戶的帳單管理員。

1. 如果您要將訂用帳戶轉移至另一個 Azure AD 租使用者中的帳戶, 請選取是否要將訂用帳戶移至新帳戶的租使用者。 如需詳細資訊, 請參閱[將訂用帳戶轉移至另一個 Azure AD 租使用者中的帳戶](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)

    > [!IMPORTANT]
    >
    > 如果您選擇將訂閱移至新帳戶的 Azure AD 租使用者, 則會永久移除所有用來管理訂用帳戶中資源的[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)指派。 只有在新帳戶中, 接受您的傳輸要求的使用者才有權管理訂用帳戶中的資源。 如需詳細資訊, 請參閱將[訂用帳戶轉移給另一個 Azure AD 租使用者中的使用者](../active-directory/managed-identities-azure-resources/known-issues.md)。 或者, 您可以取消核取 [訂用帳戶 Azure AD 租使用者] 的方塊, 以轉移帳單擁有權, 而不將訂用帳戶移至新帳戶的租使用者 如果您這樣做, 將會維護現有的 RBAC 許可權來管理 Azure 資源。
  
    ![傳送傳輸頁面](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. 選取 [**傳送傳輸要求**]。

1. 使用者會收到一封電子郵件, 其中包含用來檢查您的傳輸要求的指示。

   ![傳送給收件者的訂用帳戶傳送電子郵件](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 若要核准傳輸要求, 使用者可以選取電子郵件中的連結, 並遵循指示進行。 使用者必須選取將用來支付訂用帳戶的付款方法。 此外, 如果使用者沒有 Azure 帳戶, 他們就必須註冊新帳戶。 

   ![第一個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 成功！ 現在已移轉訂用帳戶。

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>將訂用帳戶轉移至另一個 Azure AD 租使用者中的帳戶

當您註冊 Azure 時, 系統會為您建立 Azure Active Directory (AD) 租使用者。 租使用者代表您的帳戶。 您可以使用租使用者來管理訂用帳戶和資源的存取權。

當您建立新的訂用帳戶時, 它會裝載在您帳戶的 Azure AD 租使用者中。 如果您想要將訂用帳戶或其資源的存取權提供給其他人, 則必須邀請他們加入您的租使用者。 這可協助您控制訂用帳戶和資源的存取權。

當您將訂用帳戶的帳單擁有權轉移至另一個 Azure AD 租使用者中的帳戶時, 您可以將訂用帳戶移至新帳戶的租使用者。 如果您這樣做, 所有具有[角色型存取 (RBAC)](../role-based-access-control/role-assignments-portal.md)來管理訂用帳戶及其資源的使用者、群組或服務主體都會失去其存取權。 只有在新帳戶中, 接受您的傳輸要求的使用者才有權管理資源。 若要提供存取權給原本擁有存取權的使用者, 新的擁有者必須[手動將這些使用者新增至訂](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)用帳戶。


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>傳輸 Visual Studio、MPN 及隨用隨付開發/測試訂閱

Visual Studio 和 Microsoft 合作夥伴網路訂用帳戶都有相關聯的每月週期性 Azure 點數。 當您傳輸這些訂用帳戶時, 您的信用額度無法在目的地帳單帳戶中使用。 訂用帳戶會使用目的地帳單帳戶中的點數。 例如, 如果 Bob 將 Visual Studio Enterprise 訂用帳戶轉移至 Jane 在9月9日的帳戶, 而 Jane 接受轉移。 完成傳送之後, 訂用帳戶會開始在 Jane 的帳戶中使用點數。 點數將會在每個月第9次重設。 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>轉移 Enterprise 合約 (EA) 訂用帳戶的帳單擁有權

企業系統管理員可以在註冊內的帳戶之間轉移訂閱的擁有權。 如需詳細資訊, 請參閱 EA 入口網站中的[轉移帳戶擁有權](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。

## <a name="next-steps-after-accepting-billing-ownership"></a>接受帳單擁有權後的後續步驟

如果您已接受 Azure 訂用帳戶的帳單擁有權, 建議您參閱下列後續步驟:

1. 請檢閱並更新服務管理員、共同管理員和其他 RBAC 角色。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。
1. 更新與此訂用帳戶服務相關聯的認證，包括：
   1. 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱 [建立和上傳 Azure 的管理憑證](../cloud-services/cloud-services-certs-create.md)
   1. 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)
   1. 服務 (例如 Azure 虛擬機器) 的遠端存取認證。
1. 如果您正與合作夥伴協力作業，請考慮更新此訂用帳戶的合作夥伴 ID。 您可以在 [Azure 入口網站](https://portal.azure.com)更新合作夥伴識別碼。 如需詳細資訊, 請參閱將[合作夥伴識別碼連結至您的 Azure 帳戶](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

Azure 入口網站中的訂用帳戶轉移適用于下列訂用帳戶類型。 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)或[Azure in Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/)訂閱目前不支援傳輸。 如需因應措施，請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。 若要轉移其他訂用帳戶, 例如[贊助](https://azure.microsoft.com/offers/ms-azr-0036p/)或支援方案, 請[聯絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 企業版](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 計畫](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*透過[EA 入口網站](#EA)。

\*\*僅支援在 Azure 網站註冊期間建立的帳戶。 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>寄件者的常見問題 (FAQ)

這些常見問題適用于將 Azure 訂用帳戶的帳單擁有權轉移至另一個帳戶的使用者。

### <a name="whoisaa"></a>帳戶的帳單管理員是誰？

計費管理員是有權管理帳戶帳單的人員。 他們已獲授權存取[Azure 入口網站](https://portal.azure.com)的帳單, 並執行各種計費工作, 例如建立訂用帳戶、查看和支付發票, 或更新付款條件。

若要識別您是帳單系統管理員的帳戶, 請使用下列步驟:

1. 流覽[Azure 入口網站中的成本管理 + 帳單 頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)。
1. 從左側窗格中選取 [**所有計費範圍**]。 
1. [訂用帳戶] 頁面會列出您是帳單管理員的所有訂閱。

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>所有項目都會移轉嗎？ 包括資源群組、VM、磁碟和其他執行中的服務？

您的所有資源 (例如 Vm、磁片和網站) 都會傳輸到新的帳戶。 不過, 如果您將訂用帳戶轉移到另一個 Azure AD 租使用者中的帳戶, 則[不會轉移](#transferring-subscription-to-an-account-in-another-azure-ad-tenant)訂用帳戶上的任何[系統管理員角色](billing-add-change-azure-subscription-administrator.md)和以[角色為基礎的存取控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)指派。 此外,[應用程式註冊](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)和其他租使用者特定服務也不會與訂用帳戶一起傳輸。

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>我可以將擁有權轉移給另一個國家/地區的帳戶嗎？
可惜的是, 跨國家/地區的轉移無法在 Azure 入口網站中執行。 若要跨國家/地區轉移訂用帳戶, 請[聯絡支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)人員。

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>我是兩個帳戶的系統管理員。 我可以將訂用帳戶從我的帳戶轉移到另一個帳戶嗎？
是, 您可以在您的帳戶之間傳輸訂閱。 您的帳戶在概念上會視為兩個不同使用者的帳戶, 因此您可以使用上述步驟, 在您的帳戶之間傳輸訂閱。

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>移轉訂閱會造成服務中斷嗎？

如果您將訂用帳戶轉移至相同 Azure AD 租使用者中的帳戶, 則不會影響在訂用帳戶中執行的資源。 不過, 如果您將訂用帳戶轉移到另一個租使用者中的帳戶, 並決定將訂用帳戶移至租使用者, 則擁有角色型[存取 (RBAC)](../role-based-access-control/overview.md)來管理訂用帳戶中資源的所有使用者、群組和服務主體都會失去其存取權. 這可能會導致服務停機。

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>新帳戶中的使用者是否可存取使用量和帳單歷程記錄？

新帳戶中的使用者唯一可用的資訊是您訂閱的上個月成本。 其餘的使用量和帳單記錄不會與訂用帳戶一起傳輸

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>如何將 Azure 訂閱的資料與服務移轉到新的訂閱？

如果您無法轉移訂用帳戶的擁有權，則可手動移轉您的資源。 請參閱[將資源移動到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>如果我轉移 Visual Studio 或 Microsoft 合作夥伴網路訂用帳戶, 我的點數是否會與新帳戶中的訂閱一起執行？

否, 您的點數在新帳戶中無法使用。 接受傳輸要求的使用者必須具有 Visual Studio 授權, 才能接受傳輸要求。 訂閱會使用使用者帳戶中可用的 Visual Studio 點數。 如需詳細資訊, 請參閱[傳輸 Visual Studio、Microsoft 合作夥伴網路 (MPN) 和隨用隨付開發/測試訂閱](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions)。


## <a name="frequently-asked-questions-faq-for-recipients"></a>收件者常見問題 (FAQ)

這些常見問題適用于接受來自另一個帳戶的 Azure 訂用帳戶帳單擁有權的使用者。

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>如果我接管另一個帳戶的訂用帳戶帳單擁有權, 該帳戶中的使用者是否可繼續存取我的資源？

是的。 不過, 如果您的帳戶位於與訂用帳戶租使用者不同的 Azure AD 租使用者中, 且傳送要求的使用者會將訂用帳戶移至您帳戶的租使用者, 則任何[系統管理員角色](billing-add-change-azure-subscription-administrator.md)和以[角色為基礎的存取控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)指派已移除。 若要查看具有[角色型存取 (RBAC)](../role-based-access-control/overview.md)存取權以管理訂用帳戶中資源的使用者, 請使用下列步驟:

1. 流覽[Azure 入口網站中的訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查的訂用帳戶, 然後從左側窗格中選取 [**存取控制 (IAM)** ]。
1. 從頁面頂端選取 [**角色指派**]。 [角色指派] 頁面會列出在訂用帳戶上具有 RBAC 存取權的所有使用者。

即使在傳輸期間移除以[角色為基礎的存取控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)指派, 原始擁有者帳戶中的使用者仍然可以透過一些安全性機制來存取訂用帳戶, 包括:

* 可將使用者管理權限授與給訂用帳戶資源的管理憑證。 如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](../cloud-services/cloud-services-certs-create.md)。
* 服務 (例如儲存體) 的存取金鑰。 如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)。
* 服務 (例如 Azure 虛擬機器) 的遠端存取認證。

如果收件者需要限制、存取其資源, 他們應該考慮更新與服務相關聯的任何秘密。 您可以使用下列步驟來更新大部分的資源：

  1. 登入 [Azure 入口網站](https://portal.azure.com)。
  2. 在 [中樞] 功能表中，選取 [所有資源]。
  3. 選取資源。
  4. 在資源頁面中, 按一下 [**設定**]。 您可以在這裡檢視並更新現有的密碼。

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>如果我在計費週期中接管訂用帳戶的帳單擁有權, 是否必須支付整個計費週期的費用？

您的帳戶須負責支付從傳輸開始時所回報的任何使用量。 可能有某些使用量是在移轉之前發生，但在之後才報告。 使用量會包含在您帳戶的帳單中。

### <a name="can-i-use-a-different-payment-method"></a>我可以使用不同的付款方法嗎？

是的。 接受傳輸要求時, 您可以選取連結至您帳戶的現有付款條件, 或新增付款條件。

## <a name="troubleshooting"></a>疑難排解

### <a id="no-button"></a> 為什麼看不到 [移轉訂用帳戶] 按鈕？

您的帳單帳戶無法使用自助式訂閱傳送。 目前, 我們不支援在 Azure 入口網站中, 以 Enterprise 合約 (EA) 帳戶來傳送訂閱的帳單擁有權。 此外, 使用 Microsoft 代表時所建立的 Microsoft 客戶合約帳戶不支援傳送帳單擁有權。 

### <a id="no-button"></a>為什麼我的訂用帳戶類型不支援轉移？ 

並非所有類型的訂閱都支援帳單擁有權傳輸。 若要查看支援傳輸的訂用帳戶類型清單, 請參閱[支援的訂](#supported-subscription-types)用帳戶類型

### <a id="no-button"></a>當我嘗試轉移訂用帳戶的帳單擁有權時, 為什麼會收到拒絕存取的錯誤？ 

如果您嘗試轉移 Microsoft Azure 方案訂用帳戶, 但沒有必要的許可權, 就會看到此錯誤。 若要傳送 Microsoft Azure 方案訂用帳戶, 您必須是訂用帳戶計費所在發票區段上的擁有者或參與者。 如需詳細資訊, 請參閱[管理發票的訂閱一節](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section)。


## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助, 請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

- 請檢閱並更新服務管理員、共同管理員和其他 RBAC 角色。 若要深入了解，請參閱[新增或變更 Azure 訂用帳戶管理員](billing-add-change-azure-subscription-administrator.md)和[使用 RBAC 和 Azure 入口網站來管理存取權](../role-based-access-control/role-assignments-portal.md)。
