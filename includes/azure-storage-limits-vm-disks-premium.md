---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 5ac7982d306125804fc5b7873e537f9381f717cb
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279507"
---
**進階非受控虛擬機器磁碟：每一帳戶限制**

| 資源 | 預設限制 |
| --- | --- |
| 每一帳戶的磁碟容量總計 |35 TB |
| 每一帳戶的快照集容量總計 |10 TB |
| 每一帳戶的最大頻寬 (輸入 + 輸出<sup>1</sup>) |<=50 Gbps |

<sup>1</sup>「輸入」是指傳送至某個儲存體帳戶的所有資料 (要求)。 *輸出* 是指從某個儲存體帳戶接收的所有資料 (回應)。

**進階非受控虛擬機器磁碟：每一磁碟限制**

| 進階儲存體磁碟類型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁碟大小 |128 GB |512 GB |1024 GB (1 TB) |2048 GiB (2 TB)|4095 GiB (4 TB)|
| 每一磁碟的 IOPS 上限 |500 |2300 |5000 |7500 |7500 |
| 每一磁碟的輸送量上限 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每個儲存體帳戶的磁碟數量上限 |280 |70 |35 | 17 | 8 |

**進階非受控虛擬機器磁碟：每個 VM 限制**

| 資源 | 預設限制 |
| --- | --- |
| 每個 VM 的最大 IOPS |80,000 IOPS (使用 GS5 虛擬機器) |
| 每一 VM 輸送量上限 |2,000 MB/秒 (使用 GS5 虛擬機器) |

