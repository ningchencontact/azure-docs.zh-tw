---
title: Dynamics 365 for Customer Engagement 技術資訊索引標籤 - Azure Marketplace | Microsoft Docs
description: 如何在 AppSource Marketplace 上指定 Dynamics 365 for Customer Engagement 應用程式的技術資訊。
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
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
ms.date: 03/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 1dd488c2eb419b5e210a48d7a94f7d0bb423a2b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332843"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 for Customer Engagement 技術資訊索引標籤

[新供應項目] 頁面上的 [技術資訊] 索引標籤可讓您指定有關 Dynamics 365 for Customer Engagement 應用程式的詳細資訊，包括 CRM 套件和行銷標誌資產。  此索引標籤分成四個區段：**應用程式資訊**、**CRM 套件**、**CRM 套件可用性**和**行銷成品**。 欄位名稱上附加星號 (*) 表示為必填欄位。 


## <a name="application-info-section"></a>應用程式資訊區段

您要在此區段中提供 Dynamics 365 應用程式的相關詳細資料。

![[技術資訊] 索引標籤的 [應用程式資訊] 區段](./media/dynce-technical-info-tab1.png)

下表描述這些欄位。

|      欄位                    |    描述                  |
|    ---------                  |  ---------------                |
|   基本授權模型          |  授權模型會決定客戶如何在 Dynamics 365 系統管理中心指派您的應用程式。 **資源**授權是執行個體型，其中一個租用戶獲指派一個**使用者**授權。  |
|  S2S 輸出與 CRM 安全存放區存取 |  啟用 CRM 安全存放區或伺服器對伺服器 (S2S) 輸出存取的組態。 這項功能需要 Dynamics 365 小組在認證階段期間的特殊考量。 Microsoft 會連絡您完成其他步驟，以支援這項功能。  |
| 訂閱 CRM 生命週期事件 | 與 Dynamics 365 生命週期事件的整合需要您提供透過特殊合約向 Microsoft 註冊的專用服務。 這項功能需要 Dynamics 365 小組在認證階段期間的特殊考量。 您會收到通知指示您完成其他步驟，以支援這項功能。  |
| 應用程式組態 URL | 網頁的 URL，可讓使用者設定應用程式 |
| 適用的 Dynamics 365 產品  | 選取此供應項目適用的 Dynamics 365 產品。 此供應項目會出現在 AppSource 中已選取的產品底下。  |
| 僅行銷變更         | 將這個選項設定為 [是] 表示只會對現有供應項目進行行銷/描述性變更。  這類變更可讓供應項目略過憑證和佈建階段。  |
|  |  |


## <a name="crm-package-section"></a>CRM 套件區段

您要在此區段中提供 AppSource 套件檔案的相關詳細資料。  這項資訊將由 Dynamics 365 驗證和認證小組使用。

![[技術資訊] 索引標籤的 [CRM 套件] 區段](./media/dynce-technical-info-tab2.png)

下表描述這些欄位。

|      欄位                    |    描述                  |
|    ---------                  |  ---------------                |
|  套件的檔案名稱     |  套件的檔案名稱 (.zip)。  這個名稱「不是」公用，而是由 Dynamics 365 認證小組在內部使用。  |
|  Url                          |  Azure 儲存體帳戶的 URL，其中包含已上傳的套件檔案。 此 URL 應包含唯讀 SAS 金鑰，讓我們的小組挑選要進行驗證的套件。  |
| 一個以上的 CRM 套件     | 只有當您以其他套件支援 CRM 的多個版本時，才選取 [是]。  每個版本都有您必須個別建立的對應套件檔案。  |
| 案例和使用案例資產   | 上傳應用程式的功能規格文件，供 Dynamics 365 驗證小組使用。  此規格的偏好格式是 [E2E 使用者案例範本](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx)。  |
|  |  |


## <a name="crm-package-availability-section"></a>CRM 套件可用性區段

在此區段中，選取您的應用程式可供客戶使用的地理區域。  部署至下列主權區域在認證程序期間「需要特殊權限和驗證」：[德國](https://docs.microsoft.com/azure/germany/)、[美國政府雲端](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)和 TIP。


## <a name="marketing-artifacts-section"></a>行銷成品區段

此區段需要您上傳應用程式標誌，用來在 AppSource Marketplace 中表示您的套件。  標誌影像必須是 PNG 格式，且大小為 255 x 115 像素。


## <a name="next-steps"></a>後續步驟

我們建議您藉由完成[試用產品索引標籤](./cpp-testdrive-tab.md)，提供應用程式的示範
