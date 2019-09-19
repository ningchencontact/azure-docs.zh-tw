---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "67173968"
---
**Premium 非受控虛擬機器磁片：每個帳戶的限制**

| Resource | 預設限制 |
| --- | --- |
| 每一帳戶的磁碟容量總計 |35 TB |
| 每一帳戶的快照集容量總計 |10 TB |
| 每個帳戶的最大頻寬（輸入 + 輸出）<sup>1</sup> |<=50 Gbps |

<sup>1</sup>「輸入」*是指傳送*至儲存體帳戶之要求中的所有資料。 輸出*是指從*儲存體帳戶接收的回應中的所有資料。

**Premium 非受控虛擬機器磁片：每一磁片限制**

| 進階儲存體磁碟類型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁碟大小 |128 GB |512 GB |1024 GiB （1 TB） |2048 GiB （2 TB）|4095 GiB （4 TB）|
| 每一磁片的 IOPS 上限 |500 |2,300 |5,000 |7,500 |7,500 |
| 每一磁片的最大輸送量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每個儲存體帳戶的磁片數目上限 |280 |70 |35 | 17 | 8 |

**Premium 非受控虛擬機器磁片：每個 VM 的限制**

| Resource | 預設限制 |
| --- | --- |
| 每個 VM 的最大 IOPS |80,000 IOPS (使用 GS5 虛擬機器) |
| 每個 VM 的最大輸送量 |2000 MB/秒（使用 GS5 VM） |

