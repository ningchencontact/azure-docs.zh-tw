---
title: 取得計費 Azure 訂用帳戶的擁有的權
description: 了解如何要求其他使用者的 Azure 訂用帳戶帳單擁有權。
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
ms.openlocfilehash: 4c9a8c866a157757121e6a9dd07a0a8559937c5e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490866"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>取得的帳單擁有權的其他帳戶的 Azure 訂用帳戶

您可能想要接管的 Azure 訂用帳戶的擁有權，如果現有的計費擁有者離開您的組織，或您想要支付的訂用帳戶，透過您的帳單帳戶。 取得擁有權傳送至您的帳戶的計費的訂用帳戶的責任。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 [檢查您是否擁有存取權的 Microsoft 客戶合約](#check-for-access)。

若要要求帳單擁有權，您必須是**發票區段擁有者**或是**發票區段參與者**。 若要進一步了解，請參閱[發票區段角色工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="request-billing-ownership"></a>帳單擁有權的要求

1. 登入[Azure 入口網站](http://portal.azure.com)為發票區段擁有者或 Microsoft 客戶合約的計費帳戶的參與者。

2. 搜尋**成本管理 + 計費**。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 選取 **發票區段**從左上方。 根據您的存取權，您可能需要選取 帳單帳戶或計費的設定檔。 從計費的帳戶或設定檔上，選取**發票區段**。
   
   ![螢幕擷取畫面顯示選取的發票區段](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 從清單中選取發票一節。 一旦您取得訂用帳戶擁有權，他們將予計費發票本節。

5. 選取 **轉移要求**從左下方，然後選取**新增**。
 
   ![螢幕擷取畫面顯示選取轉送要求](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 輸入您正在要求從的帳單擁有權的使用者電子郵件地址。 使用者必須是帳戶管理員上的 Microsoft 線上服務方案計費的帳戶或 Enterprise 合約的帳戶擁有者。 如需詳細資訊，請參閱 < [Azure 入口網站中檢視您的帳單帳戶](billing-view-all-accounts.md)。 選取 **傳送傳輸要求**。

   ![顯示傳送傳輸要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 使用者會收到一封電子郵件以檢閱您的轉移要求的指示。

   ![螢幕擷取畫面，顯示檢閱傳輸要求的電子郵件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 若要核准的轉移要求，使用者的電子郵件中選取的連結，並遵循指示進行。

    ![螢幕擷取畫面，顯示檢閱傳輸要求的電子郵件](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>檢查傳送要求狀態

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋**成本管理 + 計費**。

   ![螢幕擷取畫面顯示 Azure 入口網站的搜尋，成本管理 + 計費](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 選取 **發票區段**從左上方。 根據您的存取權，您可能需要選取 帳單帳戶或計費的設定檔。 從計費的帳戶或設定檔上，選取**發票區段**。
   
   ![螢幕擷取畫面顯示選取的發票區段](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 從您送出的轉移要求清單中選取發票一節。

5. 選取 **轉移要求**從左下方。 傳輸要求頁面會顯示下列資訊：

    ![顯示清單中的傳輸要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

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
   |已完成| 使用者選取的訂用帳戶的計費會轉移至您發票的區段|
   |完成但發生錯誤|無法轉移要求已完成，但部分使用者選取的訂用帳戶的計費|
   |已過期|使用者未在時間上接受要求，而且過期|
   |Canceled|其他人的存取權，傳送要求取消要求|
   |已拒絕|使用者已拒絕傳送要求|

7. 選取 傳送要求以檢視詳細資料。 傳輸的詳細資料頁面會顯示下列資訊：
   
   ![顯示傳送訂用帳戶清單的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |欄  |定義|
   |---------|---------|
   |傳送要求識別碼|您的轉移要求唯一識別碼。 如果您提交支援要求，則將識別碼分享 Azure 的支援，以加速您的支援要求|
   |移轉要求日期|當傳輸要求已送出的日期|
   |所要求的傳輸|傳送傳輸要求之使用者的電子郵件地址|
   |移轉要求到期日| 移轉要求到期日期|
   |收件者的電子郵件地址|您傳送要求以轉移帳單擁有權之使用者的電子郵件地址|
   |傳輸傳送給收件者的連結|若要檢閱移轉要求傳送給使用者的 url|

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

您可以要求下面所列的帳單擁有權的訂用帳戶類型。

- [行動套件](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open 授權](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 贊助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免費試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 贊助優惠](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/) \(英文\)
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 訂閱者](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 訂閱者](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* 任何可用的訂用帳戶的信用額度轉移之後將無法使用新的帳戶中。

\*\* 只支援訂閱期間所建立的帳戶中註冊 Azure 網站上。


## <a name="additional-information"></a>其他資訊

下一節提供有關將轉移訂用帳戶的其他資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

訂用帳戶中的 azure 服務運行不會中斷。 我們只會轉換使用者選取要傳送的 Azure 訂用帳戶的計費關係。

### <a name="disabled-subscriptions"></a>已停用的訂用帳戶

無法傳輸已停用的訂用帳戶。 訂用帳戶必須位於其帳單擁有權轉移的作用中狀態。

### <a name="azure-resources-transfer"></a>Azure 資源的傳輸

從訂用帳戶的所有資源，都例如 Vm、 磁碟和網站的傳輸。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 產品移轉

Azure Marketplace 產品傳輸以及其個別的訂用帳戶。

### <a name="azure-reservations-transfer"></a>Azure 保留的傳輸

Azure 保留的項目與訂用帳戶，不會自動移動。 [請連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)移動保留項目。

### <a name="access-to-azure-services"></a>存取 Azure 服務

現有的使用者、 群組或服務主體指派使用 (Azure RBAC （角色型存取控制）) 的存取 [...在轉換期間，不會影響 /role-based-access-control/overview.md]。

### <a name="azure-support-plan"></a>Azure 支援方案

Azure 支援服務不會傳輸的訂用帳戶。 如果使用者將傳送所有的 Azure 訂用帳戶，要求他們取消其支援方案。

### <a name="charges-for-transferred-subscription"></a>轉移訂用帳戶的費用

訂用帳戶計費的原始擁有者負責報告傳送已完成為止的任何費用。 您發票的區段會負責所報告的傳輸及更新版本的費用。 可能有某些移轉之前發生，但之後才報告的費用。 這些費用會顯示在您的發票區段中。

### <a name="cancel-a-transfer-request"></a>取消移轉要求

您可以取消傳輸要求，直到核准或拒絕要求。 若要取消移轉要求，請前往[傳輸的詳細資料頁面](#check-the-transfer-request-status)，並從頁面底部，取消選取。

### <a name="software-as-a-service-saas-transfer"></a>軟體即服務 (SaaS) 傳送

SaaS 產品不會移轉的訂用帳戶。 要求使用者[請連絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)轉移帳單擁有權的 SaaS 產品。 帳單擁有權，以及使用者也可以傳輸資源擁有權。 資源擁有權可讓您執行管理作業，例如刪除及檢視產品的詳細資料。 使用者必須是資源擁有者的 SaaS 產品將資源擁有權轉移。

## <a name="check-for-access"></a>存取檢查
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- Azure 訂用帳戶帳單擁有權轉移至您發票的區段。 追蹤的這些訂用帳戶中的費用[Azure 入口網站](https://portal.azure.com)。
- 讓其他人檢視和管理這些訂用帳戶的計費權限。 如需詳細資訊，請參閱 <<c0> [ 發票區段角色和工作](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
