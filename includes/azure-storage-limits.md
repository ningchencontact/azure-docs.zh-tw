---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b51d984726cfd178189e7fe1a994c35c7efaed36
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71124063"
---
下表說明 Azure 一般用途 v1、v2 和 Blob 儲存體帳戶的預設限制。 輸入限制是指傳送至儲存體帳戶之要求中的所有資料。 輸出限制是指從儲存體帳戶接收的回應中的所有資料。

| Resource | 預設限制 |
| --- | --- |
| 每個區域中每個訂用帳戶的儲存體帳戶數目，包括標準和進階帳戶 | 250 |
| 儲存體帳戶容量上限 | 2 PiB 適用于美國和歐洲，而 500 TiB 適用于所有其他區域（包含英國）<sup>1</sup>|
| 每個儲存體帳戶的 blob 容器、blob、檔案共用、資料表、佇列、實體或訊息數目上限 | 沒有限制 |
| 每一儲存體帳戶的要求率上限<sup>1</sup> | 每秒 20,000 個要求 |
| 每一儲存體帳戶的輸入上限<sup>1</sup> (美國、歐洲地區) | 25 Gbps |
| 每一儲存體帳戶的輸入上限<sup>1</sup> (美國和歐洲以外的地區) | 如果啟用了 RA-GRS/GRS, 則為 5 gbps, LRS/ZRS 為 10 Gbps<sup>2</sup> |
| 一般用途 v2 和 Blob 儲存體帳戶的輸出上限 (所有區域) | 50 Gbps |
| 一般用途 v1 儲存體帳戶的輸出上限 (美國地區) | 如果啟用了 RA-GRS/GRS, 則為 20 gbps, LRS/ZRS 為 30 Gbps<sup>2</sup> |
| 一般用途 v1 儲存體帳戶的輸出上限 (非美國地區) | 如果啟用了 RA-GRS/GRS, 則為 10 gbps, LRS/ZRS 為 15 Gbps<sup>2</sup> |

<sup>1</sup>Azure 標準儲存體帳戶支援更高的容量限制, 以及依要求輸入的更高限制。 若要要求提高帳戶的輸入限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。 如需詳細資訊, 請參閱[宣佈較大型、較大規模的儲存體帳戶](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)。

<sup>2</sup>如果您已啟用讀取權限（RA-GRS/RA-切換），次要位置的輸出目標會與主要位置相同。 [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy)複寫選項包括：[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

> [!NOTE]
> 我們建議您在大部分情況下使用一般用途 v2 儲存體帳戶。 您可以輕鬆地將一般用途 v1 或 Azure Blob 儲存體帳戶升級至一般用途 v2 帳戶, 而不需停機, 也不需要複製資料。
>
> 如需 Azure 儲存體帳戶的詳細資訊, 請參閱[儲存體帳戶總覽](../articles/storage/common/storage-account-overview.md)。

如果您應用程式的需求超過單一儲存體帳戶的延展性目標，您可以將應用程式建置為使用多個儲存體帳戶。 接著您可以在這些儲存體帳戶之間分割資料物件。 如需有關大量價格的資訊, 請參閱[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

所有儲存體帳戶都能在一般網路拓撲上執行，並支援本文中說明的延展性和效能目標，無論它們在何時建立。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) \(英文\)。

