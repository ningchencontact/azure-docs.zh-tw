---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 16d05203e6c8e845b2354298880d132801e4b952
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882582"
---
Azure 儲存體提供數種類型的儲存體帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 您在建立儲存體帳戶之前請先考量這些差異，以判斷您的應用程式最適用的帳戶類型。 儲存體帳戶的類型如下：

- **一般用途 v2 帳戶**： blob、檔案、佇列和資料表的基本儲存體帳戶類型。 在大部分情況下，建議您使用 Azure 儲存體。
- **一般用途 v1 帳戶**： blob、檔案、佇列和資料表的舊版帳戶類型。 如果可能的話，請改為使用一般用途 v2 帳戶。
- **BlockBlobStorage 帳戶**：具有 premium 效能特性的僅限 Blob 儲存體帳戶。 建議適用于具有高交易率的案例、使用較小的物件，或需要一致的儲存延遲。
- **FileStorage 帳戶**：僅限檔案的儲存體帳戶，具有 premium 效能特性。 建議用於企業或高效能規模的應用程式。
- **BlobStorage 帳戶**：舊版僅限 Blob 的儲存體帳戶。 如果可能的話，請改為使用一般用途 v2 帳戶。

下表描述儲存體帳戶類型及其功能：

| 儲存體帳戶類型 | 支援的服務                       | 支援的效能層級      | 支援的存取層         | 複寫選項               | 部署模型<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 加密<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 一般用途 V2   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 經常性存取、非經常性存取、封存<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS、切換（預覽）、RA-切換（預覽）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | 加密              |
| 一般用途 V1   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS、GRS、RA-GRS                  | Resource Manager、傳統    | 加密              |
| BlockBlobStorage   | Blob (僅限區塊 Blob 和附加 Blob) | 高級                       | N/A                            | LRS                               | Resource Manager             | 加密              |
| FileStorage   | 僅限檔案 | 高級                       | N/A                            | LRS                               | Resource Manager             | 加密              |
| BlobStorage         | Blob (僅限區塊 Blob 和附加 Blob) | Standard                      | 經常性存取、非經常性存取、封存<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | Resource Manager             | 加密              |

<div id="deployment-model"><sup>1</sup>建議使用 Azure Resource Manager 部署模型。 使用傳統部署模型的儲存體帳戶仍可建立於某些位置，而且會繼續支援現有的傳統帳戶。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態</a>。</div>

<div id="encryption"><sup>2</sup>所有儲存體帳戶會使用待用資料的 Azure 儲存體服務加密 (SSE) 進行加密。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">待用資料的 Azure 儲存體服務加密</a>。</div>

<div id="archive"><sup>3</sup>封存儲存層適用於於個別 Blob 的等級，不適用於儲存體帳戶層級。 只可以封存區塊 Blob 和附加 Blob。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob 儲存體︰經常性存取、非經常性存取和封存儲存層</a>。</div>

<div id="zone-redundant-storage"><sup>4</sup>區域冗余儲存體（ZRS）和地理區域冗余儲存體（切換）（預覽）僅適用于標準一般用途 v2 儲存體帳戶。 如需 ZRS 的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式</a>。 如需切換的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">高可用性和最大持久性（預覽）的異地區域冗余儲存體</a>。 如需其他複寫選項的詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure 儲存體複寫</a>。</div>

<div id="premium-performance"><sup>5</sup>一般用途 v2 和一般用途 v1 帳戶的 Premium 效能僅適用于磁片和分頁 blob。</div>
