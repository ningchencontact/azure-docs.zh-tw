---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226607"
---
下表列出 Linux 一般化 (gen.) 和特殊化 (spec.) 作業系統映像可能的上傳和擷取組合。 將正確處理沒有任何錯誤的組合以 Y 表示，而將擲回錯誤的組合會以 N 表示。下表說明遇到不同錯誤的原因和解決辦法。

| 作業系統 | 上傳特殊化 | 上傳一般化 | 擷取特殊化 | 擷取一般化 |
| --- | --- | --- | --- | --- |
| Windows 一般化 |N<sup>1</sup> |Y |N<sup>3</sup> |Y |
| Windows 特殊化 |Y |N<sup>2</sup> |Y |N<sup>4</sup> |

