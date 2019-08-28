---
title: Azure Migrate 的相依性視覺效果 | Microsoft Docs
description: 概述伺服器評定服務中的評估計算, Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 4b6a140ec428ce3b053c41074f02f65f19b8dc72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102900"
---
# <a name="dependency-visualization"></a>相依性視覺效果

Azure Migrate：伺服器評估會評估要遷移至 Azure 的內部部署機器群組。 您可以使用伺服器評估中的相依性視覺效果功能來建立群組。 本文提供此功能的相關資訊。

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

## <a name="overview"></a>總覽

伺服器評估中的相依性視覺效果可讓您建立高信賴度群組來進行遷移評量。 使用相依性視覺效果, 您可以查看電腦的網路相依性, 並識別需要一起遷移至 Azure 的相關電腦。 當您並不完全了解構成應用程式和需要一起移轉到 Azure 的機器時，此功能會很實用。

## <a name="before-you-start"></a>開始之前

- 請確定您已[建立](how-to-add-tool-first-time.md)Azure Migrate 專案。
- 如果您已經建立專案, 請確定您已[新增](how-to-assess.md)Azure Migrate:伺服器評量工具。
- 請確定您已在 Azure Migrate 中探索到您的電腦。若要這麼做, 您可以設定[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 設備。 設備會探索內部部署機器, 然後將中繼資料和效能資料傳送至 Azure Migrate:。 [深入了解](migrate-appliance.md)。

## <a name="how-does-it-work"></a>運作方式

Azure Migrate 使用[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)中的[服務對應](../operations-management-suite/operations-management-suite-service-map.md)解決方案來進行相依性視覺效果。
- 若要利用相依性視覺效果，您需要將新的或現有的 Log Analytics 工作區與 Azure Migrate 專案建立關聯。
- 您只能在建立 Azure Migrate 專案的相同訂用帳戶中, 建立或附加工作區。
- 若要將 Log Analytics 工作區附加至專案:
    1. 在 [伺服器] 索引標籤的 **[Azure Migrate：伺服器評估** ] 磚中, 按一下 **[總覽**]。
    2. 在 **[總覽**] 中, 按一下向下箭號以展開 [**基本**]。
    3. 在 [ **OMS 工作區**] 中, 按一下 [**需要**設定]。
    4. 在 [**設定工作區**] 中, 指定您要建立新的工作區, 或使用現有的工作區:
    
    ![新增工作區](./media/how-to-create-group-machine-dependencies/workspace.png)

- 與工作區建立關聯時，您可以選擇建立新的工作區，或連結現有的工作區：
  - 建立新工作區時，您必須指定工作區的名稱。 然後會在和移轉專案相同之 [Azure 地理區](https://azure.microsoft.com/global-infrastructure/geographies/)的區域中建立工作區。
  - 當您連結現有的工作區時，您能以和移轉專案相同的方式，從相同訂用帳戶中所有的可用工作區中挑選。 請注意，系統只會列出那些在[支援服務對應](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)的區域中建立的工作區。 若要能夠連結工作區，請確定您有工作區的「讀取者」存取權。

  > [!NOTE]
  > 一旦您將工作區連結到專案，您之後便無法變更它。

- 相關聯的工作區會以索引鍵 **Migration Project** 和**專案名稱**的值標記，您可用來在 Azure 入口網站中搜尋。
- 若要瀏覽到與專案相關聯的工作區，您可以移至專案 [概觀] 頁面的 [基本資訊] 區段，然後存取該工作區

    ![瀏覽 Log Analytics 工作區](./media/concepts-dependency-visualization/oms-workspace.png)

若要使用相依性視覺效果，您需要在待分析的每個內部部署機器上，下載及安裝代理程式。  

- 必須在每個機器上安裝 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。 [深入瞭解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma)如何安裝 MMA 代理程式。
- 必須在每個機器上安裝[相依性代理程式](../azure-monitor/platform/agents-overview.md#dependency-agent)。 [深入瞭解](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent)如何安裝 dependency agent。
- 此外，如果您有無法連線至網際網路的機器，則必須在該機器上下載並安裝 Log Analytics 閘道。

您不需要在所要評量的機器上有這些代理程式，除非您使用相依性視覺效果。

## <a name="do-i-need-to-pay-for-it"></a>我需要支付多少費用？

相依性視覺效果功能免費提供。 在伺服器評估中使用相依性視覺效果功能需要服務對應, 而且會要求您將 Log Analytics 工作區 (新的或現有的) 與 Azure Migrate 專案相關聯。 伺服器評估中的相依性視覺效果功能在前180天免費。

1. 在此 Log Analytics 工作區內使用服務對應以外的任何解決方案，將會產生[標準 Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 費用。
2. 為了支援不需額外成本的移轉案例，自 Log Analytics 工作區與 Azure Migrate 專案建立關聯當天算起的前 180 天，服務對應解決方案將不會產生任何費用，而 180 天則適用標準收費方式。 經過 180 天後，會套用標準 Log Analytics 費用。

當您將代理程式註冊至工作區時，請使用專案在安裝代理程式步驟頁面上提供的識別碼和金鑰。

刪除 Azure Migrate 專案時，工作區不會隨之刪除。 在專案刪除後，服務對應就無法免費使用，系統會根據 Log Analytics 工作區的付費層向每個節點收取費用。

> [!NOTE]
> 相依性視覺效果功能會透過 Log Analytics 工作區使用服務對應。 Azure Migrate 於 2018 年 2 月 28 日 宣告正式運作，現在可以免費使用該功能。 您必須建立新的專案，才能使用免費的工作區。 在正式運作之前存在的工作區仍需收費，因此建議您移至新的專案。

[在此](https://azure.microsoft.com/pricing/details/azure-migrate/)深入了解 Azure Migrate 定價。

## <a name="how-do-i-manage-the-workspace"></a>如何管理工作區？

您可以使用 Azure Migrate 外部的 Log Analytics 工作區。 如果您刪除建立它的 Azure Migrate 專案, 則不會刪除它。 如果不再需要工作區中，請手動[刪除工作區](../azure-monitor/platform/manage-access.md)。

除非您刪除 Azure Migrate 專案, 否則請勿刪除 Azure Migrate 建立的工作區。 如果這樣做，相依性視覺效果功能不會如預期般運作。

## <a name="next-steps"></a>後續步驟
- [使用機器相依性的群組機器](how-to-create-group-machine-dependencies.md)
- [深入了解](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization)相依性視覺效果的常見問題集。
