---
title: 設定您的帳單帳戶，Microsoft 客戶合約-Azure |Microsoft Docs
description: 了解如何設定您的帳單帳戶，Microsoft 客戶協議。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 90f832319fa2343003af58bd99eb64c0cbd94dd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371722"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>設定您的帳單帳戶，Microsoft 客戶協議

如果您的 Enterprise 合約註冊已過期或即將到期，您可以登入 Microsoft 的客戶合約，更新您的註冊。 更新包含下列步驟：

1. 接受新的 Microsoft 客戶合約。 使用您的 Microsoft 欄位代表了解詳細資料，並接受新的協議。
2. 設定新的計費帳戶建立新的 Microsoft 客戶協議。

這篇文章說明您現有的計費，在安裝之後所做的變更，並逐步引導您安裝新的計費帳戶。

若要設定帳單帳戶，您必須從您的 Enterprise 合約註冊轉換 Azure 訂用帳戶的計費到新的帳戶。 安裝程式不會影響您的訂用帳戶中執行的 Azure 服務。 不過，它會變更您要管理您的訂用帳戶的計費方式。

- 而不是[EA 入口網站](https://ea.azure.com)，您將在管理您的 Azure 服務和計費[Azure 入口網站](https://portal.azure.com)。
- 您會收到每月的數位發票的費用。 您可以檢視和分析 Azure 成本管理 + 帳單 頁面的發票。
- 而不是部門和您的 Enterprise 合約註冊中的帳戶，您將使用的計費結構和範圍從新的帳戶來管理及組織您的計費方式。

啟動安裝程式之前，我們建議您下列作業：

- **了解新的計費帳戶**
  - 新的帳戶可簡化為貴組織的計費。 [取得新的計費帳戶的快速概觀](billing-mca-overview.md)
- **請確認您的存取，以完成設定**
  - 只有特定的系統管理權限的使用者才可以完成安裝。 檢查您是否俱備[完成安裝所需的存取](#access-required-to-complete-the-setup)。
- **了解您帳單的階層架構的變更**
  - 您新的計費帳戶的組織方式與您的 Enterprise 合約註冊。 [了解您帳單的階層，在新的帳戶中的變更](#understand-changes-to-your-billing-hierarchy)。
- **了解對您計費系統管理員的存取**
  - 從您的 Enterprise 合約註冊的系統管理員取得計費的範圍中的新帳戶的存取權。[了解其存取權變更](#understand-changes-to-your-billing-administrators-access)。
- **檢視 Enterprise 合約功能所取代的新帳戶**
  - 檢視 Enterprise 合約註冊會在新的帳戶中的功能所取代的功能。
- **檢視最常見的問題的解答**
  - 檢視[更多資訊](#additional-information)若要深入了解安裝程式。

## <a name="access-required-to-complete-the-setup"></a>若要完成安裝所需的存取

若要完成安裝程式，您需要下列存取：

- Microsoft 客戶合約已簽署時所建立的帳單設定檔的擁有者。 若要深入了解計費的設定檔，請參閱[了解計費的設定檔](billing-mca-overview.md#understand-billing-profiles)。

- 更新註冊的企業系統管理員。

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>如果您不是企業系統管理員註冊

您可以要求註冊企業系統管理員，以完成您的帳單帳戶設定。

1. 登入 Azure 入口網站中使用，當您登入 Microsoft 客戶合約傳送給您的電子郵件中的連結。

2. 如果您的組織中的其他人簽署合約，或您沒有電子郵件，請登入使用下列連結。 取代**enrollmentNumber**您已更新的 enterprise 合約註冊數目。

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. 選取您想要傳送要求的企業系統管理員。

   ![螢幕擷取畫面顯示邀請企業系統管理員](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. 選取 **傳送要求**。

   系統管理員會收到一封電子郵件以完成安裝程序的指示。

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>如果您不計費的設定檔的擁有者

使用者在您的組織中人員已簽署 Microsoft 客戶合約會新增為帳單的設定檔的擁有者。 要求使用者將您新增為擁有者，以便您可以完成安裝。  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>了解您帳單階層的變更

新的計費帳戶可簡化您的組織，同時可讓您增強計費與成本管理功能的計費。 下圖說明在新的計費帳戶計費的組織方式。

![Ea-mca-post-轉換-階層的映像](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. 您可以使用計費的帳戶來管理您的 Microsoft 客戶協議的計費。 若要深入了解帳單帳戶，請參閱[了解帳單帳戶](billing-mca-overview.md#understand-billing-account)。
2. 您可以使用計費的設定檔來為您的組織，類似於您的 Enterprise 合約註冊管理計費。 企業系統管理員會成為擁有者的帳單的設定檔。 若要深入了解計費的設定檔，請參閱[了解計費的設定檔](billing-mca-overview.md#understand-billing-profiles)。
3. 您可以使用 [發票] 區段來組織您根據需求，類似於您的 Enterprise 合約註冊中的部門的成本。 部門變成發票區段和部門系統管理員發揮各自的發票區段的擁有者。 若要深入了解發票區段，請參閱[了解發票區段](billing-mca-overview.md#understand-invoice-sections)。
4. 您的 Enterprise 合約中所建立的帳戶並不支援新的計費帳戶。 帳戶的訂用帳戶隸屬於其系所的個別發票區段。 帳戶擁有者可以建立和管理訂用帳戶，其 「 發票 」 章節。

## <a name="understand-changes-to-your-billing-administrators-access"></a>了解對您計費系統管理員的存取

根據其存取權，您的 Enterprise 合約註冊的計費系統管理員會取得新的帳戶上計費的範圍的存取權。 下表將說明在安裝期間存取的變更：

| 現有的角色 | Post 轉換角色 |
| --- | --- |
| **企業系統管理員 (讀取只 = [否])** | **-帳單設定檔擁有者** </br> 管理計費的設定檔上的所有項目 </br> - **發票上所有 「 發票 」 區段的區段擁有者** </br> 管理上的 「 發票 」 章節中的所有項目 |
| **企業系統管理員 (讀取只 = Yes)** | **-帳單設定檔讀者** </br> 唯讀檢視所有項目上的帳單帳戶</br>**發票區段的讀取器上所有 「 發票 」 一節**</br> 唯讀檢視所有項目上的 [發票] 區段|
| **部門系統管理員 (讀取只 = [否])** |**發票區段的擁有者針對其個別的部門建立的 [發票] 區段** </br>管理 [發票] 區段中的所有項目|
| **部門系統管理員 (讀取只 = Yes)**|**發票區段的讀取器上針對其個別的部門建立的 [發票] 區段**</br> 唯讀檢視所有項目上的 [發票] 區段|
| **帳戶擁有者** | **-Azure 訂用帳戶的建立者針對其個別的部門建立的 [發票] 區段** </br>  建立 Azure 訂用帳戶，其發票區段|

登入 Microsoft 客戶合約時，Azure Active Directory 租用戶會選取新的計費帳戶。 如果您的組織沒有租用戶，則會建立新的租用戶。 租用戶代表貴組織 Azure Active Directory 內。 組織中的全域租用戶系統管理員會使用租用戶管理的應用程式與您組織中的資料存取。

新的帳戶僅支援從租用戶登入 Microsoft 客戶合約時所選取的使用者。 如果您的 Enterprise 合約中的系統管理員權限的使用者是租用戶的一部分，它們會新的計費帳戶的存取權時進行安裝。 如果它們不屬於租用戶，他們將無法存取新的計費帳戶，除非您邀請他們。

當您邀請的使用者時，他們會以來賓使用者身分加入租用戶，並取得計費帳戶的存取權。 若要邀請的使用者，來賓存取權必須開啟租用戶。 如需詳細資訊，請參閱 <<c0> [ 控制 Azure Active Directory 中的來賓存取](https://docs.microsoft.com/en-us/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)。 如果來賓存取已關閉，請連絡全域系統管理員，您的租用戶將它開啟。 <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>檢視新的計費帳戶被取代的功能

以下的 Enterprise 合約功能會取代 Microsoft 的客戶合約中的計費帳戶的新功能。

### <a name="enterprise-agreement-accounts"></a>Enterprise 合約的帳戶

在您的 Enterprise 合約註冊中建立的帳戶並不支援新的計費帳戶。 帳戶的訂用帳戶屬於針對其個別的部門建立的 [發票] 區段。 帳戶擁有者成為 Azure 訂用帳戶的建立者，以及可以建立及管理訂用帳戶，其 「 發票 」 章節。

### <a name="notification-contacts"></a>通知連絡人

通知連絡人傳送電子郵件通訊，有關 Azure Enterprise 合約。 新的計費帳戶不支援它們。 有關 Azure 信用額度和發票的電子郵件會傳送至您的帳單帳戶中具有計費的設定檔存取權的使用者。

### <a name="spending-quotas"></a>消費配額

設定您的 Enterprise 合約註冊中的部門消費配額會取代在新的計費帳戶中的預算。 預算會建立每一個設定對您的註冊中的部門的消費配額。 如需有關預算的詳細資訊，請參閱[建立和管理 Azure 的預算](../cost-management/manage-budgets.md)。

### <a name="cost-centers"></a>成本的中心

在新的計費帳戶會保留在您的 Enterprise 合約註冊 Azure 訂用帳戶設定的成本的中心。 不過，不支援針對部門和 Enterprise 合約帳戶的成本中心。

## <a name="additional-information"></a>其他資訊

下列各節提供有關設定您的帳單帳戶的其他資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

您訂用帳戶中的 Azure 服務可以持續執行，不會發生任何中斷。 我們僅會轉換您 Azure 訂用帳戶的帳務關係， 而不會影響現有資源、資源群組或管理群組。

### <a name="user-access-to-azure-resources"></a>使用者存取 Azure 資源

在轉換期間，不會影響存取 Azure 資源所設定使用 Azure RBAC （角色型存取控制）。

### <a name="azure-reservations"></a>Azure 保留

您的 Enterprise 合約註冊中的任何 Azure 保留項目會移至新的計費帳戶。 在轉換期間，套用至您訂用帳戶的保留折扣不會有任何變更。

### <a name="azure-marketplace-products"></a>Azure Marketplace 產品

Enterprise 合約註冊的任何 Azure Marketplace 產品會隨著訂用帳戶。 在轉換期間，將不會有 Marketplace 產品的服務存取的任何變更。

### <a name="support-plan"></a>支援計劃

當轉換不會移轉支援權益。 購買新的支援方案，才能取得新的計費帳戶中的 Azure 訂用帳戶權益。

### <a name="past-charges-and-balance"></a>過去的費用，並平衡

在您的 Enterprise 合約註冊，透過 Azure 入口網站中，您可以檢視在轉換之前的費用和信用額度餘額。 <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>何時完成安裝程式？

您的 Enterprise 合約註冊到期之前，請完成您的帳單帳戶設定。 如果您註冊到期時，您 Azure 訂用帳戶中的服務將仍保留在執行不中斷。 不過，您將需要支付服務的零售費率計算。

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>Enterprise 合約註冊，請在安裝之後的變更

Azure 訂用帳戶轉換可以手動移到新的計費帳戶之後，針對 Enterprise 合約註冊所建立。 如需詳細資訊，請參閱 <<c0> [ 取得帳單的其他使用者的 Azure 訂用帳戶的擁有權](billing-mca-request-billing-ownership.md)。 若要移動的轉換之後，購買 Azure 保留項目[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 您也可以提供使用者計費帳戶的存取權後轉換。 如需詳細資訊，請參閱[管理在 Azure 入口網站中的計費角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>還原轉換

無法還原轉換。 一旦您的 Azure 訂用帳戶的計費轉換至新的計費帳戶，您無法回到您的 Enterprise 合約註冊來進行還原。

### <a name="closing-your-browser-during-setup"></a>在安裝期間關閉您的瀏覽器

您按一下前**啟動轉換**，您可以關閉瀏覽器。 您可以回到安裝程式，使用您在電子郵件中取得的連結，並開始轉換。 如果您關閉瀏覽器中，啟動轉換後，將會繼續在執行轉換。 返回 [轉換狀態] 頁面，來監視您的轉換的最新狀態。 當轉換完成時，您會收到一封電子郵件。

## <a name="complete-the-setup-in-the-azure-portal"></a>完成在 Azure 入口網站中設定

若要完成安裝程式，您需要存取新的計費帳戶和 Enterprise 合約註冊。 如需詳細資訊，請參閱 <<c0> [ 所需完成設定您的帳單帳戶的存取權](#access-required-to-complete-the-setup)。

1. 登入 Azure 入口網站中使用，當您登入 Microsoft 客戶合約傳送給您的電子郵件中的連結。

2. 如果您的組織中的其他人簽署合約，或您沒有電子郵件，請登入使用下列連結。 取代**enrollmentNumber**您已更新的 Enterprise 合約註冊數目。

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. 選取 **啟動轉換**中設定的最後一個步驟。 一旦您選取開始轉換：

    ![顯示安裝精靈的螢幕擷取畫面](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - 計費的階層對應至您的 Enterprise 合約階層會建立新的計費帳戶中。 如需詳細資訊，請參閱 <<c0> [ 了解您帳單的階層變更](#understand-changes-to-your-billing-hierarchy)。
    - 從您的 Enterprise 合約註冊的系統管理員會將存取權提供給新的計費帳戶，以便繼續使用來管理貴組織的計費。
    - 您的 Azure 訂用帳戶的計費轉換至新的帳戶。 **不會有任何影響 Azure 服務上這項轉換期間。它們會繼續執行不會中斷**。
    - 如果您有 Azure 保留項目時，它們會移到新的計費帳戶具有相同的折扣和詞彙。 您的保留折扣會套用在轉換期間繼續。

4. 您可以在監視的轉換狀態**轉換狀態**頁面。

   ![顯示的轉換狀態的螢幕擷取畫面](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>驗證的計費帳戶已正確設定

 驗證以確保您新的計費帳戶已正確設定下列各項：

### <a name="azure-subscriptions"></a>Azure 訂用帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 選取計費的設定檔。 您可能需要根據存取權來選取帳單帳戶。 從計費的帳戶中，選取**帳單設定檔**，然後計費的設定檔。

4. 選取  **Azure 訂用帳戶**從左邊算起。

   ![顯示訂用帳戶清單的螢幕擷取畫面](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

在 Azure 訂用帳戶 頁面上，會顯示已從您的 Enterprise 合約註冊轉換到新的計費帳戶的 azure 訂用帳戶。 如果您認為缺少任何訂用帳戶，轉換以手動方式在 Azure 入口網站中的訂用帳戶的計費。 如需詳細資訊，請參閱[取得帳單的其他使用者的 Azure 訂用帳戶的擁有權](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure 保留

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 選取 [發票] 區段。 您可能需要根據存取權來選取帳單帳戶或帳單設定檔。  從計費的帳戶或計費的設定檔中，選取**發票區段**，然後發票 > 一節。

    ![顯示的發票區段 post 轉換清單的螢幕擷取畫面](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. 選取 **所有產品**從左邊算起。

5. 搜尋**保留**。

    ![顯示的訂用帳戶後的轉換清單的螢幕擷取畫面](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

從您的 Enterprise 合約註冊移到新的計費帳戶的 azure 保留項目會顯示在 [所有產品] 頁面。 重複確認從您的 Enterprise 合約註冊，會移動所有的 Azure 保留的所有發票區段的步驟。 如果您認為任何 Azure 保留項目遺漏[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)將此保留項目移至新的計費帳戶。

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>企業系統管理員的計費的設定檔的存取權

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 選取的計費的設定檔，建立您的註冊。 您可能需要根據存取權來選取帳單帳戶。  從計費的帳戶中，選取**帳單設定檔**，然後計費的設定檔。

4. 選取 **存取控制 (IAM)** 從左邊算起。

   ![顯示存取企業系統管理員 post 轉換的螢幕擷取畫面](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

企業系統管理員會列為帳單設定檔擁有者，同時具有唯讀權限的系統管理員會列出的企業帳單設定檔讀取器。 如果您認為缺少任何企業系統管理員的存取權，您可以授與他們存取的 Azure 入口網站。 如需詳細資訊，請參閱 <<c0> [ 管理 Azure 入口網站中的計費角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>企業系統管理員、 部門管理員和發票區段的帳戶擁有者的存取

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)上也提供本文中使用的原始碼。

3. 選取 [發票] 區段。 發票的各節會將相同的名稱，為其個別的部門在 Enterprise 合約註冊。 根據您的存取權，您可能需要選取 計費的設定檔 或 計費帳戶。 從計費的設定檔或帳單帳戶，選取**發票區段**，然後選取 [發票] 區段。

   ![顯示的發票區段 post 轉換清單的螢幕擷取畫面](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. 選取 **存取控制 (IAM)** 從左邊算起。

    ![顯示的部門和系統管理員存取 post 轉換的帳戶存取的螢幕擷取畫面](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

企業系統管理員和部門系統管理員會列為發票區段的擁有者或發票區段讀取器時部門中的帳戶擁有者列為 Azure 訂用帳戶的建立者。 重複步驟，檢查您的 Enterprise 合約註冊中的所有部門的存取權的所有發票區段。 不屬於任何部門的帳戶擁有者會取得名為 [發票] 區段上的權限**發票的預設區段**。 如果您認為缺少任何系統管理員的存取權，您可以授與他們存取的 Azure 入口網站。 如需詳細資訊，請參閱 <<c0> [ 管理 Azure 入口網站中的計費角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- [開始使用新的計費帳戶](billing-mca-overview.md)

- [Microsoft 客戶合約完成您的帳單帳戶中的 Enterprise 合約工作](billing-mca-enterprise-operations.md)

- [管理您的帳單帳戶的存取](billing-understand-mca-roles.md)
