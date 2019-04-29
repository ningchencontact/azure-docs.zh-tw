---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564337"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>強力的磁碟 （預覽） 受控磁碟的 Azure 虛擬機器工作負載

Azure 的強力磁碟 （預覽） 提供高輸送量、 高 IOPS，以及一致的低延遲磁碟儲存體為 Azure IaaS Vm。 這個新的供應項目可提供絕佳的效能，同時保有我們現有磁碟供應項目的相同可用性層級。 強力的磁碟的其他優點包括能夠以動態方式變更以及您的工作負載，而不需要重新啟動您的虛擬機器磁碟的效能。 強力磁碟適用於資料密集的工作負載，例如 SAP HANA、 最上層資料庫和交易為主的工作負載。

## <a name="ultra-disk-features"></a>強力磁碟功能

受控磁碟：強力的磁碟只可用為受控磁碟。 強力的磁碟無法部署為非受控的磁碟或分頁 blob。 在建立受控的磁碟時，您可以指定磁碟 sku UltraSSD_LRS 為輸入，並指出磁碟 IOPS、 大小和輸送量，您需要與 Azure 建立和管理為您的磁碟。  

**虛擬機器**：強力的磁碟被設計用於搭配所有進階 SSD 啟用的 Azure 虛擬機器 Sku;不過，因為它目前為預覽狀態，Vm 的大小調整為 ES/DS v3 中。

**動態效能組態**：強力的磁碟可讓您動態變更效能 （IOPS 和輸送量），以及您的工作負載需求的磁碟，而不必重新啟動您的虛擬機器。

## <a name="scalability-and-performance-targets"></a>擴充和效能目標

當您佈建強力的磁碟時，您必須個別設定容量和磁碟的效能選項。 強力的磁碟來自數個固定的大小最多 64 TiB 的 4 GiB，並有彈性的效能組態模型，可讓您個別設定 IOPS 和輸送量的功能。 強力的磁碟只可以作為資料磁碟。 建議您以進階 SSD 作為作業系統磁碟。

強力的磁碟的一些主要功能有：

- 磁碟容量：強力的磁碟提供您許多不同的磁碟大小從最多 64 TiB 的 4 GiB。
- 磁碟 IOPS：強力的磁碟支援 300 IOPS/GiB，最多 160 K IOPS 的每個磁碟的 IOPS 的限制。 若要達到您所佈建的 IOPS，請確定選取的磁碟 IOPS 小於 VM IOPS。 最小的磁碟 IOPS 為 100 IOPS。
- 磁碟輸送量：強力的磁碟，單一磁碟的輸送量限制為 256 KiB/秒的每個佈建最多 2000 MBps，每個磁碟的 IOPS，(其中 MBps = 10 ^6 位元組 / 秒)。 最小的磁碟輸送量為 1 MiB。

下表概述不同磁碟大小支援的不同設定：  

### <a name="ultra-disks-managed-disk-offerings"></a>強力磁碟受控磁碟供應項目

|磁碟大小 (GiB)  |IOPS 上限  |輸送量上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024-65,536 (此範圍中的大小會以 1 TiB 為單位遞增)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>價格和計費

使用強力的磁碟時，將會套用下列的計費考量：

- 受控磁碟大小
- 受控磁碟佈建的 IOPS
- 受控磁碟佈建的輸送量
- 強力的磁碟的 VM 保留費用

### <a name="managed-disk-size"></a>受控磁碟大小

受控磁碟會依據您佈建新 Azure VM 時所選擇的 VM 大小計費。 Azure 會將佈建大小對應 (無條件進位) 至最接近的磁碟大小供應項目。 如需所提供磁碟大小的詳細資料，請參閱上述＜延展性和效能目標＞一節中的表格。 每個磁碟皆與一個支援的佈建磁碟大小對應，並據此依小時計費。 比方說，如果您佈建為 200 的 GiB 強力的磁碟，並刪除 20 小時後，它會對應至 256 GiB 的磁碟大小供應項目，您將支付 256 GiB 的 20 個小時。 不論實際寫入至磁碟的資料量為何，此計費是以計算時數耗用量為基礎。

### <a name="managed-disk-provisioned-iops"></a>受控的磁碟佈建的 IOPS

IOPS 是指您的應用程式在美秒內傳送到磁碟的要求數。 輸入/輸出作業可以是循序讀取或寫入或隨機讀取或寫入作業。 根據 VM 所連結磁碟的磁碟大小或磁碟數目，IOPS 的平均數目是依照每小時計費。 如需所提供 IOPS 的詳細資訊，請參閱上述「延展性和效能目標」一節中的表格。

### <a name="managed-disk-provisioned-throughput"></a>受控磁碟佈建的輸送量

輸送量是指應用程式在指定的間隔內傳送到磁碟的資料量，以位元組/秒為計算單位。 如果您的應用程式執行較大的輸入/輸出作業，則需要較高輸送量。  

輸送量和 IOPS 之間存在某種關聯，如下列公式所示：IOPS x IO 大小 = 輸送量

因此，務必判斷應用程式所需的最佳輸送量和 IOPS 值。 嘗試最佳化其中一個時，另一個也會受影響。 建議一開始先使用對應於 16 KiB IO 大小的輸送量，等到需要更多輸送量時再進行調整。

如需強力的磁碟上的受支援的磁碟輸送量的詳細資訊，請參閱延展性和效能目標上一節中的資料表。 如同磁碟大小和 IOPS，佈建的輸送量也會根據佈建的 MBps 依小時計費。

### <a name="ultra-disk-vm-reservation-fee"></a>強力的磁碟的 VM 保留費用

我們引進了一項功能表示您的 VM 是相容的強力磁碟的 VM 上。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當強力的磁碟附加至強力的相容 VM 時，就不會套用這筆費用。 此費用會根據在 VM 上佈建的 vCPU 計費。

請參閱[定價頁面的 Azure 磁碟](https://azure.microsoft.com/pricing/details/managed-disks/)新強力磁碟價格詳細資料以有限預覽形式提供。

### <a name="ultra-disk-preview-scope-and-limitations"></a>強力磁碟預覽範圍和限制

在預覽期間，強力的磁碟：

- 最初可在美國東部 2 的單一可用性區域中受到支援  
- 只能搭配可用性區域 （可用性設定組並不能將強力的磁碟連接的單一 VM 部署區域將外部）
- 僅在 ES/DS v3 VM 上受到支援
- 只能作為資料磁碟，且僅支援 4k 實體磁區大小  
- 只能建立為空磁碟  
- 目前只能使用 Resource Manager 範本、CLI 和 Python SDK 進行部署。
- 尚不支援磁碟快照集、VM 映像、可用性設定組、虛擬機器擴展集和 Azure 磁碟加密。
- 尚不支援與 Azure 備份或 Azure Site Recovery 整合。
- 如同 [大部分的預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，這項功能在正式運作 (GA) 之前不應該用於生產工作負載。
