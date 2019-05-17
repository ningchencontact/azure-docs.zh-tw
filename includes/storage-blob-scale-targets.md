---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787628"
---
| 資源 | 目標        |
|----------|---------------|
| 单个 Blob 容器的最大大小 | 与最大存储帐户容量相同 |
| 块 Blob 或附加 Blob 中的块数上限 | 50,000 個區塊 |
| 块 Blob 中块的最大大小 | 100 MiB |
| 块 Blob 的最大大小 | 50,000 X 100 MiB（大约 4.75 TiB） |
| 附加 Blob 中块的最大大小 | 4 MiB |
| 附加 Blob 的最大大小 | 50,000 x 4 MiB（大约 195 GiB） |
| 页 Blob 的最大大小 | 8 TiB |
| 每个 Blob 容器存储的访问策略的最大数目 | 5 |
|單一 Blob 的目標輸送量 |儲存體帳戶輸入/輸出限制到<sup>1</sup> |

<sup>1</sup>單一物件輸送量取決於許多因素，包括但不是限於： 並行存取、 要求大小、 效能層級、 上傳來源和目的地，以下載的速度。 若要善用[高輸送量的區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)效能增強功能，使用 Put Blob 或 Put Block 要求的大小 > 4 MiB (> 256 KiB premium 效能區塊 blob 儲存體或 Data Lake 儲存體 Gen2)。
