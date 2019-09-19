---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "67174191"
---
| Resource | 目標        |
|----------|---------------|
| 單一 blob 容器的大小上限 | 與儲存體帳戶容量上限相同 |
| 區塊 blob 或附加 blob 中的區塊數目上限 | 50,000 個區塊 |
| 區塊 blob 中的區塊大小上限 | 100 MiB |
| 區塊 blob 的大小上限 | 50000 X 100 MiB （約 4.75 TiB） |
| 附加 blob 中的區塊大小上限 | 4 MiB |
| 附加 blob 的大小上限 | 50000 x 4 MiB （約 195 GiB） |
| 分頁 blob 的大小上限 | 8 TiB |
| 每個 blob 容器的預存存取原則數目上限 | 5 |
|單一 Blob 的目標輸送量 |最高可達儲存體帳戶的輸入/輸出限制<sup>1</sup> |

<sup>1</sup>單一物件輸送量取決於數個因素，包括但不限於平行存取、要求大小、效能層級、上傳來源的速度，以及下載的目的地。 若要利用[高輸送量區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)效能增強功能，請使用 > 4 MiB 的 put Blob 或 put 區塊要求大小（適用于高階效能區塊 blob 儲存體的 > 256 KiB 或適用于 Data Lake Storage Gen2）。
