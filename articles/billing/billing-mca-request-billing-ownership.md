---
title: 取得 Azure 訂用帳戶的帳單擁有權
description: 瞭解如何向其他使用者要求 Azure 訂用帳戶的帳單擁有權。
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
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019564"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>從其他帳戶取得 Azure 訂用帳戶的帳單擁有權

如果現有的帳單擁有者離開您的組織, 或您想要透過帳單帳戶支付訂閱費用, 您可能會想要接管 Azure 訂用帳戶的擁有權。 取得擁有權會將訂用帳戶的帳單責任轉移給您的帳戶。

本文適用于 Microsoft 客戶合約的帳單帳戶。 [檢查您是否有權存取 Microsoft 客戶合約](#check-for-access)。

若要要求帳單擁有權, 您必須是**發票區段的擁有**者或**發票區段參與者**。 若要深入瞭解, 請參閱[發票區段角色](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)工作。

## <a name="request-billing-ownership"></a>要求帳單擁有權

1. 以發票區段 [擁有者] 或 [參與者] 的身分登入[Azure 入口網站](https://portal.azure.com), 以取得 Microsoft 客戶合約的帳單帳戶。

2. 搜尋**成本管理 + 帳單**。

   ![顯示 [成本管理] 和 [計費] Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 從左側選取 [**發票區段**]。 根據您的存取權, 您可能需要選取帳單帳戶或帳單設定檔。 從帳單帳戶或設定檔中, 選取 [**發票區段**]。
   
   ![顯示選取發票區段的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 從清單中選取 [發票] 區段。 一旦您接管訂用帳戶的擁有權, 就會向此發票區段收取費用。

5. 從左下方選取 [**傳送要求**], 然後選取 [**新增**]。
 
   ![顯示選取傳送要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 輸入您要要求帳單擁有權的使用者電子郵件地址。 使用者必須是 Microsoft Online Services 方案帳單帳戶的帳戶管理員, 或 Enterprise 合約上的帳戶擁有者。 如需詳細資訊, 請參閱[在 Azure 入口網站中查看帳單帳戶](billing-view-all-accounts.md)。 選取 [**傳送傳輸要求**]。

   ![顯示傳送傳輸要求的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 使用者會收到一封電子郵件, 其中包含用來檢查您的傳輸要求的指示。

   ![顯示審核傳送要求電子郵件的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 若要核准傳輸要求, 使用者可以選取電子郵件中的連結, 並遵循指示進行。

    ![顯示審核傳送要求電子郵件的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>檢查傳輸要求狀態

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋**成本管理 + 帳單**。

   ![顯示 [成本管理] 和 [計費] Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 從左側選取 [**發票區段**]。 根據您的存取權, 您可能需要選取帳單帳戶或帳單設定檔。 從帳單帳戶或設定檔中, 選取 [**發票區段**]。
   
   ![顯示選取發票區段的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 從您傳送傳輸要求的清單中選取 [發票] 區段。

5. 從左下方選取 [**傳送要求**]。 [傳送要求] 頁面會顯示下列資訊:

    ![顯示傳送要求清單的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |「資料行」|定義|
   |---------|---------|
   |要求日期|傳送要求送出的日期|
   |收件者|您傳送要求以傳輸帳單擁有權之使用者的電子郵件地址|
   |有效期限|要求到期的日期|
   |狀態|傳輸要求的狀態|

    傳送要求可以具有下列其中一種狀態:

   |狀態|定義|
   |---------|---------|
   |進行中|使用者尚未接受傳送要求|
   |正在處理|使用者已核准傳送要求。 使用者所選取之訂用帳戶的帳單已轉移至您的發票區段|
   |已完成| 使用者選取之訂用帳戶的帳單, 會傳送至您的發票區段|
   |已完成但發生錯誤|要求已完成, 但無法傳送使用者選取的部分訂用帳戶的帳單|
   |已過期|使用者不接受當時的要求, 且已過期|
   |已取消|具有傳輸要求存取權的人員已取消要求|
   |已拒絕|使用者已拒絕傳送要求|

7. 選取要查看詳細資料的傳輸要求。 [傳輸詳細資料] 頁面會顯示下列資訊:
   
   ![顯示已傳送訂閱清單的螢幕擷取畫面](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |「資料行」  |定義|
   |---------|---------|
   |傳輸要求識別碼|您的傳輸要求的唯一識別碼。 如果您提交支援要求, 請與 Azure 支援共用識別碼, 以加速您的支援要求|
   |傳送要求于|傳送要求送出的日期|
   |要求傳輸者|傳送要求之使用者的電子郵件地址|
   |傳輸要求將于到期| 傳輸要求到期的日期|
   |收件者的電子郵件地址|您傳送要求以傳輸帳單擁有權之使用者的電子郵件地址|
   |傳送到收件者的轉移連結|傳送給使用者以檢查傳輸要求的 url|

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

您可以要求下列訂用帳戶類型的帳單擁有權。

- [動作套件](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure in Open 授權](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 贊助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 計畫](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 贊助的供應專案](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft Enterprise 合約](https://azure.microsoft.com/pricing/enterprise-agreement/) \(英文\)
- [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平臺](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 訂閱者](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 訂閱者](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 訂閱者](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\*在傳送之後, 新帳戶中將無法使用訂用帳戶上任何可用的信用額度。

\*\*只有在 Azure 網站註冊期間建立的帳戶中, 才支援訂閱。


## <a name="additional-information"></a>其他資訊

下一節提供有關傳輸訂閱的其他資訊。

### <a name="no-service-downtime"></a>沒有服務停機時間

訂用帳戶中的 Azure 服務會繼續執行, 而不會有任何中斷。 我們只會針對使用者選取要傳送的 Azure 訂用帳戶, 轉換其計費關係。

### <a name="disabled-subscriptions"></a>已停用訂用帳戶

無法轉移已停用的訂閱。 訂閱必須處於作用中狀態, 才能轉移其帳單擁有權。

### <a name="azure-resources-transfer"></a>Azure 資源傳輸

訂用帳戶中的所有資源, 例如 Vm、磁片和網站傳輸。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 產品傳輸

Azure Marketplace 產品會連同各自的訂閱一起轉移。

### <a name="azure-reservations-transfer"></a>Azure 保留傳輸

Azure 保留專案不會自動與訂用帳戶一起移動。 [聯絡 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以移動保留。

### <a name="access-to-azure-services"></a>存取 Azure 服務

使用指派的現有使用者、群組或服務主體的存取權 (Azure RBAC (角色型存取控制)) [.。/role-based-access-control/overview.md] 在轉換期間不會受到影響。

### <a name="azure-support-plan"></a>Azure 支援計畫

Azure 支援不會與訂閱一起傳輸。 如果使用者傳送所有 Azure 訂用帳戶, 請要求他們取消其支援方案。

### <a name="charges-for-transferred-subscription"></a>已轉移的訂用帳戶費用

訂用帳戶的原始計費擁有者, 會負責回報到傳送完成為止的任何費用。 您的發票區段會負責從傳輸開始時回報的費用。 傳輸之前可能會發生一些費用, 但之後才回報。 這些費用會顯示在您的發票區段上。

### <a name="cancel-a-transfer-request"></a>取消傳輸要求

您可以取消傳送要求, 直到要求獲得核准或遭到拒絕為止。 若要取消傳送要求, 請移至 [[傳輸詳細資料] 頁面](#check-the-transfer-request-status), 然後從頁面底部選取 [取消]。

### <a name="software-as-a-service-saas-transfer"></a>軟體即服務 (SaaS) 傳輸

SaaS 產品不會與訂閱一起傳輸。 要求使用者[聯繫 Azure 支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以轉移 SaaS 產品的帳單擁有權。 除了帳單擁有權之外, 使用者也可以轉移資源擁有權。 資源擁有權可讓您執行管理作業, 例如刪除和查看產品的詳細資料。 使用者必須是 SaaS 產品上的資源擁有者, 才能轉移資源擁有權。

## <a name="check-for-access"></a>檢查存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎? 連絡支援人員

如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

- Azure 訂用帳戶的帳單擁有權會轉移至您的發票區段。 追蹤[Azure 入口網站](https://portal.azure.com)中這些訂用帳戶的費用。
- 授與其他人查看和管理這些訂用帳戶帳單的許可權。 如需詳細資訊, 請參閱[發票區段角色和](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)工作。
