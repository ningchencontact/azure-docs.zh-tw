---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ee9b70827c19236287f81f66f4b9c6e1004a54fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114525"
---
下表說明 Azure 的一般用途 v1，v2 和 Blob 儲存體帳戶的預設限制。 *輸入*限制是指所有資料傳送至儲存體帳戶的要求。 *輸出*限制是指所有資料從儲存體帳戶中所收到的回應。

| 資源 | 預設限制 |
| --- | --- |
| 每個區域中每個訂用帳戶的儲存體帳戶數目，包括標準和進階帳戶 | 250 |
| 最大的儲存體帳戶容量 | 2 適用於美國和歐洲地區，其他所有區域，其中包括英國的 500 TB 的 PB |
| Blob 容器、 blob、 檔案共用、 資料表、 佇列、 實體或訊息，每個儲存體帳戶的最大數目 | 沒有限制 |
| 每一儲存體帳戶的要求率上限<sup>1</sup> | 每秒 20,000 個要求 |
| 最大輸入<sup>1</sup>每個儲存體帳戶 （美國地區） | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 20 Gbps<sup>2</sup> |
| 最大輸入<sup>1</sup>每個儲存體帳戶 （非美國地區） | 如果啟用 RA-GRS/GRS，則 5 Gbps，LRS/ZRS 為 10 Gbps<sup>2</sup> |
| 一般用途 v2 和 Blob 儲存體帳戶 （所有區域） 的最大輸出 | 50 Gbps |
| 針對一般用途 v1 儲存體帳戶 （美國地區） 的最大輸出 | 如果啟用 RA-GRS/GRS，則為 20 Gbps，LRS/ZRS 為 30 Gbps<sup>2</sup> |
| 針對一般用途 v1 儲存體帳戶 （非美國地區） 的最大輸出 | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 15 Gbps<sup>2</sup> |

<sup>1</sup>azure 標準儲存體帳戶支援的輸入要求，以較高的限制。 若要要求提高帳戶的輸入限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。

<sup>2</sup> [Azure 儲存體複寫](https://docs.microsoft.com/azure/storage/common/storage-redundancy)選項包括：
* **RA-GRS**：讀取權限異地備援儲存體。 如果已啟用 RA-GRS，次要位置的輸出目標會與主要位置的輸出目標完全相同。
* **GRS**：異地備援儲存體。 
* **ZRS**：區域備援儲存體。
* **LRS**：本地備援儲存體。 

> [!NOTE]
> 我們建議您在大部分情況下，使用一般用途 v2 儲存體帳戶。 一般用途 v2 帳戶產生任何停機時間，而且不需要複製資料，您可以輕鬆地升級一般用途 v1 或 Azure Blob 儲存體帳戶。
>
> 如需有關 Azure 儲存體帳戶的詳細資訊，請參閱 <<c0> [ 儲存體帳戶概觀](../articles/storage/common/storage-account-overview.md)。 

如果您應用程式的需求超過單一儲存體帳戶的延展性目標，您可以將應用程式建置為使用多個儲存體帳戶。 接著您可以在這些儲存體帳戶之間分割資料物件。 如需批量價格的詳細資訊，請參閱[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

所有儲存體帳戶都能在一般網路拓撲上執行，並支援本文中說明的延展性和效能目標，無論它們在何時建立。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) \(英文\)。

