---
title: 取得 Azure 訂用帳戶的帳單擁有權
description: 了解如何向其他使用者要求 Azure 訂用帳戶的帳單擁有權。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "69019564"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>從其他帳戶接管 Azure 訂用帳戶的帳單擁有權

如果現有的帳單擁有者離開您的組織，或您想要透過計費帳戶支付訂用帳戶費用，您可以接管 Azure 訂用帳戶的擁有權。 取得擁有權後，訂用帳戶的計費責任會轉移到您的帳戶。

本文適用於 Microsoft 客戶合約的計費帳戶。 請[確認您是否有 Microsoft 客戶合約的存取權](#check-for-access)。

若要要求帳單擁有權，您必須是**發票區段擁有者**或**發票區段參與者**。 若要深入了解，請參閱[發票區段角色工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="request-billing-ownership"></a>要求帳單擁有權

1. 以發票區段擁有者或參與者的身分登入 [Azure 入口網站](https://portal.azure.com)，以存取 Microsoft 客戶合約的計費帳戶。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 從左側選取 [發票區段]  。 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔。 從計費帳戶或帳單設定檔中選取 [發票區段]  。
   
   ![顯示選取發票區段的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 從清單中選取發票區段。 在您接管訂用帳戶的擁有權後，其費用就會計入此發票區段。

5. 從左下方選取 [轉移要求]  ，然後選取 [新增]  。
 
   ![顯示選取轉移要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 輸入您要對其要求帳單擁有權之使用者的電子郵件地址。 這個使用者必須是 Microsoft 線上服務方案計費帳戶的帳戶管理員，或是Enterprise 合約的帳戶擁有者。 如需詳細資訊，請參閱[在 Azure 入口網站中檢視計費帳戶](billing-view-all-accounts.md)。 選取 [傳送轉移要求]  。

   ![顯示傳送轉移要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 使用者會收到一封電子郵件，內含用來檢閱轉移要求的指示。

   ![顯示檢閱轉移要求電子郵件的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 若要核准轉移要求，使用者須選取電子郵件中的連結，並遵循相關指示。

    ![顯示檢閱轉移要求電子郵件的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>查看轉移要求狀態

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋 [成本管理 + 帳單]  。

   ![顯示在 Azure 入口網站中搜尋 [成本管理 + 帳單] 的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 從左側選取 [發票區段]  。 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔。 從計費帳戶或帳單設定檔中選取 [發票區段]  。
   
   ![顯示選取發票區段的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 從清單中選取您為其傳送轉移要求的發票區段。

5. 從左下方選取 [轉移要求]  。 [轉移要求] 頁面會顯示下列資訊：

    ![顯示轉移要求清單的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |欄|定義|
   |---------|---------|
   |要求日期|轉移要求的傳送日期|
   |收件者|您傳送轉移帳單擁有權要求的收件方電子郵件地址|
   |到期日期|要求的到期日|
   |狀態|轉移要求的狀態|

    轉移要求可處於下列其中一種狀態：

   |狀態|定義|
   |---------|---------|
   |進行中|使用者尚未接受轉移要求|
   |Processing|使用者已核准轉移要求。 使用者所選取的訂用帳戶正在將其帳單移轉至您的發票區段|
   |Completed| 使用者所選取的訂用帳戶已將其帳單轉移至您的發票區段|
   |作業完成，但發生錯誤|要求已完成，但使用者選取的部分訂用帳戶無法轉移其帳單|
   |已過期|使用者未及時接受要求，且已過期|
   |Canceled|具有轉移要求存取權的人員已取消要求|
   |已拒絕|使用者已拒絕轉移要求|

7. 選取轉移要求以檢視詳細資料。 [轉移詳細資料] 頁面會顯示下列資訊：
   
   ![顯示已轉移訂用帳戶清單的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |欄  |定義|
   |---------|---------|
   |轉移要求識別碼|轉移要求的唯一識別碼。 如果您提交支援要求，請將識別碼提供給 Azure 支援，以加速支援要求的處理|
   |要求轉移時間|轉移要求的傳送日期|
   |要求轉移者|傳送轉移要求的使用者電子郵件地址|
   |轉移要求到期時間| 轉移要求的到期日|
   |收件者的電子郵件地址|您傳送轉移帳單擁有權要求的收件方電子郵件地址|
   |傳送給收件者的轉移連結|傳送給使用者供其檢閱轉移要求的 URL|

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

您可以要求下列訂用帳戶類型的帳單擁有權。

- [行動套件](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open 授權](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 贊助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 贊助的供應項目](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/) \(英文\)
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 訂閱者](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 訂閱者](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* 在轉移之後，訂用帳戶上任何可用的點數在新帳戶中都將無法使用。

\*\* 僅支援在 Azure 網站註冊期間在帳戶中建立的訂用帳戶。


## <a name="additional-information"></a>其他資訊

下一節將提供其他有關於轉移訂用帳戶的資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

訂用帳戶中的 Azure 服務可以持續執行，不會發生任何中斷。 我們只會轉換使用者選擇要轉移的 Azure 訂用帳戶的帳務關係。

### <a name="disabled-subscriptions"></a>已停用訂用帳戶

已停用的訂用帳戶無法轉移。 訂用帳戶必須處於作用中狀態，才能轉移其帳單擁有權。

### <a name="azure-resources-transfer"></a>Azure 資源轉移

訂用帳戶中的所有資源 (例如 VM、磁碟和網站) 都會轉移。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 產品轉移

Azure Marketplace 產品會連同其各自的訂用帳戶一起轉移。

### <a name="azure-reservations-transfer"></a>Azure 保留轉移

Azure 保留不會自動隨著訂用帳戶轉移。 若要移轉保留，請[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="access-to-azure-services"></a>對 Azure 服務的存取

使用 Azure RBAC (角色型存取控制) [../role-based-access-control/overview.md] 為現有使用者、群組或服務主體指派的存取權，在轉換期間不受影響。

### <a name="azure-support-plan"></a>Azure 支援方案

Azure 支援不會隨著訂用帳戶一起轉移。 如果使用者轉移了所有 Azure 訂用帳戶，請要求他們取消其支援方案。

### <a name="charges-for-transferred-subscription"></a>已轉移的訂用帳戶費用

訂用帳戶的原始帳單擁有者須負責處理計算到轉移完成為止的任何費用。 您的發票區段會處理轉移後所報告的費用。 可能有某些費用是在轉移之前產生，但在之後才報告。 這些費用會顯示在您的發票區段上。

### <a name="cancel-a-transfer-request"></a>取消轉移要求

轉移要求獲得核准或遭到拒絕之前，您可以取消該要求。 若要取消轉移要求，請移至[轉移詳細資料頁面](#check-the-transfer-request-status)，然後從頁面底部選取 [取消]。

### <a name="software-as-a-service-saas-transfer"></a>軟體即服務 (SaaS) 轉移

SaaS 產品不會隨著訂用帳戶一起轉移。 請要求使用者[連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以轉移 SaaS 產品的帳單擁有權。 除了帳單擁有權之外，使用者也可以轉移資源擁有權。 資源擁有權可讓您執行管理作業，例如刪除和檢視產品的詳細資料。 使用者必須是 SaaS 產品的資源擁有者，才能轉移資源擁有權。

## <a name="check-for-access"></a>檢查存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- Azure 訂用帳戶的帳單擁有權已轉移至您的發票區段。 請在 [Azure入口網站](https://portal.azure.com)中追蹤這些訂用帳戶的費用。
- 授與他人對這些訂用帳戶檢視和管理帳單的權限。 如需詳細資訊，請參閱[發票區段角色和工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
