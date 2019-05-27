---
title: 發佈 Azure SaaS 應用程式供應項目 |Azure Marketplace
description: 在 Azure Marketplace 上發佈 SaaS 應用程式供應項目的程序和步驟。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 5f4c38ed6ee19beacc67e29d094a20f5576668d6
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833472"
---
# <a name="publish-a-saas-application-offer"></a>發佈 SaaS 應用程式供應項目

在 [新增供應項目] 頁面提供資訊並建立新供應項目後，即可發佈供應項目。 選取 [發佈] 即可啟動發佈程序。

> [!IMPORTANT] 
> SaaS 提供的功能正移轉至[Microsoft 合作夥伴中心](https://partner.microsoft.com/dashboard/directory)。  所有新的發行者必須使用合作夥伴中心建立新的 SaaS 供應項目，以及管理現有的供應項目。  SaaS 供應項目目前的發行者 batchwise 移轉從 Cloud Partner 入口網站來合作夥伴中心。  Cloud Partner 入口網站會顯示狀態訊息，指出何時已移轉特定的現有供應項目。

下圖顯示發佈新 SaaS 應用程式供應項目的概略步驟。

![供應項目發佈步驟](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>發佈步驟的詳細說明

下列表格將說明各個發佈步驟，以及完成該步驟估計所需時間 (最大值)。

|     **Step**       |     <bpt id="p1">**</bpt>Time<ept id="p1">**</ept>      |  **說明**  |
|  ---------------   |  ---------------  |  ---------------  |
|         認證           |       2 週            |          供應項目將由 Azure 認證團隊負責分析。 此步驟會掃描病毒、惡意程式碼，並檢查安全合規性和安全性問題。 它也會驗證此供應項目符合所有適用性準則 (請參閱[必要條件](./cpp-prerequisites.md))。 若發現問題，將提供意見反應。         |
|           封裝         |       1 小時            |       供應項目的技術資產會封裝供客戶使用，潛在客戶系統也設定完畢。            |
|        發行者簽核            |         -          |        供應項目上線前發行者最終檢閱與確認 您可以在所選訂用帳戶 (於供應項目資訊步驟) 部署供應項目，以確認它符合您的所有需求。 選取 [Go Live] 讓供應項目進行下一個步驟。           |
|        封裝            |        1 小時           |        最終完成的供應項目會在市集生產系統和磁碟區域產生複本。           |
|        即時            |       4 天            |         供應項目推出、複寫到必要磁碟區域，並開放給大眾使用。          |

完成發佈程序並推出供應項目最多需要 10 個工作天。 完成發佈程序之後，您的 SaaS 供應項目將會列在 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 中。

## <a name="next-steps"></a>後續步驟

[更新現有的供應項目](./cpp-update-existing-offer.md)
