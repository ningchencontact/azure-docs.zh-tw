---
title: 取得計費的 Azure 訂用帳戶的擁有權 |Microsoft Docs
description: 了解如何要求其他使用者的 Azure 訂用帳戶帳單擁有權。
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
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371879"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>取得帳單其他使用者的 Azure 訂用帳戶的擁有的權

您可能想要取得 Azure 訂用帳戶的擁有權，如果現有的計費擁有者離開您的組織，或您想要支付的訂用帳戶，透過您的帳單帳戶。

您可以傳送要求以從其他計費的帳戶中的現有擁有者取得的 Azure 訂用帳戶的擁有權。 取得擁有權傳送至 [發票] 區段的計費的訂用帳戶的責任。

若要要求帳單擁有權，您必須是**發票區段擁有者**或是**發票區段參與者**。 若要進一步了解，請參閱[發票區段角色工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)。

## <a name="request-billing-ownership-in-the-azure-portal"></a>要求在 Azure 入口網站中的帳單擁有權

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 移至 [發票] 區段。 根據您的存取權，您可能需要選取 帳單帳戶或計費的設定檔。 從計費的帳戶或設定檔上，選取**發票區段**，然後發票 > 一節。
   <!-- Todo - Add a screenshot -->

4. 選取 **轉移要求**從左下方。

5. 從頁面頂端選取 [新增]。

6. 輸入您正在要求從的帳單擁有權的使用者電子郵件地址。 使用者必須是 Microsoft 線上服務方案的計費帳戶的帳戶系統管理員 」 或 「 Enterprise 合約的帳戶擁有者。 如需詳細資訊，請參閱 < [Azure 入口網站中檢視您的帳單帳戶](billing-view-all-accounts.md)。

   ![顯示如何加入新的傳輸要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. 選取 **傳送傳輸要求**。

8. 使用者會收到一封電子郵件以檢閱您的轉移要求的指示。

   ![螢幕擷取畫面，顯示檢閱傳輸要求的電子郵件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. 若要核准的轉移要求，使用者的電子郵件中選取的連結，並遵循指示進行。

    ![螢幕擷取畫面，顯示檢閱傳輸要求的電子郵件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>檢查您的轉移要求，在 Azure 入口網站中的狀態

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 移至 [發票] 區段。 根據您的存取權，您可能需要選取 帳單帳戶或計費的設定檔。 從計費的帳戶或設定檔上，選取**發票區段**，然後發票 > 一節。
   <!-- Todo - Add a screenshot -->

4. 選取 **轉移要求**從左下方。

5. 傳輸要求頁面會顯示下列資訊：

    ![顯示清單中的傳輸要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |欄|定義|
   |---------|---------|
   |要求日期|當傳輸要求已送出的日期|
   |收件者|您傳送要求以轉移帳單擁有權之使用者的電子郵件地址|
   |到期日期|要求的到期日期|
   |狀態|傳送要求的狀態|

    移轉要求可以有下列狀態之一：

   |狀態|定義|
   |---------|---------|
   |進行中|使用者尚未接受轉移要求|
   |Processing|使用者核准，傳送要求。 使用者選取的訂用帳戶的計費正在傳輸至 [發票] 區段|
   |Completed| 使用者選取的訂用帳戶的計費會轉移至您發票的區段|
   |完成但發生錯誤|無法轉移要求已完成，但部分使用者選取的訂用帳戶的計費|
   |已過期|使用者未在時間上接受要求，而且過期|
   |Canceled|其他人的存取權，傳送要求取消要求|
   |已拒絕|使用者已拒絕傳送要求|

6. 選取 傳送要求以檢視詳細資料。 傳輸的詳細資料頁面會顯示下列資訊：
   <!-- Todo - Add a screenshot -->

   |欄  |定義|
   |---------|---------|
   |傳送要求識別碼|您的轉移要求唯一識別碼。 如果您提交支援要求，則將識別碼分享 Azure 的支援，以加速您的支援要求|
   |移轉要求日期|當傳輸要求已送出的日期|
   |所要求的傳輸|傳送傳輸要求之使用者的電子郵件地址|
   |移轉要求到期日| 移轉要求到期日期|
   |收件者的電子郵件地址|您傳送要求以轉移帳單擁有權之使用者的電子郵件地址|
   |傳輸傳送給收件者的連結|若要檢閱移轉要求傳送給使用者的 url|

## <a name="additional-information"></a>其他資訊

下一節提供有關將轉移訂用帳戶的其他資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

訂用帳戶中的 azure 服務運行不會中斷。 我們只會轉換使用者選取要傳送的 Azure 訂用帳戶的計費關係。

### <a name="disabled-subscriptions"></a>已停用訂用帳戶

無法傳輸已停用的訂用帳戶。 訂用帳戶必須位於其帳單擁有權轉移的作用中狀態。

### <a name="azure-resources-transfer"></a>Azure 資源的傳輸

從訂用帳戶的所有資源，都例如 Vm、 磁碟和網站的傳輸。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 產品移轉

Azure Marketplace 產品傳輸以及其個別的訂用帳戶。

### <a name="azure-reservations-transfer"></a>Azure 保留的傳輸

Azure 保留的項目與訂用帳戶，不會自動移動。 [請連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)移動保留項目。

### <a name="access-to-azure-services"></a>存取 Azure 服務

在轉換期間，不會影響存取 Azure 資源所設定使用 Azure RBAC （角色型存取控制）。

### <a name="azure-support-plan"></a>Azure 支援方案

Azure 支援服務不會傳輸的訂用帳戶。 如果使用者將傳送所有的 Azure 訂用帳戶，要求他們取消其支援方案。

### <a name="charges-for-transferred-subscription"></a>轉移訂用帳戶的費用

訂用帳戶計費的原始擁有者負責報告傳送已完成為止的任何費用。 您發票的區段會負責所報告的傳輸及更新版本的費用。 可能有某些移轉之前發生，但之後才報告的費用。 這些費用會顯示在您的發票區段中。

### <a name="supported-offers"></a>支援的供應項目

可以移轉的任何型別或供應項目，除了 CSP 提供的訂用帳戶。

### <a name="cancel-a-transfer-request"></a>取消移轉要求

您可以取消傳輸要求，直到核准或拒絕要求。 若要取消移轉要求，從頁面底部移至傳輸詳細資料頁面，並選取 [取消]。

### <a name="software-as-a-service-saas-transfer"></a>軟體即服務 (SaaS) 傳送

SaaS 產品不會移轉的訂用帳戶。 要求使用者[請連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)轉移帳單擁有權的 SaaS 產品。 帳單擁有權，以及使用者也可以傳輸資源擁有權。 資源擁有權可讓您執行管理作業，例如刪除及檢視產品的詳細資料。 使用者必須是資源擁有者的 SaaS 產品將資源擁有權轉移。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- Azure 訂用帳戶帳單擁有權轉移至您發票的區段。 追蹤的這些訂用帳戶中的費用[Azure 入口網站](https://portal.azure.com)。
- 讓其他人檢視和管理這些訂用帳戶的計費權限。 如需詳細資訊，請參閱 <<c0> [ 發票區段角色和工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
