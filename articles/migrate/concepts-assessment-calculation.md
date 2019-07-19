---
title: Azure Migrate 中的評量計算 | Microsoft Docs
description: 概括介紹 Azure Migrate 服務中的評量計算。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234299"
---
# <a name="assessment-calculations"></a>評量計算

[Azure Migrate](migrate-services-overview.md)提供中央中樞來追蹤內部部署應用程式和工作負載的探索、評估和遷移, 以及私人/公用雲端實例到 Azure 的能力。 中樞提供評估和遷移的 Azure Migrate 工具, 以及協力廠商獨立軟體廠商 (ISV) 產品。

伺服器評估是 Azure Migrate 中的一項工具, 可評估要遷移至 Azure 的內部部署伺服器。 本文提供評量計算方式的相關資訊。

## <a name="whats-in-an-assessment"></a>評估包含什麼？

**屬性** | **詳細資料**
--- | ---
**目標位置** | 要作為移轉目的地的 Azure 位置。<br/> 伺服器評估目前支援下列目的地區域:澳大利亞東部、澳大利亞東南部、巴西南部、加拿大中部、加拿大東部、印度中部、美國中部、中國東部、中國北部、東亞、美國東部、東部美國2、德國中部、德國東北部、日本東部、日本西部、韓國中部、南韓南部、北部美國中部、北歐、美國中南部、東南亞、印度南部、英國南部、英國西部、US Gov 亞利桑那州、US Gov 德克薩斯州、US Gov 維吉尼亞州、美國中西部、西歐、印度西部、美國西部和 West 美國2。
**儲存體類型** | 您可以使用此屬性, 在 Azure 中指定您想要移至的磁片類型。 <br/><br/> 針對「內部部署」大小調整, 您可以將目標儲存類型指定為高階受控磁片、標準 SSD 受控磁片或標準 HDD 受控磁片。 針對以效能為基礎的大小調整, 您可以將目標磁片類型指定為自動、高階受控磁片、標準 HDD 受控磁片, 或標準 SSD 受控磁片。<br/><br/> 將儲存體類型指定為自動時，將根據磁碟的效能資料 (IOPS 和輸送量) 完成磁碟建議。 如果您將儲存體類型指定為 [premium/standard], 則評量會建議所選儲存體類型內的磁片 SKU。 如果您想要達到 99.9% 的單一實例 VM SLA, 您可能會想要將儲存體類型指定為 Premium 受控磁片。 這可確保建議評量中的所有磁碟成為進階受控磁碟。 請注意，Azure Migrate 只支援將受控磁碟用於進行移轉評估。
**保留實例 (RI)** | 此屬性可協助您指定在 Azure 中是否有[保留實例](https://azure.microsoft.com/pricing/reserved-vm-instances/), 評估中的成本預估會完成 RI 折扣。 Azure Migrate 中的隨用隨付優惠目前僅支援保留執行個體。
**調整大小準則** | 要用來為 Azure 適當調整 Vm 大小的準則。 您可以執行以*效能為基礎*的大小調整, 或將 vm 大小設*為內部部署*, 而不考慮效能歷程記錄。
**效能歷程記錄** | 評估機器效能資料時所要考慮的持續時間。 只有當調整大小準則是以*效能為基礎*時, 才適用此屬性。
**百分位數使用率** | 要視為適當大小調整的效能取樣集百分位數值。 只有在調整大小是以*效能為基礎*時, 才適用此屬性。
**VM 系列** |     您可以指定考慮要正確調整大小的 VM 系列。 例如，如果您不打算將生產環境移轉至 Azure 中的 A 系列虛擬機器，則可以從清單或系列排除 A 系列，而正確調整大小只會在選取的系列中完成。
**緩和因數** | Azure Migrate Server 評估會考慮在評估期間的緩衝區 (緩和因數)。 此緩衝區適用於 VM 的機器使用量資料 (CPU、記憶體、磁碟和網路)。 緩和因數會考量各個問題，例如季節性使用量、簡短的效能歷程記錄，以及未來可能增加的使用量。<br/><br/> 以 10 核心的 VM 為例，如果使用率為 20%，一般即相當於一個 2 核心的 VM。 但如果緩和因數為 2.0 x，則結果為 4 核心的 VM。
**供應項目** | 您所註冊的 [Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 會據以預估成本。
**貨幣** | 帳單貨幣。
**折扣 (%)** | 針對 Azure 供應項目所能獲得的任何訂用帳戶特定折扣。<br/> 預設設定為 0%。
**VM 運作時間** | 如果 VM不會在 Azure 中全天候執行，您可以指定其執行的持續時間 (每月天數和每天時數)，成本預估會據此來計算。<br/> 預設值是每月 31 天和每天 24 小時。
**Azure Hybrid Benefit** | 指定您是否擁有軟體保證, 以及是否符合[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)的資格。 如果設定為 [是]，則會考慮對 Windows VM 使用非 Windows Azure 價格。 預設值為 [是]。

## <a name="how-are-assessments-calculated"></a>如何計算評量？

Azure Migrate Server 評估中的評量是使用針對內部部署伺服器所收集的中繼資料來計算。 評量計算會在三個階段完成:針對每部伺服器, 評量計算會從 Azure 適用性分析開始, 然後是調整大小, 最後是每月成本估計。 如果伺服器通過上一個階段, 則只會移至稍後的階段。 例如, 如果伺服器未通過 Azure 適用性檢查, 就會將其標示為不適合 Azure, 而且不會針對相同的進行大小調整和成本計算。

## <a name="azure-suitability-analysis"></a>Azure 適用性分析

並非所有機器都適合在 Azure 中執行。 Azure Migrate Server 評估會評估每個內部部署機器, 以適合 Azure 的遷移, 並將評定的機器分類為下列其中一個適用性類別:
- **可供 Azure 使用** - 機器不進行任何變更，即可依原樣移轉至 Azure。 它會在 Azure 中開機，並具有完整的 Azure 支援。
- **可有條件地供 Azure 使用** - 機器可在 Azure 中開機，但無法獲得完整的 Azure 支援。 例如，Azure 中不支援使用較舊版本 Windows Server 作業系統的機器。 將這些機器移轉至 Azure 之前需要特別注意，並請在移轉之前，遵循評量中建議的補救指導方針來修正整備程度問題。
- **無法供 Azure 使用** - 機器在 Azure 中將無法開機。 例如, 如果內部部署機器的磁片大小超過 64 TB, 就無法裝載于 Azure 上。 在移轉至 Azure 之前，您需要遵循評量中建議的補救指導方針來修正整備程度問題。 對於標示為未備妥供 Azure 使用的機器，未完成適當的大小調整和成本估計。
-  已過期-Azure Migrate 找不到電腦的就緒, 因為從內部部署環境收集的中繼資料不足。

Azure Migrate Server 評估會檢查機器屬性和客體作業系統, 以識別內部部署機器的 Azure 就緒程度。

### <a name="machine-properties"></a>機器屬性

伺服器評估會檢查內部部署 VM 的下列屬性, 以識別是否可以在 Azure 上執行。

**屬性** | **詳細資料** | **Azure 移轉整備程度狀態**
--- | --- | ---
**開機類型** | Azure 支援開機類型為 BIOS (而非 UEFI) 的 VM。 | 如果開機類型為 UEFI，便已有條件地就緒。
**核心** | 機器的核心數目必須等於或小於 Azure VM 支援的核心數目上限 (128 個核心)。<br/><br/> 如果有提供效能記錄，則 Azure Migrate 會將已使用的核心納入考量，進行比較。 如果評量設定已指定緩和因數，則會將使用的核心數目乘以緩和因數。<br/><br/> 如果沒有效能記錄，Azure Migrate 會使用配置的核心，而不套用緩和因數。 | 小於或等於限制便就緒。
**記憶體** | 機器的記憶體大小必須等於或小於 Azure VM 允許的記憶體上限 (Azure M 系列 Standard_M128m 上 3892 GB &nbsp;<sup>2</sup>)。 [深入了解](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果有提供效能記錄，Azure Migrate 會將已使用的記憶體納入考量，進行比較。 如果已指定緩和因數，則會將使用的記憶體乘以緩和因數。<br/><br/> 如果沒有記錄，則會使用配置的記憶體，而不套用緩和因數。<br/><br/> | 在限制內便就緒。
**存放磁碟** | 磁片的配置大小必須 32 TB 或更少。<br/><br/> 附加至機器的磁碟數目必須小於或等於 65，作業系統磁碟亦包含在內。 | 在限制內便就緒。
**網路功能** | 機器必須附加 32 個以內的網路介面卡。 | 在限制內便就緒。

### <a name="guest-operating-system"></a>客體作業系統
除了 VM 屬性, Azure Migrate Server 評估會查看機器的客體作業系統, 以識別是否可以在 Azure 上執行。

> [!NOTE]
> 針對 VMware Vm, Azure Migrate Server 評估會使用在 vCenter Server 中為 VM 指定的作業系統來執行虛擬作業系統分析。 對於在 VMware 上執行的 Linux Vm, 它目前不會識別虛擬作業系統的確切核心版本。

Azure Migrate Server 評估會使用下列邏輯, 以根據作業系統識別 Azure 的就緒程度。

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
其他作業系統<br/><br/> 例如, Oracle Solaris、Apple Mac OS 等、FreeBSD 等。 | Azure 並未認同這些作業系統。 機器可能可以在 Azure 中開機，但 Azure 不會提供作業系統支援。 | 可有條件地供 Azure 使用，建議在移轉到 Azure 之前安裝支援的作業系統。  
在 vCenter Server 中作業系統指定為**其他** | Azure Migrate 無法在此情況下識別作業系統。 | 整備程度未知。 請確保 Azure 支援 VM 內執行的作業系統。
32 位元作業系統 | 機器可能可以在 Azure 中開機，但 Azure 可能不會提供完整支援。 | 可有條件地供 Azure 使用，在移轉到 Azure 之前，請考慮將機器的作業系統從 32 位元作業系統升級至 64 位元作業系統。

## <a name="sizing"></a>調整大小

將機器標示為可供 Azure 使用之後, 伺服器評估會執行大小調整, 這牽涉到識別適用于內部部署 VM 的適當 Azure VM 和磁片 SKU。 大小建議會根據指定的評量屬性而有所不同。

- 如果評估使用以*效能為基礎的大小調整*, Azure Migrate 會考慮機器的效能歷程記錄, 以識別 Azure 中的 VM 大小和磁片類型。 如果您已過度配置內部部署 VM, 但使用率很低, 而您想要在 Azure 中適當調整 VM 的大小, 以節省成本, 此方法特別有用。 這個方法可協助您優化遷移期間的大小。
- 如果您不想要考慮 VM 大小的效能資料, 且想要將內部部署機器視為 Azure, 您可以將調整大小準則指定為內部*部署*, 然後伺服器評估會根據內部部署來調整 vm 的大小設定而不考慮使用量資料。 在此情況下, 磁片大小會根據您在評估內容中指定的儲存體類型 (標準 HDD 磁片、標準 SSD 磁片或高階磁片) 來完成。

### <a name="performance-based-sizing"></a>以效能為基礎調整大小

針對以效能為基礎的大小調整, Azure Migrate Server 評估會從連接至 VM 的磁片開始, 接著是網路介面卡, 然後根據內部部署 VM 的計算需求來對應 Azure VM SKU。 Azure Migrate 設備會分析內部部署環境, 以收集 CPU、記憶體、磁片和網路介面卡的效能資料。

**效能資料收集**

- 針對 VMware Vm, Azure Migrate 設備會收集每隔20秒的時間點, 針對 Hyper-v Vm, 即時取樣點會每隔30秒的間隔收集一次。
- 然後, 設備會匯總每10分鐘收集的樣本點, 並將過去10分鐘的最大值傳送至 Azure Migrate 伺服器評量。
- Azure Migrate Server 評估會儲存過去一個月的所有10分鐘範例點, 並根據針對*效能歷程記錄*和*百分位數使用率*所指定的評量屬性而定, 它會識別適當的資料點應該用於適當調整大小。 例如, 如果效能歷程記錄設定為一天, 而百分位數使用率為第95個百分位數, 則會在最後一天使用10分鐘取樣點, 以遞增順序排序, 並挑選第95個百分位數值來進行適當的調整大小。
- 上述值接著會乘以緩和因素, 以取得每個計量 (CPU 使用率、記憶體使用率、磁片 IOPS (讀取和寫入)、磁片輸送量 (讀取和寫入)、網路輸送量 (輸入和輸出) 的有效效能使用率資料,設備會收集。
- 一旦識別出有效的使用率值, 計算、儲存體和網路大小的完成方式如下所示:

**儲存體大小**:Azure Migrate 嘗試將連接至機器的每個磁碟對應至 Azure 中的磁碟。

> [!NOTE]
> Azure Migrate：伺服器評估僅支援受控磁片進行評估。

  - 系統會新增磁片的讀取和寫入 IOPS, 以取得所需的 IOPS 總數、類似讀取, 以及新增的寫入輸送量值, 以取得每個磁片的總輸送量
  - 如果您已將儲存體類型指定為自動, 根據有效的 IOPS 和輸送量值, Azure Migrate Server 評估會識別磁片是否應對應至 Azure 中的標準 HDD、標準 SSD 或 premium 磁片。 如果儲存體類型設定為標準 HDD/SSD/Premium, 它會嘗試在選取的儲存體類型 (標準 HDD/SSD/Premium 磁片) 中尋找磁片 SKU。
  - 如果伺服器評估找不到具有所需 IOPS & 輸送量的磁片, 它會將機器標示為不適合 Azure。
  - 如果找到一組適當的磁片, 它會選取支援評量設定中指定之位置的磁片。
  - 如果有多個符合資格的磁碟，則會選取成本最低的磁碟。
  - 如果任何磁片的效能資料無法使用, 磁片的設定資料 (磁片大小) 會用來在 Azure 中尋找標準 SSD 磁片。

**網路大小調整**:Azure Migrate Server 評估會嘗試尋找可支援附加至內部部署機器的網路介面卡數目, 以及這些網路介面卡所需效能的 Azure VM。
    - 為了取得內部部署 VM 的有效網路效能, 伺服器評量會匯總所有網路介面卡上每秒傳輸的資料 (MBps) (網路輸出), 並套用緩和因數。 會使用此數字來尋找可支援所要求的網路效能的 Azure VM。
    - 隨著網路效能，它也會考慮 Azure VM 是否可支援所需的網路介面卡數目。
    - 如果沒有可用的網路效能資料，調整虛擬機器大小時只會僅會考量網路介面卡的數量。

**計算大小**:計算儲存體和網路需求之後, Azure Migrate Server 評估會考慮 CPU 和記憶體需求, 以便在 Azure 中尋找適當的 VM 大小。
    - Azure Migrate 會查看有效運用的核心和記憶體, 以便在 Azure 中尋找適當的 VM 大小。
    - 如果找不到合適的大小，便會將機器標示為不適合 Azure。
    - 如果找到合適的大小，Azure Migrate 會套用儲存體與網路計算。 然後再套用位置及定價層設定，對虛擬機器大小提出最終建議。
    - 如果有多個符合資格的 Azure VM 大小，建議使用成本最低的那個。

### <a name="as-on-premises-sizing"></a>作為內部部署調整大小

如果您使用做為*內部部署*調整大小, 伺服器評估不會考慮 vm 和磁片的效能歷程記錄, 並且會根據內部部署所配置的大小來配置 Azure 中的 VM SKU。 同樣地, 針對磁片大小調整, 它會查看評估屬性 (標準 HDD/SSD/Premium) 中指定的儲存體類型, 並據此建議磁片類型。 預設儲存體類型為 [Premium 磁片]。

## <a name="confidence-ratings"></a>信賴評等
Azure Migrate 中的每個效能型評量會與信賴評等相關聯, 範圍從一個 (最低) 到五個開始 (最高)。
- 根據計算評量所需的資料點可用性，每個評量都會指派信賴評等。
- 評量的信賴評等可協助您評估 Azure Migrate 提供的大小建議之可靠性。
- 信賴評等不適用於作為內部部署評量。
- 針對以效能為基礎的大小調整, Azure Migrate Server 評估需求:
    - CPU 和 VM 記憶體的使用量資料。
    - 此外，對於連結至 VM 的每個磁碟，它都需要磁碟 IOPS 和輸送量資料。
    - 同樣地, 針對連接至 VM 的每個網路介面卡, 信賴評等需要網路 i/o 來執行以效能為基礎的大小調整。
    - 如果 vCenter Server 中未提供上述任何使用量號碼, 則大小建議可能不可靠。

根據可用資料點的百分比提供評量的信賴評等如下：

   **資料點的可用性** | **信賴評等**
   --- | ---
   0%-20% | 1 顆星
   21%-40% | 2 顆星
   41%-60% | 3 顆星
   61%-80% | 4 顆星
   81%-100% | 5 顆星

### <a name="low-confidence-ratings"></a>低信賴等級

評量可能會獲得低信賴度評等的幾個原因:

- 您在建立評量的持續時間內, 未針對您的環境進行分析。 例如, 如果您建立的評量將效能持續時間設定為一天, 則您必須在啟動探索之後至少等候一天, 以收集所有資料點。
- 在計算評估期間, 某些 Vm 已關閉。 如果有任何 Vm 在某段期間內電源關閉, Azure Migrate Server 評估無法收集該期間的效能資料。
- 有些 Vm 是在計算評估的期間內建立的。 例如, 如果您針對過去一個月的效能歷程記錄建立評量, 但有些 Vm 只是在一周前于環境中建立, 則新 Vm 的效能歷程記錄將不會在整個持續時間內。

> [!NOTE]
> 如果任何評量的信賴評等低於5顆星, 建議您至少等候一天讓設備分析環境, 然後重新計算評量。 如果您不這麼做, 以效能為基礎的大小調整可能不可靠, 而且我們建議您切換評量作為內部部署調整大小。

## <a name="monthly-cost-estimation"></a>每月成本預估

調整大小建議完成後, Azure Migrate 會計算遷移後的計算和儲存成本。

- **計算成本**：使用建議的 Azure VM 大小時，Azure Migrate 會使用計費 API 來計算虛擬機器的每月成本。
    - 計算時會將作業系統、軟體保證、保留執行個體、VM 運作時間、位置和貨幣設定納入考量。
    - 其會彙總所有機器的成本，以計算每月計算總成本。
- **儲存成本**：機器的每月儲存成本是藉由匯總所有連接到機器的磁片的每月成本計算而得。
    - Azure Migrate Server 評估會匯總所有機器的儲存成本, 以計算每月儲存成本總計。
    - 目前在計算時不會將評量設定中指定的供應項目列入考量。

成本會以評量設定中指定的貨幣顯示。


## <a name="next-steps"></a>後續步驟

建立[VMware vm](tutorial-assess-vmware.md)或[hyper-v vm](tutorial-assess-hyper-v.md)的評量。
