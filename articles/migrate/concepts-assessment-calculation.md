---
title: Azure Migrate 中的評量計算 | Microsoft Docs
description: 概括介紹 Azure Migrate 服務中的評量計算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805977"
---
# <a name="assessment-calculations"></a>評量計算

Azure Migrate Server 評量會評估要移轉至 Azure 的內部工作負載。 本文提供評量計算方式的相關資訊。


[Azure Migrate](migrate-services-overview.md)提供中央的中樞，以追蹤探索、 評估和移轉您的內部部署應用程式和工作負載和私密/公開金鑰雲端執行個體，至 Azure。 中樞會提供 Azure Migrate 的工具，來評估和移轉，以及第三方獨立軟體廠商 (ISV) 供應項目。

## <a name="overview"></a>總覽

Azure Migrate Server 評估評量有三個階段。 評量會從適用性分析開始，然後是調整大小，最後是每月成本估計。 機器必須先通過前一個評量，才可前往下一階段。 舉例而言，如果機器未通過 Azure 適用性檢查，就會標示為不適合 Azure，而不會再去計算大小和成本。


## <a name="whats-in-an-assessment"></a>評估包含什麼？

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要作為移轉目的地的 Azure 位置。<br/><br/> Azure Migrate 目前支援這些目標區域：澳洲東部、 澳大利亞東南部、 巴西南部、 加拿大中部、 加拿大東部、 印度中部、 美國中部、 中國東部、 中國北部、 東亞、 美國東部、 美國東部 2、 德國中部、 德國東北部、 日本東部、 日本西部、 韓國中部、 韓國南部、 北美國中部、 北歐、 美國中南部、 東南亞、 印度南部、 英國南部、 英國西部、 美國亞歷桑那政府、 美國德州政府、 美國維吉尼亞州政府、 美國中西部、 西歐、 印度西部、 美國西部和美國西部 2。<br/> 根據預設，目標區域會設定為美國西部 2。
**儲存體類型** | 標準 HHD 標準磁碟/SSD 磁碟/進階。<br/><br/> 當您指定的儲存體類型為自動評估時，磁碟建議根據磁碟 （IOPS 和輸送量） 的效能資料。<br/><br/> 如果您指定的儲存體類型為進階/標準時，便會選取磁碟 SKU 中的儲存體類型評估建議。<br/><br/> 如果您想要達到 99.9%的單一執行個體 VM SLA，您可以設定為進階受控磁碟的儲存體類型。 然後會建議評量中的所有磁碟，為進階受控磁碟。 <br/> Azure Migrate 只支援將受控磁碟用於進行移轉評估。<br/> 
**保留的執行個體 (RI)** | 如果您已保留在 Azure 中的執行個體，請指定這個屬性。 在評估中的成本的估計將 RI 的折扣納入考量。 保留的執行個體是目前僅支援在 Azure Migrate 提供的隨用隨付。
**調整大小準則** | 用來適當 Vm 大小。 以效能為基礎，可以調整大小或-在內部，而不必考量效能歷程記錄。
**效能歷程記錄** | 考量評估 VM 效能持續時間。 當調整大小是以效能為基礎時，這個屬性才適用。
**百分位數使用率** | 使用於適當調整 Vm 的效能取樣百分位數值。 當調整大小是以效能為基礎時，這個屬性才適用。
**VM 系列** | 用來估計大小的 VM 系列。 例如，如果您不打算將生產環境移轉至 Azure 中的 A 系列虛擬機器，則可以從清單或系列排除 A 系列。 大小調整只會根據選取的系列執行。
**緩和因數** | Azure Migrate Server 評量會在評估期間考量緩衝區 （緩和因數）。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。
**供應項目** | 您所註冊的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 會據以預估成本。
**貨幣** | 帳單貨幣。 
**折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。<br/> 預設設定為 0%。
**VM 運作時間** | 如果 VM不會在 Azure 中全天候執行，您可以指定其執行的持續時間 (每月天數和每天時數)，成本預估會據此來計算。<br/> 預設值是每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證，以及是否適合[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 



## <a name="azure-suitability-analysis"></a>Azure 適用性分析

並非所有機器都都適用於在 Azure 中執行。 Azure Migrate Server 評量會評估移轉，每個內部部署機器，並將機器分類成下列適用性類別之一：
- **可供 Azure 使用** - 機器不進行任何變更，即可依原樣移轉至 Azure。 它會在 Azure 中開機，並具有完整的 Azure 支援。
- **可有條件地供 Azure 使用** - 機器可在 Azure 中開機，但無法獲得完整的 Azure 支援。 例如，Azure 中不支援使用較舊版本 Windows Server 作業系統的機器。 將這些機器移轉至 Azure 之前需要特別注意，並請在移轉之前，遵循評量中建議的補救指導方針來修正整備程度問題。
- **無法供 Azure 使用** - 機器在 Azure 中將無法開機。 例如，如果內部部署機器有大小超過 4 TB、與其連接的磁碟，則無法將它裝載在 Azure 上。 在移轉至 Azure 之前，您需要遵循評量中建議的補救指導方針來修正整備程度問題。 對於標示為未備妥供 Azure 使用的機器，未完成適當的大小調整和成本估計。
- **整備程度未知** - 由於 vCenter Server 中的可用資料不足，Azure Migrate 找不到機器的整備程度。



### <a name="machine-properties"></a>機器屬性

Azure Migrate 檢閱內部部署 VM，以識別是否在 Azure 上執行的下列屬性。

**屬性** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
**開機類型** | Azure 支援開機類型為 BIOS (而非 UEFI) 的 VM。 | 如果開機類型為 UEFI，便已有條件地就緒。
**核心** | 機器的核心數目必須等於或小於 Azure VM 支援的核心數目上限 (128 個核心)。<br/><br/> 如果有提供效能記錄，則 Azure Migrate 會將已使用的核心納入考量，進行比較。 如果評量設定已指定緩和因數，則會將使用的核心數目乘以緩和因數。<br/><br/> 如果沒有效能記錄，Azure Migrate 會使用配置的核心，而不套用緩和因數。 | 小於或等於限制便就緒。
**記憶體** | 機器的記憶體大小必須等於或小於 Azure VM 允許的記憶體上限 (Azure M 系列 Standard_M128m 上 3892 GB &nbsp;<sup>2</sup>)。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果有提供效能記錄，Azure Migrate 會將已使用的記憶體納入考量，進行比較。 如果已指定緩和因數，則會將使用的記憶體乘以緩和因數。<br/><br/> 如果沒有記錄，則會使用配置的記憶體，而不套用緩和因數。<br/><br/> | 在限制內便就緒。
**存放磁碟** | 已配置的磁碟大小必須小於或等於 4 TB (4096 GB)。<br/><br/> 附加至機器的磁碟數目必須小於或等於 65，作業系統磁碟亦包含在內。 | 在限制內便就緒。
**網路功能** | 機器必須附加 32 個以內的網路介面卡。 | 在限制內便就緒。

### <a name="guest-operating-system"></a>客體作業系統
隨著 VM 屬性，Azure Migrate Server 評量會探討的其他電腦上，以識別是否在 Azure 上執行客體作業系統。

> [!NOTE]
> Azure Migrate Server 評定會使用 VM 在 vCenter Server 中進行分析指定的作業系統。 Azure Migrate Server 評量是以設備為基礎，虛擬機器探索，並無法確認是否與 vCenter Server 中所指定相同的 VM 上執行的作業系統。

Azure Migrate Server 評量，用下列邏輯來識別作業系統為基礎的 Azure 移轉整備程度。

**作業系統** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
Windows Server 2016 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2012 R2 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2012 與所有 SP | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2008 R2，含所有 SP | Azure 提供完整支援。| 可供 Azure 使用
Windows Server 2008 (32 位元和 64 位元) | Azure 提供完整支援。 | 可供 Azure 使用
Windows Server 2003、2003 R2 | 這些作業系統已經過其支援結束的日期，需要[自訂支援合約 (CSA)](https://aka.ms/WSosstatement) 才能在 Azure 中獲得支援。 | 可有條件地供 Azure 使用，在移轉到 Azure 之前，請考慮升級作業系統。
Windows 2000、98、95、NT、3.1、MS-DOS | 這些作業系統已經過其支援結束的日期，機器可能可以在 Azure 中開機，但 Azure 不會提供作業系統支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前升級作業系統。
Windows Client 7、8 及 10 | Azure [僅對 Visual Studio 訂用帳戶](https://docs.microsoft.com/azure/virtual-machines/windows/client-images)提供支援。 | 可有條件地供 Azure 使用
Windows 10 專業版桌面 | Azure 對[多租用戶主機權限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)提供支援。 | 可有條件地供 Azure 使用
Windows Vista、XP Professional | 這些作業系統已經過其支援結束的日期，機器可能可以在 Azure 中開機，但 Azure 不會提供作業系統支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前升級作業系統。
Linux | Azure 認同這些 [Linux 作業系統](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 作業系統可能可以在 Azure 中開機，但建議您在移轉至 Azure 之前，將作業系統升級為認同的版本。 | 如果版本受到認同，則可供 Azure 使用。<br/><br/>如果版本未受到認同，則有條件地可供使用。
其他作業系統<br/><br/> 例如，Oracle Solaris、Apple Mac OS 等、FreeBSD 等。 | Azure 並未認同這些作業系統。 機器可能可以在 Azure 中開機，但 Azure 不會提供作業系統支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前安裝支援的作業系統。  
在 vCenter Server 中作業系統指定為**其他** | Azure Migrate 無法在此情況下識別作業系統。 | 整備程度未知。 請確保 Azure 支援 VM 內執行的作業系統。
32 位元作業系統 | 機器可能可以在 Azure 中開機，但 Azure 可能不會提供完整支援。 | 可有條件地供 Azure 使用，在移轉到 Azure 之前，請考慮將機器的作業系統從 32 位元作業系統升級至 64 位元作業系統。

## <a name="sizing"></a>調整大小

將機器標示為可供 Azure 使用之後，Azure Migrate 會為 Azure 調整 VM 大小和其磁碟。

- 如果評定會使用以效能為基礎的調整大小，Azure Migrate 會考慮效能歷程記錄，以識別在 Azure 中的 VM 大小和磁碟類型的機器。 如果您已過度配置在內部部署 VM，但使用率很低，且您想要正確大小來節省成本的 Azure 中的 VM，則這個方法會特別有幫助。
- 如果您是使用做為內部評估，Azure Migrate Server 評量會調整大小，以在內部部署的設定值，而不考慮使用量資料的 Vm。 磁碟大小，在此情況下，根據您在評估屬性 （標準磁碟或進階磁碟） 中指定的儲存體類型。

### <a name="performance-based-sizing"></a>以效能為基礎調整大小

對於以效能為基礎的大小調整，Azure Migrate 開始連接至 VM，後面接著網路介面卡的磁碟，然後對應 Azure VM 根據內部部署 VM 的計算需求。

- **儲存體**：Azure Migrate 嘗試將連接至機器的每個磁碟對應至 Azure 中的磁碟。
    - 為了獲得有效的每秒磁碟 I/O (IOPS) 和輸送量 (MBps)，Azure Migrate 會將磁碟 IOPS 和輸送量乘上緩和因數。 根據有效的 IOPS 及輸送量值，Azure Migrate 會識別磁碟應該對應至 Azure 中的標準或進階磁碟。
    - 如果 Azure Migrate 找不到 IOPS 及輸送量符合需求的磁碟，便會將該機器標示為不適合 Azure。 [進一步了解](../azure-subscription-service-limits.md#storage-limits)關於每個磁碟和 VM 的 Azure 限制。
    - 如果找到多個適合的磁碟，Azure Migrate 會挑選可支援儲存體備援方法的磁碟，以及可支援評量設定的指定位置的磁碟。
    - 如果有多個符合資格的磁碟，則會選取成本最低的磁碟。
    - 如果磁碟的效能資料無法使用時，所有磁碟都會都對應至 Azure 中的標準磁碟。

- **網路**：Azure Migrate 會嘗試尋找可支援附加至內部部署機器的網路介面卡數量以及這些網路介面卡所需效能的 Azure VM。
    - 為了獲得內部部署 VM 的有效網路效能，Azure Migrate 會彙總在所有的網路介面卡間每秒從機器傳出的資料 (MBps) (網路對外)，並套用緩和因數。 會使用此數字來尋找可支援所要求的網路效能的 Azure VM。
    - 隨著網路效能，它也會考慮 Azure VM 是否可支援所需的網路介面卡數目。
    - 如果沒有可用的網路效能資料，調整虛擬機器大小時只會僅會考量網路介面卡的數量。

- **計算**：計算儲存體和網路需求之後，Azure Migrate 會考慮在 Azure 中尋找適當的 VM 大小的 CPU 和記憶體需求。
    - Azure Migrate 查看使用的核心和記憶體，並套用緩和因數以獲得有效的核心和記憶體。 Azure Migrate 會根據該數字，嘗試在 Azure 中尋找適當的虛擬機器大小。
    - 如果找不到合適的大小，便會將機器標示為不適合 Azure。
    - 如果找到合適的大小，Azure Migrate 會套用儲存體與網路計算。 然後再套用位置及定價層設定，對虛擬機器大小提出最終建議。
    - 如果有多個符合資格的 Azure VM 大小，建議使用成本最低的那個。

### <a name="as-on-premises-sizing"></a>作為內部部署調整大小

如果您使用就像在內部部署調整大小，Server 評定會配置內部大小為基礎的 Azure 中的 VM SKU。 與調整磁碟大小類似，這會查看評估內容中指定的儲存類型 (標準/進階)，並據此建議磁碟類型。 進階磁碟為預設儲存體類型。

## <a name="confidence-ratings"></a>信賴評等
在 Azure Migrate 中每個以效能為基礎的評量是相關聯的範圍是從 1 （最低） 至五個開始 （最高） 的信賴評等。
- 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。
- 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。
- 信賴評等並不適用於內部評估。
- 對於以效能為基礎的大小調整，Azure Migrate Server 評估需要：
    - CPU 使用量資料和 VM 的記憶體。
    - 此外，對於連結至 VM 的每個磁碟，它都需要磁碟 IOPS 和輸送量資料。
    - 同樣地連結至 VM 的每個網路介面卡，信賴評等會需要進行以效能為基礎的調整大小的網路 I/O。
    - 如果任何上述的使用量數字都無法使用 vCenter Server 中，可能不可靠的大小建議。 

根據可用資料點的百分比提供評量的信賴評等如下：

   **資料點的可用性** | **信賴評等**
   --- | ---
   0%-20% | 1 顆星
   21%-40% | 2 顆星
   41%-60% | 3 顆星
   61%-80% | 4 顆星
   81%-100% | 5 顆星

### <a name="low-confidence-ratings"></a>低信賴評等

幾個評量為何無法取得低信賴評等的原因：

- 您未針對您要為其建立評量的持續時間來分析您的環境。 例如，如果您建立評量設定為 1 天的效能持續時間，您需要等待至少一天開始以取得所收集的所有資料點的探索。
- 一些 Vm，評量計算期間已關閉。 如果任何 Vm 電源關閉段期間，Azure Migrate Server 評估無法收集效能資料，該期間內。
- 為其評量計算期間已建立一些 Vm。 例如，如果您建立的評估效能歷程的過去一個月，但只在一週前建立一些 Vm 在環境中，新的 Vm 的效能歷程記錄不會有的整個持續期間。

  > [!NOTE]
  > 如果任何評量的信賴評等低於 5 顆星，建議您至少等候一天來分析環境中，應用裝置，然後重新計算評定。 如果不這麼做，以效能為基礎的調整大小可能不可靠，我們建議您改用評估，以做為內部部署調整大小。
  
## <a name="monthly-cost-estimation"></a>每月成本預估

大小調整建議完成之後，Azure Migrate 計算移轉後的計算和儲存體成本。

- **計算成本**：使用建議的 Azure VM 大小時，Azure Migrate 會使用計費 API 來計算虛擬機器的每月成本。
    - 計算時會將作業系統、軟體保證、保留執行個體、VM 運作時間、位置和貨幣設定納入考量。
    - 其會彙總所有機器的成本，以計算每月計算總成本。
- **儲存成本**：機器的每月儲存成本的計算方式是彙總所有的磁碟連接至機器的每月成本
    - Azure Migrate Server 評量來計算每月的總儲存體成本的彙總所有機器的儲存體成本。
    - 目前在計算時不會將評量設定中指定的供應項目列入考量。

成本會以評量設定中指定的貨幣顯示。


## <a name="next-steps"></a>後續步驟

建立的評估[VMware Vm](tutorial-assess-vmware.md)或是[HYPER-V Vm](tutorial-assess-hyper-v.md)。
