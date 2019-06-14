---
title: 發佈 Azure 容器映像供應項目 |Azure Marketplace
description: 如何發佈 Azure 容器供應項目。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 9433673e464beb2df74eb4f49851e960d2e7f99c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942653"
---
# <a name="publish-container-offer"></a>發佈容器供應項目

 使用 [新增供應項目]  頁面建立新的供應項目後，您可以發佈該供應項目。 選取 [發佈]  即可啟動發佈程序。

下圖將展示供應項目「上線」發佈程序中主要的步驟。

![發佈容器供應項目的步驟](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>發佈步驟的詳細說明

下表說明每個發佈步驟。 也會提供完成每個步驟的預估時間。


|  **發佈步驟**           | <bpt id="p1">**</bpt>Time<ept id="p1">**</ept>    | **說明**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 檢查必要條件         | 15 分鐘   | 檢查供應項目資訊和供應項目設定。                        |
| 認證                  | 1 週 | Azure 認證團隊會分析供應項目。 掃描供應項目中是否有病毒、惡意程式碼、安全合規性和安全性問題。 檢查供應項目以查看它是否符合所有資格條件。 如需詳細資訊，請參閱[先決條件](./cpp-prerequisites.md)和[準備您的技術資產](./cpp-create-technical-assets.md)。 若發現問題，將提供意見反應。 |
| 包裝中 | 1 小時  | 供應項目的技術資產會封裝供客戶使用，潛在客戶系統也設定完畢。 |
|  發佈者登出             |  -        | 供應項目上線前發行者最終檢閱與確認。 您可以在所選訂用帳戶 (於供應項目資訊步驟) 部署供應項目，以確認它符合您的所有需求。  選取 [Go Live]  讓供應項目進行下一個步驟。 |
| 包裝中                 | 1 小時 | 完成的供應項目會在市集生產系統和區域產生複本。 | 
| 即時                           | 4 天 |供應項目推出、複寫到必要磁碟區域，並開放給大眾使用。 |

完成發佈程序並推出供應項目最多需要 10 個工作天。 完成發佈程序之後，您的容器供應項目將會列在 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 中。

## <a name="next-steps"></a>後續步驟

[更新 Azure Marketplace 上的現有容器供應項目](./cpp-update-existing-offer.md)
