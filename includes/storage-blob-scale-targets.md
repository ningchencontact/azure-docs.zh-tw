---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114777"
---
| 資源 | 目標        |
|----------|---------------|
| 單一 blob 容器的大小上限 | 儲存體帳戶容量上限相同 |
| 最大數目的區塊中的區塊 blob 或附加 blob | 50,000 個區塊 |
| 中的區塊 blob 的區塊大小上限 | 100 MiB |
| 區塊 blob 的大小上限 | 50,000 x 100 MiB (約為 4.75 TiB) |
| 附加 blob 中的區塊大小上限 | 4 MiB |
| 附加 blob 的大小上限 | 50,000 x 4 MiB (約為 195 GiB) |
| 分頁 blob 的大小上限 | 8 TiB |
| 每個 blob 容器的預存的存取原則的最大數目 | 5 |
|單一 Blob 的目標輸送量 |儲存體帳戶輸入/輸出限制到<sup>1</sup> |

<sup>1</sup>單一物件輸送量取決於許多因素，包括但不是限於： 並行存取、 要求大小、 效能層級、 上傳來源和目的地，以下載的速度。 若要善用[高輸送量的區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)效能增強功能，使用 Put Blob 或 Put Block 要求的大小 > 4 MiB (> 256 KiB premium 效能區塊 blob 儲存體或 Data Lake 儲存體 Gen2)。
