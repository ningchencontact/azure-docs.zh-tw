---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2daafa6bf5f9a28ad2b61a97e7a8bd2246ae18d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2019
ms.locfileid: "65538372"
---
# <a name="what-disk-types-are-available-in-azure"></a>在 Azure 中可使用哪些磁碟類型？

Azure 受控的磁碟目前提供四種磁碟類型，其中三個已正式運作 (GA)，另一個目前處於預覽狀態。 這四個磁碟類型各有自己適用的目標客戶案例。

## <a name="disk-comparison"></a>磁碟比較

下表會比較適用於受控磁碟的 Ultra 固態硬碟 (SSD) (預覽)、進階 SSD、標準 SSD 和標準硬碟 (HDD)，以協助您決定要使用何者。

|   | Ultra SSD (預覽)   | 進階 SSD   | 標準 SSD   | 標準 HDD   |
|---------|---------|---------|---------|---------|
|磁碟類型   |SSD   |SSD   |SSD   |HDD   |
|案例   |IO 密集的工作負載 (例如 SAP HANA)、最上層資料庫 (例如 SQL、Oracle)，以及其他高交易量的工作負載。   |生產環境和重視效能的工作負載   |網頁伺服器，輕量使用的企業應用程式和開發/測試   |備份、不重要、存取不頻繁   |
|磁碟大小   |65,536 GiB (預覽)   |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大輸送量   |2,000 MiB/秒 (預覽)   |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|IOPS 上限   |160,000 (預覽)   |20,000   |6,000   |2,000   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (預覽)

Azure Ultra SSD (預覽) 可為 Azure IaaS VM 提供高輸送量、高 IOPS 以及一致的低延遲磁碟儲存體。 Ultra SSD 的其他一些優點包括能夠以動態方式變更磁碟的效能和您的工作負載，而不需要重新啟動虛擬機器。 Ultra SSD 適用於資料密集的工作負載 (例如 SAP HANA)、最上層資料庫，以及高交易量的工作負載。 Ultra SSD 只可用來作為資料磁碟。 建議您使用進階 SSD 作為作業系統磁碟。

### <a name="performance"></a>效能

當您佈建 Ultra 磁碟時，您可個別設定磁碟的容量和效能。 Ultra SSD 有 4 GiB 到 64 TiB 的幾種固定大小可供選擇，且具有彈性效能組態模型，可讓您個別設定 IOPS 和輸送量。

Ultra SSD 的主要功能包括：

- 磁碟容量：Ultra SSD 容量的範圍介於 4 GiB 到 64 TiB。
- 磁碟 IOPS：Ultra SSD 支援 300 IOPS/GiB 的 IOPS 限制，最多可達每個磁碟 160 K 的 IOPS。 若要達到您所佈建的 IOPS，請確定選取的磁碟 IOPS 小於 VM IOPS。 最小的磁碟 IOPS 為 100 IOPS。
- 磁碟輸送量：使用 Ultra SSD 時，對於每個佈建的 IOPS，單一磁碟的輸送量上限為 256 KiB/秒，最多可達每個磁碟 2000 MBps (其中，MBps = 10 ^6 位元組/秒)。 最小的磁碟輸送量為 1 MiB。
- 強力的 Ssd 支援調整磁碟效能屬性 （IOPS 和輸送量） 在執行階段不卸離磁碟與虛擬機器。 向磁碟發出磁碟效能調整作業後，最多可能需要一個小時，變更才會實際生效。

### <a name="disk-size"></a>磁碟大小

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

### <a name="transactions"></a>交易

強力的 Ssd，每個 I/O 作業小於或等於 256 KiB 的輸送量會被視為單一 I/O 作業。 大於 256 KiB 的輸送量的 I/O 作業會被視為多個 I/o 大小 256 KiB。

### <a name="preview-scope-and-limitations"></a>預覽版的範圍和限制

在預覽期間，Ultra SSD：

- 可在美國東部 2 的單一可用性區域中受到支援  
- 只能用於可用性區域 (區域以外的可用性設定組和單一 VM 部署將無法連結 Ultra磁碟)
- 僅在 ES/DS v3 VM 上受到支援
- 只能作為資料磁碟，且僅支援 4k 實體磁區大小  
- 只能建立為空磁碟  
- 目前只能使用 Azure Resource Manager 範本、CLI 和 Python SDK 進行部署。
- 尚不支援磁碟快照集、VM 映像、可用性設定組、虛擬機器擴展集和 Azure 磁碟加密。
- 尚不支援與 Azure 備份或 Azure Site Recovery 整合。
- 如同 [大部分的預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，這項功能在正式運作 (GA) 之前不應該用於生產工作負載。
