---
title: 自訂評估 Azure Migrate Server 評定 |Microsoft Docs
description: 描述如何自訂 Azure Migrate Server 評量與建立評定
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807917"
---
# <a name="customize-an-assessment"></a>自訂評量

本文說明如何自訂 Azure Migrate Server 評量所建立的評估。

[Azure Migrate](migrate-services-overview.md)提供一個中央中樞，來追蹤探索、 評估和移轉您的內部部署應用程式和工作負載和私密/公開金鑰雲端 Vm 至 Azure。 中樞會提供 Azure Migrate 的工具，來評估和移轉，以及第三方獨立軟體廠商 (ISV) 供應項目。

您可以使用 Azure Migrate Server 評量工具來建立評定內部部署 VMware Vm 和 HYPER-V Vm，以準備移轉至 Azure。 

## <a name="about-assessments"></a>關於評量

有兩種您可以使用 Azure Migrate Server 評定執行的評定。

**評量** | **詳細資料** | **資料**
--- | --- | ---
**以效能為基礎** | 根據所收集的效能資料的評量 | **建議的 VM 大小**:根據 CPU 和記憶體使用量資料。<br/><br/> **建議的磁碟類型 （標準或進階受控磁碟）** :根據 IOPS 和輸送量的內部部署磁碟。
**為內部部署** | 在內部部署調整大小所根據的評定。 | **建議的 VM 大小**:根據內部部署 VM 大小<br/><br> **建議的磁碟類型**:根據您選取評量的儲存體類型 設定。


## <a name="how-is-an-assessment-done"></a>如何完成評估？

Azure Migrate 評量有三個階段。 評量會從適用性分析開始，然後是調整大小，最後是每月成本估計。 機器必須先通過前一個評量，才可前往下一階段。 舉例而言，如果機器未通過 Azure 適用性檢查，就會標示為不適合 Azure，而不會再去計算大小和成本。

## <a name="whats-in-an-assessment"></a>評估包含什麼？

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要作為移轉目的地的 Azure 位置。<br/> Azure Migrate 目前支援這些目標區域：澳洲東部、 澳大利亞東南部、 巴西南部、 加拿大中部、 加拿大東部、 印度中部、 美國中部、 中國東部、 中國北部、 東亞、 美國東部、 美國東部 2、 德國中部、 德國東北部、 日本東部、 日本西部、 韓國中部、 韓國南部、 北美國中部、 北歐、 美國中南部、 東南亞、 印度南部、 英國南部、 英國西部、 美國亞歷桑那政府、 美國德州政府、 美國維吉尼亞州政府、 美國中西部、 西歐、 印度西部、 美國西部和美國西部 2。<br/> 根據預設，目標區域會設定為美國西部 2。
**儲存體類型** | 標準 HHD 標準磁碟/SSD 磁碟/進階。<br/> 當您指定的儲存體類型為自動評估時，磁碟建議根據磁碟 （IOPS 和輸送量） 的效能資料。<br/> 如果您指定的儲存體類型為進階/標準時，便會選取磁碟 SKU 中的儲存體類型評估建議。<br/> 如果您想要達到 99.9%的單一執行個體 VM SLA，您可以設定為進階受控磁碟的儲存體類型。 然後會建議評量中的所有磁碟，為進階受控磁碟。 <br/> Azure Migrate 只支援將受控磁碟用於進行移轉評估。<br/> 
**保留的執行個體 (RI)** | 如果您已保留在 Azure 中的執行個體，請指定這個屬性。 在評估中的成本的估計將 RI 的折扣納入考量。 保留的執行個體是目前僅支援在 Azure Migrate 提供的隨用隨付。
**調整大小準則** | 用來適當 Vm 大小。 調整大小可以是以效能為基礎，或是**作為內部**，而不必考量效能歷程記錄。
**效能歷程記錄** | 考量評估 VM 效能持續時間。 當調整大小是以效能為基礎時，這個屬性才適用。
**百分位數使用率** | 使用於適當調整 Vm 的效能取樣百分位數值。 當調整大小是以效能為基礎時，這個屬性才適用。
**VM 系列** | 用來估計大小的 VM 系列。 例如，如果您不打算將生產環境移轉至 Azure 中的 A 系列虛擬機器，則可以從清單或系列排除 A 系列。 大小調整只會根據選取的系列執行。
**緩和因數** | Azure Migrate Server 評量會在評估期間考量緩衝區 （緩和因數）。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。
**供應項目** | 您所註冊的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 會據以預估成本。
**貨幣** | 帳單貨幣。 
**折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。<br/> 預設設定為 0%。
**VM 運作時間** | 如果 VM不會在 Azure 中全天候執行，您可以指定其執行的持續時間 (每月天數和每天時數)，成本預估會據此來計算。<br/> 預設值是每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證，以及是否適合[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 | 預設值為 [是]。


## <a name="edit-assessment-properties"></a>編輯評量屬性

若要編輯評量屬性，建立評估之後，執行下列作業：

1. 在 Azure Migrate 專案中，按一下**伺服器**。
2. 在  **Azure Migrate:Server 評定**，按一下 評估計數。
3. 在 **評定**，按一下相關的評定 >**編輯屬性**。
5. 自訂評量屬性，根據上表。
6. 按一下 [儲存]  以更新評估。


當您建立評量時，您也可以編輯評量屬性。


## <a name="next-steps"></a>後續步驟

[深入了解](concepts-assessment-calculation.md)評定的計算方式。
