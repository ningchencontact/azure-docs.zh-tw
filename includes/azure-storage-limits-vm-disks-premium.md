---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173968"
---
**進階非受控的虛擬機器磁碟：每個帳戶限制**

| 資源 | 預設限制 |
| --- | --- |
| 每一帳戶的磁碟容量總計 |35 TB |
| 每一帳戶的快照集容量總計 |10 TB |
| 每個帳戶 （輸入 + 輸出） 的最大頻寬<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*輸入*是指傳送至儲存體帳戶的要求中的所有資料。 *輸出*是指從儲存體帳戶中所收到的回應的所有資料。

**進階非受控的虛擬機器磁碟：每個磁碟限制**

| 進階儲存體磁碟類型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁碟大小 |128 GB |512 GB |1,024 giB (1 TB) |2,048 giB (2 TB)|4095 giB (4 TB)|
| 每個磁碟的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每個磁碟的最大輸送量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每個儲存體帳戶的磁碟數目上限 |280 |70 |35 | 17 | 8 |

**進階非受控的虛擬機器磁碟：每個 VM 限制**

| 資源 | 預設限制 |
| --- | --- |
| 每個 VM 的最大 IOPS |80,000 IOPS (使用 GS5 虛擬機器) |
| 每個 VM 的最大輸送量 |2,000 MB/秒，使用 gs5 虛擬機器 |

