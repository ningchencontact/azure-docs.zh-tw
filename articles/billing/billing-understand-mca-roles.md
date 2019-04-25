---
title: 了解適用於 Microsoft 客戶合約-Azure 的計費系統管理角色
description: 深入了解計費適用於 Microsoft 客戶合約計費帳戶在 Azure 中的角色。
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370890"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>了解在 Azure 中的 Microsoft 客戶合約系統管理角色

若要管理您的帳單帳戶之 Microsoft 客戶合約，使用下列各節所述的角色。 這些角色是除了 Azure 有以控制資源的存取權的內建角色。 如需詳細資訊，請參閱 [Azure 資源的內建角色](../role-based-access-control/built-in-roles.md)。

這篇文章適用於計費的帳戶 Microsoft 客戶合約。 檢查您是否擁有存取權的 Microsoft 客戶合約。

## <a name="billing-role-definitions"></a>計費的角色定義

下表描述您用來管理您的帳單帳戶計費設定檔，和發票區段的計費角色。

|角色|描述|
|---|---|
|帳單帳戶擁有者|管理計費帳戶的所有項目|
|帳單帳戶參與者|管理計費帳戶的權限以外的所有項目|
|帳單帳戶讀者|唯讀檢視所有項目上的帳單帳戶|
|帳單設定檔擁有者|管理計費的設定檔的所有項目|
|帳單設定檔參與者|管理計費的設定檔的權限以外的所有項目|
|帳單設定檔讀者|唯讀的所有項目上檢視帳單設定檔|
|發票管理員|檢視及支付發票計費設定檔|
|發票區段擁有者|管理在發票 區段中的所有項目|
|發票區段參與者|管理 [發票] 區段中的權限以外的所有項目|
|發票區段讀者|唯讀檢視所有項目上的 [發票] 區段|
|Azure 訂用帳戶建立者|建立 Azure 訂用帳戶|

## <a name="billing-account-roles-and-tasks"></a>帳單帳戶角色和工作

帳單帳戶可讓您管理貴組織的計費。 您可以使用計費帳戶組織成本、 監視費用和發票，並控制您組織的計費的存取。 如需詳細資訊，請參閱 <<c0> [ 了解帳單帳戶](billing-mca-overview.md#understand-billing-account)。

下表顯示您需要完成的工作計費帳戶的內容中的角色。

### <a name="manage-billing-account-permissions-and-properties"></a>管理計費的帳戶權限和屬性

|Task|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者|
|---|---|---|---|
|檢視現有的權限，為帳單帳戶|✔|✔|✔|
|讓其他人檢視和管理的計費帳戶的權限|✔|✘|✘|
|檢視帳單帳戶屬性，例如公司名稱、 地址和更多功能|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>管理計費的計費帳戶的設定檔

|Task|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者|
|---|---|---|---|
|檢視帳戶中的所有計費的設定檔|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>管理計費帳戶的發票

|Task|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者|
|---|---|---|---|
|檢視帳戶中的所有發票|✔|✔|✔|
|下載發票，Azure 使用量和費用檔案，價位表和稅務帳戶中的文件|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>管理計費帳戶的發票區段

|Task|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者|
|---|---|---|---|
|檢視帳戶中的所有 「 發票 」 區段|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>管理計費帳戶的交易

|Task|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者|
|---|---|---|---|
|檢視帳戶的所有計費的交易|✔|✔|✔|
|檢視所有帳戶所購買的產品|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>管理訂用帳戶帳單帳戶

|Task|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者|
|---|---|---|---|
|檢視帳單帳戶中的所有 Azure 訂用帳戶|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>帳單設定檔角色和工作

帳單的設定檔可讓您管理您的發票和付款方法。 Azure 訂用帳戶和計費的設定檔的使用購買其他產品，會產生每月的發票。 您可以使用的付款方法要按發票付款。 如需詳細資訊，請參閱 <<c0> [ 了解計費的設定檔](billing-mca-overview.md#understand-billing-profiles)。

下表顯示您需要完成的工作計費的設定檔的內容中的角色。

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>管理計費的設定檔權限、 屬性和原則

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單的設定檔的現有權限|✔|✔|✔|✔|✔|✔|✔|
|讓其他人檢視和管理計費的設定檔的權限|✔|✘|✘|✘|✘|✘|✘|
|檢視帳單設定檔屬性，例如訂單號碼、 電子郵件發票喜好設定，和更多功能|✔|✔|✔|✔|✔|✔|✔|
|更新計費的設定檔屬性 |✔|✔|✘|✘|✘|✘|✘|
|檢視原則套用在例如計費的設定檔啟用 Azure 的保留購買、 啟用 Azure marketplace 購買項目，和更多功能|✔|✔|✔|✔|✔|✔|✔|
|將原則套用的計費的設定檔 |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>管理計費的設定檔的發票

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單寄送的設定檔的所有發票|✔|✔|✔|✔|✔|✔|✔|
|下載發票，Azure 使用量和費用檔案，價位表和稅務計費的設定檔的文件|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>管理計費的設定檔的發票區段

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單寄送的設定檔的所有發票區段|✔|✔|✔|✔|✔|✔|✔|
|建立新的發票區段計費的設定檔|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>管理計費的設定檔的交易

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單的設定檔的所有計費的交易|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>管理計費的設定檔的付款方式

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視帳單的設定檔的付款方法|✔|✔|✔|✔|✔|✔|✔|
|追蹤計費的設定檔的 Azure 信用額度餘額|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>管理訂用帳戶計費的設定檔

|Task|帳單設定檔擁有者|帳單設定檔參與者|帳單設定檔讀者|發票管理員|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視所有 Azure 訂用帳戶計費的設定檔|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>發票區段角色和工作

發票 區段可讓您組織的成本，發票上。 您可以建立依部門、 開發環境、 組織您的成本區段，或根據您組織的需求。 若要建立之區段的 Azure 訂用帳戶的權限讓其他人。 任何使用費用和購買訂用帳戶則顯示在發票中的區段。 如需詳細資訊，請參閱 <<c0> [ 了解發票區段](billing-mca-overview.md#understand-invoice-sections)。

下表顯示您需要發票區段的內容中完成工作的角色。

### <a name="manage-invoice-section-permissions-and-properties"></a>管理 [發票] 區段的權限和屬性

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者 | |
|---|---|---|---|---|---|---|---|---|
|發票 區段中檢視所有的權限|✔|✔|✔|✔|✔|✔|✔| |
|讓其他人檢視和管理 [發票] 區段的權限|✔|✘|✘|✘|✘|✘|✘| |
|檢視發票區段屬性|✔|✔|✔|✔|✔|✔|✔| |
|更新發票的區段屬性|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>管理產品發票區段

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視購買發票一節中的所有產品|✔|✔|✔|✘|✔|✔|✔|
|管理計費發票區段中跟取消的產品，請關閉自動續約，和更多功能|✔|✔|✘|✘|✘|✘|✘|
|產品變更發票區段|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>管理訂用帳戶的發票區段

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|帳單帳戶擁有者|帳單帳戶參與者|帳單帳戶讀者
|---|---|---|---|---|---|---|---|
|檢視所有 Azure 訂用帳戶發票區段|✔|✔|✔|✘|✔|✔|✔|
|變更訂用帳戶的發票區段|✔|✔|✘|✘|✘|✘|✘|
|從其他計費的帳戶中的使用者要求訂用帳戶的帳單擁有的權|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>訂用帳戶帳單角色和工作

下表顯示您要的訂用帳戶內容中完成工作的角色。

|工作|發票區段擁有者|發票區段參與者|發票區段讀者|Azure 訂用帳戶建立者|
|---|---|---|---|---|
|建立 Azure 訂用帳戶|✔|✔|✘|✔|
|更新訂用帳戶的成本中心|✔|✔|✘|✘|
|變更訂用帳戶的發票區段|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>管理 Azure 入口網站中的計費角色

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單] 進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. 選取 [**存取控制 (IAM)** 在例如計費帳戶、 計費的設定檔或發票] 區段中，您要授與存取權範圍。

4. 存取控制 (IAM) 頁面會列出使用者和群組指派給該範圍的每個角色。

   ![顯示的計費帳戶的系統管理員清單的螢幕擷取畫面](./media/billing-understand-mca-roles/billing-list-admins.png)

5. 若要讓使用者存取，請選取**新增**從頁面頂端。 在 [角色] 下拉式清單中選取的角色。 輸入您要授與存取權之使用者的電子郵件地址。 選取 **儲存**將角色指派。

   ![顯示加入帳單帳戶的系統管理員的螢幕擷取畫面](./media/billing-understand-mca-roles/billing-add-admin.png)

6. 若要移除使用者的存取權，請選取您想要移除的角色指派的使用者。 選取 [移除]。

   ![顯示從計費的帳戶移除系統管理員的螢幕擷取畫面](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟

請參閱下列文章以深入了解您的帳單帳戶：

- [取得您的帳單帳戶的 Microsoft 客戶合約所述](billing-mca-overview.md)
- [Microsoft 客戶協議建立為帳單帳戶的 Azure 訂用帳戶](billing-mca-create-subscription.md)
