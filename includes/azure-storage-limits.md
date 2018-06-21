---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852053"
---
| 資源 | 預設限制 |
| --- | --- |
| 每個區域中每個訂用帳戶的儲存體帳戶數目 | 200<sup>1</sup> |
| 儲存體帳戶容量上限 | 500 TiB<sup>2</sup> |
| 每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限 | 沒有限制 |
| 每一儲存體帳戶的要求率上限 | 每秒 20,000 個要求<sup>2</sup> |
| 每一儲存體帳戶的輸入上限<sup>3</sup> (美國地區) | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 20 Gbps<sup>4</sup> |
| 每一儲存體帳戶的輸出上限<sup>3</sup> (美國地區) | 如果啟用 RA-GRS/GRS，則為 20 Gbps，LRS/ZRS 為 30 Gbps<sup>4</sup> |
| 每一儲存體帳戶的輸入上限<sup>3</sup> (非美國地區) | 如果啟用 RA-GRS/GRS，則為 5 Gbps，LRS/ZRS 為 10 Gbps<sup>4</sup> |
| 每一儲存體帳戶的輸出上限<sup>3</sup> (非美國地區) | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 15 Gbps<sup>4</sup> |

<sup>1</sup>同時包括標準和進階儲存體帳戶。 如果您在指定區域需要超過 200 個儲存體帳戶，請透過 [Azure 支援](https://azure.microsoft.com/support/faq/)提出要求。 Azure 儲存體小組將會檢閱您的商務案例，而且可以針對指定區域核准多達 250 個儲存體帳戶。 

<sup>2</sup> 若需要儲存體帳戶的延伸限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。 Azure 儲存體團隊會檢閱要求，並可能依照個案核准較高的限制。 一般用途與 Blob 儲存體帳戶都可依照要求支援更高的容量、輸入/輸出和要求率。 如需 Blob 儲存體帳戶的最新最大值，請參閱[宣佈較大型、較大規模的儲存體帳戶](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

<sup>3</sup>只受限於帳戶的輸入/輸出限制。 *輸入*是指傳送至某個儲存體帳戶的所有資料 (要求)。 *輸出* 是指從某個儲存體帳戶接收的所有資料 (回應)。  

<sup>4</sup>Azure 儲存體備援選項包括：
* **RA-GRS**：讀取權限異地備援儲存體。 如果已啟用 RA-GRS，次要位置的輸出目標會與主要位置的輸出目標完全相同。
* **GRS**：異地備援儲存體。 
* **ZRS**：區域備援儲存體。
* **LRS**：本地備援儲存體。 
