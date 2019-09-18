---
title: 在 Azure 入口網站檢視計費帳戶 | Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視計費帳戶。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490210"
---
# <a name="view-billing-accounts-in-azure-portal"></a>在 Azure 入口網站檢視計費帳戶  

當您註冊使用 Azure 時，就會建立計費帳戶。 您可使用計費帳戶來管理您的發票、付款及追蹤成本。 您可擁有多個計費帳戶的存取權。 例如，您可能已針對您的個人專案註冊 Azure。 但也可以透過貴組織的 Enterprise 合約或 Microsoft 客戶合約獲得存取權。 針對上述每個案例，您會有不同的計費帳戶。

Azure 入口網站目前支援下列計費帳戶類型：

- **Microsoft Online Services 方案**：當您透過 Azure 網站註冊 Azure 時，就會建立 Microsoft Online Services 方案的計費帳戶。 例如，當您註冊 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)、[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)，或以 [Visual studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)身分註冊時。

- **Enterprise 合約**：當貴組織簽署 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 以使用 Azure 時，就會建立 Enterprise 合約的計費帳戶。

- **Microsoft 客戶合約**：當貴組織與 Microsoft 代表共同簽署 Microsoft 客戶合約時，就會建立 Microsoft 客戶合約的計費帳戶。 精選區域中某些透過 Azure 網站註冊[採用隨用隨付費率的帳戶](https://azure.microsoft.com/offers/ms-azr-0003p/)或將其 [Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)升級的客戶，可能也有 Microsoft 客戶合約的計費帳戶。 如需詳細資訊，請參閱[開始使用 Microsoft 客戶合約的計費帳戶](billing-mca-overview.md)。

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>計費帳戶的範圍
範圍是計費帳戶內使用者用來查看和管理計費的節點。 使用者可在此管理計費資料、付款、發票，以及進行一般帳戶管理。 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 方案

|影響範圍  |定義  |
|---------|---------|
|計費帳戶     | 代表一或多個 Azure 訂用帳戶的單一擁有者 (帳戶管理員)。 帳戶管理員有權執行各種計費工作，例如建立訂用帳戶、檢視發票或變更訂用帳戶的計費。  |
|Subscription     |  代表 Azure 資源的群組。 發票會在此範圍產生。 它有自己的付款方式，可用來支付其發票。|


### <a name="enterprise-agreement"></a>Enterprise 合約

|影響範圍  |定義  |
|---------|---------|
|計費帳戶    | 表示 Enterprise 合約註冊。 發票會在此範圍產生。 其使用部門和註冊帳戶進行結構化。  |
|department     |  註冊帳戶的選擇性群組。      |
|註冊帳戶     |  代表單一帳戶擁有者。 Azure 訂用帳戶會建立在此範圍之下。  |


### <a name="microsoft-customer-agreement"></a>Microsoft 客戶合約

|影響範圍  |工作  |
|---------|---------|
|計費帳戶     |   表示適用於多項 Microsoft 產品和服務的客戶合約。 其使用帳單設定檔和發票區段進行結構化。   |
|帳單設定檔     |  表示發票及其付款方式。 發票會在此範圍產生。 它可以有多個發票區段。      |
|發票區段     |   代表發票中的一組成本。 訂用帳戶和其他購買項目會與此範圍相關聯。    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>在 Azure 入口網站中切換計費範圍


1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 針對 [成本管理 + 帳單]  進行搜尋。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 從左側選取 [所有計費範圍]  。

   ![顯示全部計費範圍的螢幕擷取畫面](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** 如果您只有一個範圍的存取權，您將不會看到 [所有計費範圍]  。

4. 選取範圍以檢視詳細資料。



## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 了解如何開始[分析成本](../cost-management/quick-acm-cost-analysis.md)。