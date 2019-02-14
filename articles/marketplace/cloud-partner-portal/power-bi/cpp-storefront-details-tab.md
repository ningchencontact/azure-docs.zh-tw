---
title: Power BI 應用程式供應項目的電子店面詳細資料 - Azure Marketplace |Microsoft Docs
description: 為 Microsoft AppSource Marketplace 的 Power BI 應用程式供應項目設定電子店面詳細資料。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/30/2019
ms.author: pbutlerm
ms.openlocfilehash: ffc0d8e9e41cf4d202b4040ce3f09e7180101c8b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665839"
---
# <a name="power-bi-apps-storefront-details-tab"></a>Power BI 應用程式 [電子店面詳細資料] 索引標籤

使用 [新增供應項目] 頁面的 [電子店面詳細資料] 索引標籤來為您的潛在客戶提供行銷、銷售和法律資訊。 此索引標籤也可指定如何管理來自市集的潛在客戶。 此長表單分為六個區段：**供應項目詳細資料**、**清單詳細資料**、**行銷成品**、**法律聲明**、**客戶支援**和**潛在客戶管理**。  欄位名稱上附加星號 (*) 表示為必填欄位。


## <a name="offer-details-section"></a>供應項目詳細資料區段

在此區段中輸入您 App Source 供應項目的一般資訊。

![店面詳細資料索引標籤的供應項目詳細資料區段](./media/offer-details-section.png)

下表描述這些欄位的用途和名稱。

|   欄位               |   說明                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **供應項目摘要**     | 簡短的應用程式用途。 長度上限是 100 個字元。                             |
| **供應項目描述** | 應用程式的描述。 長度上限是 3000 個字元，支援簡單的 HTML 格式。 |
|                       |                                                                                         |


## <a name="listing-details-section"></a>清單詳細資料區段

第二個區段提供您應用程式的額外內容：一般會使用它的產業有哪些、它最適合的分類為何、相容的產品，以及相關聯的搜尋詞彙。

![店面詳細資料索引標籤的清單詳細資料區段](./media/listing-details-section.png)

 下表描述這些欄位的用途和名稱。
 
|   欄位                                  |   說明                                                        |
| --------------                           | ---------------------                                                |
| **產業**                           | 請選取您應用程式最適用的產業。 若您的應用程式與多個產業相關聯，您可以保留空白。      |
| **類別**                           | 選取與您應用程式相相關的類別。 最多可選取 3 個。     |
| **應用程式的說明連結**               | 連接到有您應用程式線上說明頁面的 URL           |
| **可搭配您應用程式的產品 (最多 3 個)** | 列出可搭配您應用程式的特定產品。 您最多可以列出 3 個產品。 若要列出產品，請按一下位於 [新增] 旁的加號，隨即會為您建立新的開放文字欄位，供您輸入可搭配您應用程式的產品名稱。      |
| **搜尋關鍵字 (最多 3 個)**              | 客戶可利用 AppSource 根據關鍵字搜尋。 您可輸入一組您應用程式會展現給客戶的關鍵字。 例如，如果應用程式是「My Emailing app」，則 Emails、Mailing、Mail app 可能是一些關鍵字。 在 AppSource 搜尋方塊中選擇使用者可能會用來搜尋您應用程式的字詞。 |
|  |  |


## <a name="marketing-artifacts-section"></a>行銷成品區段

第三個區段可讓您上傳商標和行銷材料。  它分為四個子區段：[標誌]、[影片]、[文件] 和 [螢幕擷取畫面]。 標誌和螢幕擷取畫面是必要的行銷成品，但強烈建議您填寫所有項目，以提升對客戶的吸引力。

![店面詳細資料索引標籤的行銷成品區段](./media/marketing-artifacts-section.png)

 
|    欄位                             |    說明                                                    |
|   -----------                        |    -------------                                                  |
| *標誌*                              |                                                                   |
| **供應項目標誌 (png 格式，48x48)**   | 在完成搜尋時，此標誌會在 AppSource 上顯示於應用程式的概觀或應用程式結果中。 僅支援解析度為 48px\*48px 的 PNG 格式。  |
| **供應項目標誌 (PNG 格式，216x216)** | 顯示於 AppSource 上您應用程式的詳細資料頁面。  僅支援解析度為 216px\*216px 的 PNG 格式。  |
| *影片*                             |                                                                   |
| **名稱**                             | 應用程式的名稱或標題                                          |
| **URL**                              | 裝載於 YouTube 或 Vimeo 的影片 URL                              |
| **縮圖**                        | 應用程式的縮圖影像。  僅支援解析度為 1280px\*720px 的 PNG 格式。   |
| *文件*                          | 選擇性 (最多三個文件)。 您在此處上傳的文件會出現在 AppSource 的 [深入了解] 下方。  |
| **名稱**                             | 支援文件的名稱或標題                              |
| **檔案**                             | 上傳的文件必須是 PDF 格式                             |
| *螢幕擷取畫面*                        | (選擇性) 螢幕擷取畫面上限為五個。                        |
| **名稱**                             | 螢幕擷取畫面的名稱或標題                                       |
| **映像**                            | 上傳畫面擷取影像，必須是解析度為 1280px\*720px 的 PNG 格式  | 
|   |   |


### <a name="logo-guidelines"></a>標誌指導方針

[Cloud Partner 入口網站](https://cloudpartner.azure.com)中所上傳的所有標誌都應該遵循指導方針︰

- 不要在標誌上使用漸層背景。
- 避免在標誌上放置文字，包括公司或品牌名稱。 您標誌的外觀與風格應該很「平面」，而且應避免使用漸層。
- 不要延展標誌。


## <a name="legal-section"></a>法律聲明區段

第四個區段可讓您提供每個供應項目都需要的兩個法律文件：隱私權原則及使用規定。

![店面詳細資料索引標籤的法律聲明區段](./media/legal-section.png)

|   欄位                |   說明                           |
|------------------------|--------------------------------------   |
| **隱私權原則 URL** | 張貼隱私權原則的 URL       |
| **使用規定**       | 純文字或簡單 HTML 原則     |
|  |  |


## <a name="customer-support-section"></a>客戶支援服務區段

提供您線上客戶支援頁面的 [支援 URL]。  建議在此線上支援頁面提供客戶多種連絡選項，如電話、電子郵件和即時聊天。 


## <a name="lead-management-section"></a>潛在客戶管理區段

最後一個區段可讓您收集產生自您 AppSource 供應項目的潛在客戶。 此區段提供下列潛在客戶資訊的儲存選項 (位於下拉式清單)。

|    欄位               |   潛在客戶目的地                               |
|------------------------|--------------------------------------            |
|  **None**              | 不收集潛在客戶 (預設值)。  |
| **Azure Blob (已淘汰)** | 以容器名稱和連接字串指定的 [Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)。  此選擇已淘汰，請改為使用 [Azure 資料表]。  |
| **Azure 資料表**        | 以連接字串指定的 [Azure 資料表](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview)  |
| **Dynamics CRM Online** | 以 URL 和 驗證認證指定的 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 執行個體 |
| **HTTPS 端點**     | 作為 JSON 承載的指定 HTTPS 端點   |
| **Marketo**            | 以伺服器識別碼、munchkin 識別碼和表單識別碼指定的 [Marketo](https://www.marketo.com/) 執行個體   |
| **Salesforce**         | 以物件識別碼指定的 [Salesforce](https://www.salesforce.com/) 資料庫 |
|  |  |

發行供應項目之後，即已驗證潛在客戶連線，而且測試潛在客戶會自動傳送到您指定的目的地。 應持續管理潛在客戶資訊，且應立即更新這些設定，以反映您目前的客戶管理架構。


## <a name="next-steps"></a>後續步驟

您可以在下一個[連絡人](./cpp-contacts-tab.md)索引標籤中，為供應項目提供技術和使用者支援資源。
