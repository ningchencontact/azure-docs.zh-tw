---
title: "自訂 Azure Migrate 評估設定 | Microsoft Docs"
description: "說明如何使用 Azure Migration Planner，設定和執行將 VMware VM 移轉至 Azure 的評估"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>自訂評估

[Azure Migrate](migrate-overview.md) 會使用預設設定建立評估。 在建立評估之後，您可以使用本文中的指示修改這些預設設定。


## <a name="edit-assessment-values"></a>編輯評估值

1. 在 Azure Migrate 專案的 [評估] 頁面上選取評估，然後按一下 [編輯屬性]。
2. 根據下表修改設定。

    **設定** | **詳細資料** | **預設值**
    --- | --- | ---
    **目標位置** | 要作為移轉目的地的 Azure 位置。 |  預設位置是「美國西部 2」。
    **儲存體備援** | Azure VM 會在移轉之後使用的儲存體類型。 | 目前只支援[本地備援儲存體 (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage)複寫。
    **緩和因數** | 緩和因數是評估期間所使用的緩衝。 我們會用它來考量各項因素，例如季節性使用量、簡短的效能歷程記錄，未來可能增加的使用量。 | 預設設定是 1.3x。
    **效能歷程記錄** | 用來評估效能歷程記錄的時間。 | 預設值為一個月。
    **百分位數使用率** | 針對效能歷程記錄所要考慮的百分位數值。 | 預設值為 95%。
    **定價層** | 您可以指定 VM 的[定價層](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/)。  | 預設會使用[標準](../virtual-machines/windows/sizes-general.md)層。
    **提供項目** | 適用的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 | 預設值是[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)。
    **貨幣** | 帳單貨幣。 | 預設值為美元。
    **折扣 (%)** | 針對任何供應項目所能獲得的訂用帳戶特定折扣。 | 預設設定為 0%。
    **Azure Hybrid Use Benefit** | 指出您是否已在 [Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) 中註冊。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 | 預設值為 [是]。

3. 按一下 [儲存] 以更新評估。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。
