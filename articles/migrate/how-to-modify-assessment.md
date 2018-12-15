---
title: 自訂 Azure Migrate 評估設定 | Microsoft Docs
description: 說明如何使用 Azure Migration Planner，設定和執行將 VMware VM 移轉至 Azure 的評估
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/30/2018
ms.author: raynew
ms.openlocfilehash: 2423c4fde177ab50552af580a60c7a15550e5586
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840430"
---
# <a name="customize-an-assessment"></a>自訂評量

[Azure Migrate](migrate-overview.md) 會使用預設屬性來建立評量。 在建立評量之後，您可以使用本文中的指示修改預設屬性。


## <a name="edit-assessment-properties"></a>編輯評量屬性

1. 在移轉專案的 [評量] 頁面中選取評量，然後按一下 [編輯屬性]。
2. 根據下列詳細資料自訂評量屬性：

    **設定** | **詳細資料** | **預設值**
    --- | --- | ---
    **目標位置** | 要作為移轉目的地的 Azure 位置。<br/><br/> Azure Migrate 目前支援 30 個區域，包括澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、德國中部、德國東北部、美國東部 2、日本東部、日本西部、南韓中部、南韓南部、美國中北部、北歐、美國中南部、東南亞、印度南部、英國南部、英國西部、US Gov 維吉尼亞州、US Gov 德克薩斯州、US Gov 維吉尼亞州、美國中西部、西歐、印度西部、美國西部和美國西部 2。 |  預設位置是「美國西部 2」。
    **儲存體類型** | 您可以使用此屬性，指定要在 Azure 中配置的磁碟類型。 針對內部部署的大小調整，您可以將目標磁碟類型指定為進階受控磁碟或標準受控磁碟。 針對以效能為基礎的大小調整，您可以將目標磁碟類型指定為自動、進階受控磁碟或標準受控磁碟。 將儲存體類型指定為自動時，將根據磁碟的效能資料 (IOPS 和輸送量) 完成磁碟建議。 例如，如果您想要達成 [99.9% 的單一執行個體 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，則可能需要將儲存體類型指定為進階受控磁碟，如此可確保將評估中的所有磁碟建議為進階受控磁碟。 請注意，Azure Migrate 只支援將受控磁碟用於進行移轉評估。 | 預設值為進階受控磁碟 (其調整大小準則為「作為內部部署調整大小」)。
    **保留執行個體** |  您也可以指定在 Azure 中是否有[保留執行個體](https://azure.microsoft.com/pricing/reserved-vm-instances/)，Azure Migrate 會據此預估成本。 保留執行個體不適用於主權區域 (Azure Government、Azure 德國和 Azure 中國)，只適用於 Azure Migrate 中的預付型方案供應項目。 | 這個屬性的預設值是 3 年期的保留執行個體。
    **調整大小準則** | Azure Migrate 用於為 Azure 設定適當 VM 大小的準則。 您可以執行*以效能為基礎*的大小調整或將 VM 調整為*內部部署*大小，而不必考量效能歷程記錄。 | 以效能為基礎的大小調整是預設選項。
    **效能歷程記錄** | 考量評估 VM 效能的持續時間。 當調整大小的準則為*以效能為基礎的大小調整*時，此屬性才適用。 | 預設值為一天。
    **百分位數使用率** | 要視為適當大小調整的效能取樣集百分位數值。 當調整大小的準則為*以效能為基礎的大小調整*時，此屬性才適用。  | 預設值為第 95 個百分位數。
    **VM 系列** | 您可以指定考慮要正確調整大小的 VM 系列。 例如，如果您不打算將生產環境移轉至 Azure 中的 A 系列虛擬機器，則可以從清單或系列排除 A 系列，而正確調整大小只會在選取的系列中完成。 | 預設會選取所有 VM系列。
    **緩和因數** | Azure Migrate 會在評估期間考量緩衝區 (緩和因數)。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 例如，10 核心的 VM，如果使用率只達 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。 | 預設設定是 1.3x。
    **供應項目** | 您註冊取得的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 | 預設值是[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)。
    **貨幣** | 帳單貨幣。 | 預設值為美元。
    **折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。 | 預設設定為 0%。
    **VM 運作時間** | 如果 VM不會在 Azure 中全天候執行，您可以指定其執行的持續時間 (每月天數和每天時數)，成本預估會據此來計算。 | 預設值是每月 31 天和每天 24 小時。
    **Azure Hybrid Benefit** | 指定您是否擁有軟體保證以及是否符合 [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 的資格。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 | 預設值為 [是]。

3. 按一下 [儲存] 以更新評估。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。
