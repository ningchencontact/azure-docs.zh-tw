---
title: 在 Azure 入口網站中檢視您的帳單帳戶 |Microsoft Docs
description: 了解如何在 Azure 入口網站中檢視您的計費帳戶。
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490210"
---
# <a name="view-billing-accounts-in-azure-portal"></a>在 Azure 入口網站中檢視計費的帳戶  

當您使用 Azure 註冊時，會建立帳單帳戶。 您可以使用您的帳單帳戶來管理您的發票付款，並追蹤成本。 您可以有多個計費帳戶的存取權。 比方說，您可能已註冊 Azure 為您個人的專案。 您也可以透過您組織的 Enterprise 合約或 Microsoft 客戶合約的存取。 針對每個這些案例中，您必須使用個別的帳單帳戶。

Azure 入口網站目前支援下列類型的計費帳戶：

- **Microsoft Online Services Program**:當您註冊 Azure 透過 Azure 網站時，會建立 Microsoft Online Services 方案的計費帳戶。 比方說，當您申請[Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)，[帳戶，以隨用隨付費率](https://azure.microsoft.com/offers/ms-azr-0003p/)或是[Visual studio 訂閱者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)。

- **Enterprise 合約**：當貴組織註冊建立帳單帳戶的 Enterprise 合約[Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)使用 Azure。

- **Microsoft 客戶合約**:您的組織搭配 Microsoft 代表以簽署 Microsoft 客戶合約時，會建立 Microsoft 客戶合約的計費帳戶。 精選的區域中的一些客戶透過 Azure 網站中註冊[帳戶，以隨用隨付費率](https://azure.microsoft.com/offers/ms-azr-0003p/)或升級其[Azure 免費帳戶](https://azure.microsoft.com/offers/ms-azr-0044p/)可能有的 Microsoft 客戶的帳單帳戶協議。 如需詳細資訊，請參閱 <<c0> [ 開始使用您的帳單帳戶的 Microsoft 客戶合約](billing-mca-overview.md)。

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>計費帳戶的範圍
範圍是使用者用來檢視及管理計費的帳單帳戶內的節點。 它是使用者管理計費資料，付款發票和進行一般的帳戶管理的位置。 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 方案

|`Scope`  |定義  |
|---------|---------|
|計費帳戶     | 表示單一的擁有者 （帳戶系統管理員） 的一或多個 Azure 訂用帳戶。 帳戶管理員已獲授權執行各種計費工作，例如建立訂用帳戶、 檢視發票或變更訂用帳戶的計費。  |
|訂用帳戶     |  代表 Azure 資源的群組。 在這個範圍內，會產生發票。 它有自己用來支付其發票的付款方法。|


### <a name="enterprise-agreement"></a>Enterprise 合約

|`Scope`  |定義  |
|---------|---------|
|計費帳戶    | 表示的 Enterprise 合約註冊。 在這個範圍內，會產生發票。 它是結構化使用部門和註冊帳戶。  |
|department     |  選擇性的註冊帳戶的群組。      |
|註冊帳戶     |  代表單一帳戶擁有者。 此領域下建立 azure 訂用帳戶。  |


### <a name="microsoft-customer-agreement"></a>Microsoft 客戶合約

|`Scope`  |工作  |
|---------|---------|
|計費帳戶     |   表示用於多個 Microsoft 產品和服務之客戶合約。 它是結構化使用計費的設定檔和發票區段。   |
|帳單設定檔     |  代表發票和付款方法。 在這個範圍內，會產生發票。 它可以有多個 「 發票 」 區段。      |
|發票區段     |   表示一群發票中的成本。 訂用帳戶和其他購買項目是這個領域關聯。    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>切換 Azure 入口網站中的計費範圍


1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 搜尋**成本管理 + 計費**。

   ![顯示 Azure 入口網站搜尋的螢幕擷取畫面](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 選取 **計費的所有領域**從左上方。

   ![顯示所有計費範圍的螢幕擷取畫面](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   貴用戶將不會看到**計費的所有領域**如果您只需要一個範圍的存取。

4. 選取範圍以檢視詳細資料。



## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟
- 了解如何開始[分析成本](../cost-management/quick-acm-cost-analysis.md)。