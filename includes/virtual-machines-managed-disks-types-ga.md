---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 885bc1c627626ee7ba4f391be31131b18fa1ab39
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65211933"
---
## <a name="premium-ssd"></a>進階 SSD

針對輸入/輸出 (IO) 工作負載大的虛擬機器 (VM)，Azure 進階 SSD 可提供高效能和低延遲的磁碟支援。 您可以將現有 VM 磁碟遷移到進階 SSD，以利用進階儲存體磁碟的速度和效能。 進階 SSD 適用於任務關鍵性的生產應用程式。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P50 磁碟，Azure 會為該磁碟佈建 4,095 GB 儲存體容量、7,500 IOPS 和 250 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。 進階 SSD 磁碟的設計訴求是要提供 99.9% 的時間達到目標效能。

## <a name="standard-ssd"></a>標準 SSD

Azure 標準 SSD 是符合成本效益的儲存體選項，已針對在較低 IOPS 層級上需要一致效能的工作負載進行最佳化。 「標準 SSD」為想要移至雲端的使用者，尤其是在於內部部署 HDD 解決方案上執行之工作負載的變異方面遇到問題的使用者，提供一個良好的入門級體驗。 與 HDD 磁碟相比，「標準 SSD」提供更佳的可用性、一致性、可靠性及延遲。 標準 SSD 適用於網頁伺服器、低 IOPS 應用程式伺服器、不常使用的企業應用程式及開發/測試工作負載。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

對於大部分的 IO 作業，提供單一數字毫秒延遲，並以傳遞的 IOPS 和輸送量數目達到上述表格 99%的時間中所述的限制，被設計標準的 Ssd。 實際的 IOPS 和輸送量會有所不同有時流量模式。 與 HDD 磁碟相比，「標準 SSD」將可提供延遲更低的更一致效能。

## <a name="standard-hdd"></a>標準 HDD

如果 VM 執行的工作負載不在乎延遲，Azure 標準 HDD 可以提供可靠、低成本的磁碟支援。 它也支援 blob、資料表、佇列和檔案。 使用標準儲存體時，資料會儲存在硬碟 (HDD)。 使用 VM 時，您可以對開發/測試案例和較不重要的工作負載使用標準 SSD 和 HDD 磁碟。 所有 Azure 區域中都可以使用標準儲存體。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>計費

使用受控磁碟時，需考量下列計費資訊：

- 磁碟類型
- 受控磁碟大小
- 快照集
- 輸出資料傳輸
- 交易數目

**受控磁碟大小**：受控磁碟會依佈建大小計費。 Azure 會將佈建大小對應 (無條件進位) 至供應項目中最接近的磁碟大小。 若要深入了解提供的磁碟大小，請參閱之前的資料表。 每一個磁碟會對應至供應項目中支援的佈建磁碟大小，並據此計費。 例如，如果您佈建了 200 GiB 的「標準 SSD」，它將會與 E15 (256 GiB) 的磁碟大小供應項目對應。 任何已佈建的磁碟都是使用每月的進階儲存體供應項目價格，以每小時的方式計費。 例如，如果您佈建了 E10 磁碟並在 20 小時後刪除它，便會按 20 小時的比例向您收取 E10 供應項目的費用。 這與寫入磁碟的實際資料量無關。

**快照集**：快照集會根據使用的大小來計費。 例如，如果建立佈建容量為 64 GiB 的受控磁碟快照集，而實際使用資料大小為 10 GiB，則只會對已使用的 10 GiB 資料大小收取快照集費用。