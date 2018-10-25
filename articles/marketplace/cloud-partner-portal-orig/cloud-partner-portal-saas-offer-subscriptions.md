---
title: SaaS - 透過 Azure 銷售 |Microsoft Docs
description: 描述適用於 SaaS 應用程式的訂用帳戶計費模型，以及實作它的方式。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806445"
---
<a name="saas---sell-through-azure"></a>SaaS - 透過 Azure 銷售
=========================

此文章說明適用於 SaaS 應用程式的訂用帳戶計費模型，以及在 Cloud Partner 入口網站中實作它的方式。


<a name="overview"></a>概觀
--------

Azure Marketplace 可讓您透過 Azure Marketplace 發佈並銷售您的 SaaS 供應項目。 對於您的客戶來說，他們現在可以直接透過 Azure 的計費支付款項，並從 Azure 接收包含其所啟用之所有資源和服務的單一帳單。 在發佈方面，SaaS 訂用帳戶具有這些優點：

-   **一致的發佈體驗**：如果您已經在 Azure Marketplace 中發佈過 SaaS 供應項目或任何其他供應項目，在相同發佈入口網站中的後續發佈體驗都會是相同的。
-   **可供探索的新店面**：所有發佈的供應項目都會顯示在外部的 Azure Marketplace 店面，以及 Azure 入口網站內部的 Azure Marketplace 擴充功能中。
-   **整合式計費**：您現在可以在 Azure 進行銷售的所有區域中銷售，並由 Azure 為您處理帳單事務。
-   **整合式潛在客戶開發**：現在，每當有 Azure 使用者使用 Azure Marketplace 訂閱您的 SaaS 服務時，您便可以在自己所選擇的 CRM 中從 Azure 自動接收該潛在客戶。
-   **與 Microsoft 銷售者共同銷售**：您可以符合雙向分享潛在客戶的資格、取得排列優先順序的目錄列表，並有機會與 Microsoft 銷售者合作共同完成聯合交易。

<a name="billing-models"></a>計費模型
--------------

目前唯一支援的計費模型，是針對您 SaaS 服務每個訂用帳戶收取的固定每月費用。

**注意：** 可能會在未來推出其他計費模型。

每個 SaaS 訂用帳戶都可以有一或多個方案 (例如「基礎」或「進階」)。 每個方案都必須有一些相關的基本中繼資料，以及與該方案相關聯的價格。

您對方案的定義具有完全的控制權。 例如，「基礎」方案是由什麼所構成？ 該方案是由您所定義，且作為發佈方案的一部分，您可以提供所需的文字來描述它。

與方案相關聯的價格，是 Azure 使用者需要支付以使用服務的固定每月費用。

### <a name="what-is-not-supported-today"></a>目前不支援什麼？

-   根據自訂單位所進行的計費，例如以要求數目為基礎的固定價格。
-   根據基座配置所進行的計費，例如允許 Azure 使用者根據使用者數目購買授權。

<a name="end-to-end-flow"></a>端對端流程
---------------

SaaS 訂用帳戶供應項目流程是從使用者的觀點開始

**注意：** 此流程描述假設您已在 Azure Marketplace 中發佈稱為 'Contoso demo SaaS' 的 SaaS 供應項目。

![SaaS 訂用帳戶使用者流程](media/saas-offer-subscription/saas-subscription-user-flow.png)

Azure 使用者可以與您的 SaaS 服務進行下列互動：

-   在 Azure Marketplace 中探索到您的 SaaS 服務
-   在 Azure 中訂閱您的 SaaS 服務
-   從 Azure 入口網站瀏覽至該 SaaS 服務
-   在該 SaaS 服務中建立帳戶，並在 SaaS 中管理 (變更方案/刪除) 該帳戶
-   從 Azure 入口網站取消訂閱該 SaaS 服務

<a name="discover-your-saas-service-in-azure-marketplace"></a>在 Azure Marketplace 中探索到您的 SaaS 服務
-----------------------------------------------

當使用者在 Azure 入口網站中啟動 Azure Marketplace 時，他們將會看見稱為 [軟體即服務 (SaaS)] 的類別。

![使用類別功能表探索 SaaS](media/saas-offer-subscription/saas-category-menu.png)

使用者也可以搜尋該 SaaS 服務。

![使用搜尋探索 SaaS](media/saas-offer-subscription/saas-cpp-search.png)

使用者接著可以檢視您服務的詳細資料，然後按一下 [建立]。

![建立 SaaS 訂用帳戶](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>在 Azure 中訂閱 SaaS 服務

使用者接著可以從 Azure 入口網站訂閱該 SaaS 服務。

![訂閱 SaaS 服務](media/saas-offer-subscription/saas-subscribe-signup.png)

當使用者訂閱您的 SaaS 服務時，該使用者會提供下列資訊

-   名稱：使用者可以在 Azure 中探索或管理此 SaaS 訂用帳戶執行個體的名稱。
-   訂用帳戶：使用者想要與 SaaS 服務的計費相關聯的訂用帳戶內容。
-   方案：使用者想要訂閱的 SaaS 服務方案。

作為發佈供應項目的一部分所提供的使用條款文件，也會在使用者訂閱 SaaS 服務之前向其顯示。

使用者現在可以按一下 [訂閱] 來訂閱該服務。
訂閱完成之後，Azure 便會在入口網站中傳送通知。

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>從 Azure 入口網站瀏覽至該 SaaS 服務

使用者接著可以按一下 [前往資源] 來檢視或管理其 SaaS 訂用帳戶執行個體。

![檢視或管理您的 SaaS 執行個體](media/saas-offer-subscription/saas-go-to-resource.png)

系統會通知使用者，指出他們必須先在 SaaS 服務中設定帳戶。 在 SaaS 服務通知 Azure 開始計費，也就是當使用者在 SaaS 服務網站上建立帳戶之後，Azure 便會開始對使用者進行計費。

![設定您的 SaaS 執行個體](media/saas-offer-subscription/saas-configure-account.png)

當使用者按一下 [設定帳戶] 時，系統便會將他們重新導向至您的 SaaS 服務端點。 在此重新導向期間，系統同時會傳遞權杖作為查詢參數。 例如︰

![SaaS 帳戶權杖](media/saas-offer-subscription/saas-account-token.png)

請留意此權杖的值。 這是一個短期權杖，並需要進行解析以在 Azure 中取得訂用帳戶識別碼。

<a name="creating-a-saas-offer-subscription"></a>建立 SaaS 供應項目訂用帳戶
----------------------------------

若要建置此體驗，需要進行兩個工作：

-   將您的 SaaS 服務網站連線至 Microsoft 的 SaaS API。 [SaaS 透過 Azure 銷售 - API](./cloud-partner-portal-saas-subscription-apis.md) 文件會說明建置此連線的方法。  
-   在 Cloud Partner 入口網站上的 [技術資訊] 區段中啟用 [透過 Azure 銷售]，並提供所有設定詳細資料。

![SaaS 新增供應項目技術資訊](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

下面為 [技術資訊] 區段所有必要欄位的說明：

|  **供應項目欄位**                 |  **說明**                                   |
|  ----------------                 |  -------------------------------------             |
| 預覽訂用帳戶識別碼          | 在公開提供您的供應項目之前，用來在預覽中測試它的所有 Azure 訂用帳戶識別碼。 |
| 開始使用指示      | 與客戶共用以協助他們連線至您 SaaS 應用程式的指示。 允許使用基本 HTML，例如 `<p>`、`<h1>`、`<li>` 等。  |
| 登陸頁面 URL                  | 在客戶從 Azure 入口網站中取得應用程式之後，您將會引導他們進行登陸的網站 URL。 此 URL 也會是接收連線 API 以與 Microsoft 建立商務的端點。  |
| 連線 Webhook                | 針對 Microsoft 需代表客戶傳送給您的所有非同步事件 (例如 Azure 訂用帳戶已經無效)，我們需要您向我們提供連線 Webhook。 如果您尚未具有 Webhook 系統，最簡單的設定方式便是準備會接聽任何向它張貼的事件，並能適當處理它們的 HTTP 端點邏輯應用程式。  如需詳細資訊，請參閱[在邏輯應用程式中透過 HTTP 端點呼叫、觸發或巢狀處理工作流程](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。 |
| Azure AD 租用戶識別碼和應用程式識別碼     | 在 Azure 入口網站內，我們需要您建立 Active Directory 應用程式，使我們可以確認我們服務之間的連線是透過已驗證的通訊來進行。 針對這些欄位，請建立 AD 應用程式並貼上相對應的所需租用戶識別碼和應用程式識別碼。 |
|  |  |


最後，如果您選取 [透過 Azure 銷售]，還會有一個稱為 [方案] 的額外區段。 [方案] 只有在已選取 [透過 Azure 銷售] 的情況下才有需要。 此區段會列出您的 SaaS 應用程式所支援的特定方案，以及其相對應的價格。 就目前為止，我們僅允許每月計價，以及能提供 1 個月或 3 個月免費存取的能力。 這些方案和價格應該要符合您實際在自己的 SaaS 應用程式網站上所張貼的方案和價格。
