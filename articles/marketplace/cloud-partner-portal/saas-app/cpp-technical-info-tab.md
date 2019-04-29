---
title: Azure SaaS 應用程式供應項目技術設定 | Microsoft Docs
description: 在 Azure Marketplace 上設定 SaaS 應用程式供應項目的技術資訊。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 891d9b7b34e3d30efb46b69ef1aa75566fe634c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594315"
---
# <a name="saas-application-technical-info-tab"></a>SaaS 應用程式技術資訊索引標籤

[技術資訊] 索引標籤會提供 [技術設定] 表單。 使用此表單來挑選您要建立的 SaaS 應用程式 (App) 類型，並設定將應用程式提供給客戶的方式。

![技術設定表單](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>技術設定表單

此表單有 2 個欄位：[產品] 與 [召喚行動]。

### <a name="product-field"></a>產品欄位

您可以針對下列兩個電子店面提供 SaaS 應用程式：
- 讓商務使用者選取 [清單] 選項。
- 讓 IT 管理使用者選取 [透過 Microsoft 銷售]。
為了協助您決定要建置的 SaaS 應用程式類型，請閱讀[了解電子店面選取範圍](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection)。

#### <a name="sell-through-microsoft"></a>透過 Microsoft 銷售
若要建置這項體驗，您需要設定下列部分：

- 將您的 SaaS 服務網站連線至 Microsoft 的 SaaS API。 [SaaS 透過 Azure 銷售 - API ](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) 一文會說明建立此連線的方法。
- 在 Cloud Partner 入口網站的 [技術設定] 表單中啟用 [透過 Azure 銷售]，並提供必要資訊。 如需此計費模型及其實作方式的詳細資訊，請參閱 [SaaS - 透過 Azure 銷售](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions)。

  ![[透過 Microsoft 銷售] 表單](./media/saas-techinfo-sellthrough-ms.png)

下表描述 [透過 Microsoft 銷售] 的必要欄位。

|  **欄位名稱**   |  **說明**  |
|  ---------------  |  ---------------  |
|    預覽訂用帳戶識別碼               |    在公開提供您的供應項目之前，用來在預覽中測試它的所有 Azure 訂用帳戶識別碼。               |
|     開始使用指示              |   與客戶共用以協助他們連線至您 SaaS 應用程式的指示。 允許使用基本的 HTML 標記，例如：&lt;p&gt;、&lt;h1&gt;、&lt;li&gt; 等標記。                |
|    登陸頁面 URL  |   在客戶從 Azure 入口網站中取得應用程式之後，您將會引導他們進行登陸的網站 URL。 此 URL 也會是接收連線 API 以與 Microsoft 建立商務的端點。                |
|  連線 Webhook    |  針對 Microsoft 代表客戶傳送給您所需的所有非同步事件 (範例：Azure 訂用帳戶已經失效)，我們要求您提供連線 Webhook。 如果您尚未具有 Webhook 系統，最簡單的設定方式便是準備會接聽任何向它張貼的事件，並能適當處理它們的 HTTP 端點邏輯應用程式。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">在邏輯應用程式中透過 HTTP 端點呼叫、觸發或巢狀處理工作流程</a>。                |
|  Azure AD 租用戶識別碼和應用程式識別碼      |   在 Azure 入口網站內，我們需要您建立 Active Directory 應用程式，使我們可以確認我們服務之間的連線是透過已驗證的通訊來進行。 針對這些欄位，請建立 AD 應用程式並貼上相對應的所需租用戶識別碼和應用程式識別碼。 請注意，應用程式識別碼會與您的發行者識別碼相關聯。 因此，請確定應用程式識別碼與所有供應項目中的識別碼相同。             |


最後，如果您選取 [透過 Microsoft 銷售]，則有另一個名為**方案**的 [新增供應項目] 索引標籤。 

這個[方案索引標籤](./cpp-plans-tab.md)會列出您 SaaS 應用程式所支援的特定方案及其相對應的價格。 就目前為止，我們僅允許每月計價，以及能提供 1 個月或 3 個月免費存取的能力。 這些方案和價格應該要符合您實際在自己的 SaaS 應用程式網站上所張貼的方案和價格。

>[!NOTE] 
>只有在您選擇 [透過 Microsoft 銷售] 時，才需要使用方案。

### <a name="call-to-action-field"></a>召喚行動欄位

[召喚行動] 欄位可讓您挑選要在供應項目的擷取按鈕上出現的訊息。 有下列選項可供使用：

- 免費：如果您挑選此選項，系統就會提示您輸入試用 URL，讓客戶可從中存取您的 SaaS 應用程式。 例如： https://contoso.com/trial
- 免費試用：如果您挑選此選項，系統就會提示您輸入試用 URL，讓客戶可從中存取您的 SaaS 應用程式。 例如： https://contoso.com/trial
- 與我連絡

如需 [召喚行動] 選項的詳細資訊，請參閱＜選擇發佈選項＞。

>[!Note]
>雲端解決方案提供者 (CSP) 合作夥伴通道選用功能現已開放下載。  請參閱[雲端方案提供者](../../cloud-solution-providers.md)如需有關行銷您的供應項目，透過 Microsoft CSP 合作夥伴的通道。

## <a name="next-steps"></a>後續步驟

- [方案索引標籤 (選擇性)](./cpp-plans-tab.md)
- [通道資訊索引標籤](./cpp-channel-info-tab.md)
