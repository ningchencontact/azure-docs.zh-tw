---
title: Azure Migrate 的相依性視覺效果 | Microsoft Docs
description: 概括介紹 Azure Migrate 服務中的評量計算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 2b6b3e78436a215e64894b1102c4e109f72c896d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203062"
---
# <a name="dependency-visualization"></a>相依性視覺效果

[Azure Migrate](migrate-overview.md) 服務會評估要移轉至 Azure 的內部部署機器群組。 若要將多台機器設定為群組，您可以使用相依性視覺效果。 本文提供此功能的相關資訊。


## <a name="overview"></a>概觀

Azure Migrate 的相依性視覺效果可建立用於移轉評估的群組，藉以提升信心。 您可以使用相依性視覺效果，檢視特定電腦或一整個機器群組的網路相依性。 若有多部電腦在執行應用程式和工作負載，這就相當實用，可確保在移轉流程中不漏失任何功能 (或遺忘機器)。  

## <a name="how-does-it-work"></a>運作方式

為提供相依性的視覺效果，Azure Migrate 採用了 [Log Analytics](../log-analytics/log-analytics-overview.md) 的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案。
- 您建立 Azure 移轉專案時，會在您的訂用帳戶中建立 Log Analytics 工作區。
- 工作區名稱是您為移轉專案指定的名稱，前面加上 **migrate-**，後面則可選擇要不要加數字。 
- 從專案 [概觀] 頁面的 [基本資訊] 區段中，瀏覽至 Log Analytics 工作區。
- 建立的工作區會以索引鍵 **MigrateProject** 和**專案名稱**的值標記。 您可以使用上述資訊在 Azure 入口網站進行搜尋。  

    ![Log Analytics 工作區](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用相依性視覺效果，您需要在待分析的每個內部部署機器上，下載及安裝代理程式。  

## <a name="do-i-need-to-pay-for-it"></a>我需要支付多少費用？

不須額外費用即可使用 Azure Migrate。 使用 Azure Migrate 的相依性視覺效果功能時，需要服務對應。 在建立 Azure Migrate 專案時，Azure Migrate 會自動代替您建立新的 Log Analytics 工作區。

> [!NOTE]
> 相依性視覺效果功能會透過 Log Analytics 工作區使用服務對應。 Azure Migrate 於 2018 年 2 月 28 日 宣告正式運作，現在可以免費使用該功能。 您必須建立新的專案，才能使用免費的工作區。 在正式運作之前存在的工作區仍需收費，因此建議您移至新的專案。

1. 在此 Log Analytics 工作區內使用服務對應以外的任何解決方案，將會產生標準 Log Analytics 費用。 
2. 為了支援不需額外成本的移轉案例，自 Azure Migrate 專案建立算起的前 180 天，服務對應解決方案將不會產生任何費用，而 180 天則適用標準收費方式。
3. 只有在建立專案的過程中建立工作區，才可免費使用。

當您將代理程式註冊至工作區時，請使用專案在安裝代理程式步驟頁面上提供的識別碼和金鑰。 您無法使用現有的工作區並將它與 Azure Migrate 專案產生關聯。

刪除 Azure Migrate 專案時，工作區不會隨之刪除。 在專案刪除後，服務對應就無法免費使用，系統會根據 Log Analytics 工作區的付費層向每個節點收取費用。

[在此](https://azure.microsoft.com/pricing/details/azure-migrate/)深入了解 Azure Migrate 定價。 

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作區？

您可以使用 Azure Migrate 外部的 Log Analytics 工作區。 如果您刪除建立工作區的移轉專案，並不會刪除工作區。 如果不再需要工作區中，請手動[刪除工作區](../log-analytics/log-analytics-manage-access.md)。

除非您刪除移轉專案，否則請勿刪除 Azure Migrate 建立的工作區。 如果您這樣做，相依性將無法如預期般正常運作。

## <a name="next-steps"></a>後續步驟

[使用機器相依性的群組機器](how-to-create-group-machine-dependencies.md)