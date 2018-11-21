---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/23/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84333b26ac70db4b400f7236d4255f4b57a6ca7d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571914"
---
下表說明 Azure 儲存體的預設限制。 *輸入*限制是指傳送至某個儲存體帳戶的所有資料 (要求)。 *輸出*限制是指從某個儲存體帳戶接收的所有資料 (回應)。

| 資源 | 預設限制 |
| --- | --- |
| 每個區域中每個訂用帳戶的儲存體帳戶數目，包括標準和進階帳戶 | 250 |
| 儲存體帳戶容量上限 | 美國和歐洲為 2 PB，其他所有區域 (包括英國) 為 500 TB |
| 每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限 | 沒有限制 |
| 每一儲存體帳戶的要求率上限<sup>1</sup> | 每秒 20,000 個要求 |
| 每一儲存體帳戶的輸入上限<sup>1</sup> (美國地區) | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 20 Gbps<sup>2</sup> |
| 每一儲存體帳戶的輸入上限<sup>1</sup> (非美國區域) | 如果啟用 RA-GRS/GRS，則為 5 Gbps，LRS/ZRS 為 10 Gbps<sup>2</sup> |
| 一般用途 v2 和 Blob 儲存體帳戶的輸出上限 (所有區域) | 50 Gbps |
| 一般用途 v1 儲存體帳戶的輸出上限 (美國區域) | 如果啟用 RA-GRS/GRS，則為 20 Gbps，LRS/ZRS 為 30 Gbps <sup>2</sup> |
| 一般用途 v1 儲存體帳戶的輸出上限 (非美國區域) | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 15 Gbps <sup>2</sup> |

<sup>1</sup> Azure 儲存體帳戶會依照要求支援較高的輸入限制。 若要要求提高帳戶的輸入限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。

<sup>2</sup>[Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy)選項包括：
* **RA-GRS**：讀取權限異地備援儲存體。 如果已啟用 RA-GRS，次要位置的輸出目標會與主要位置的輸出目標完全相同。
* **GRS**：異地備援儲存體。 
* **ZRS**：區域備援儲存體。
* **LRS**：本地備援儲存體。 

如果您應用程式的需求超過單一儲存體帳戶的延展性目標，您可以將應用程式建置為使用多個儲存體帳戶。 接著您可以在這些儲存體帳戶之間分割資料物件。 如需批量價格的詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/) 。

所有儲存體帳戶都能在一般網路拓撲上執行，並支援本文中說明的延展性和效能目標，無論它們在何時建立。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。

