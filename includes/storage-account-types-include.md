---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/02/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b278cce8f885f31f9d59fd389261812e04e58405
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2019
ms.locfileid: "54066985"
---
Azure 儲存體提供三種儲存體帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 您在建立儲存體帳戶之前請先考量這些差異，以判斷您的應用程式最適用的帳戶類型。 儲存體帳戶的三種類型如下：

* **一般用途 v2 帳戶**：適用於 Blob、文件，佇列和資料表的基本儲存體帳戶類型。 在大部分情況下，建議您使用 Azure 儲存體。
* **一般用途 v1 帳戶**：適用於 Blob、文件，佇列和資料表的傳統帳戶類型。 如果可能的話，請改為使用一般用途 v2 帳戶。
* **Blob 儲存體帳戶**：僅限 Blob 的儲存體帳戶。 如果可能的話，請改為使用一般用途 v2 帳戶。 

下表描述儲存體帳戶類型及其功能：

| 儲存體帳戶類型 | 支援的服務                       | 支援的效能層級 | 支援的存取層               | 複寫選項                                                | 部署模型<sup>1</sup>  | 加密<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| 一般用途 V2   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階           | 經常性存取層、非經常性存取層、封存存取層<sup>3</sup> | LRS、ZRS<sup>4</sup>、GRS、RA-GRS | Resource Manager | 已加密  |
| 一般用途 V1   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階           | N/A                                  | LRS、GRS、RA-GRS                                                   | Resource Manager、傳統  | 已加密  |
| Blob 儲存體         | Blob (僅限區塊 Blob 和附加 Blob) | 標準                    | 經常性存取層、非經常性存取層、封存存取層<sup>3</sup>                            | LRS、GRS、RA-GRS                                                   | Resource Manager  | 已加密  |

<sup>1</sup>建議使用 Azure Resource Manager 部署模型。 使用傳統部署模型的儲存體帳戶仍可建立於某些位置，而且會繼續支援現有的傳統帳戶。 如需詳細資訊，請參閱 [Azure Resource Manager 與傳統部署：了解資源的部署模型和狀態](../articles/azure-resource-manager/resource-manager-deployment-model.md)。

<sup>2</sup>所有儲存體帳戶會使用待用資料的 Azure 儲存體服務加密 (SSE) 進行加密。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](../articles/storage/common/storage-service-encryption.md)。

<sup>3</sup>封存儲存層適用於於個別 Blob 的等級，不適用於儲存體帳戶層級。 只可以封存區塊 Blob 和附加 Blob。 如需詳細資訊，請參閱 [Azure Blob 儲存體：經常性存取、非經常性存取和封存儲存層](../articles/storage/blobs/storage-blob-storage-tiers.md)。

<sup>4</sup>區域備援儲存體 (ZRS) 僅適用於標準的一般用途 v2 儲存體帳戶。 如需 ZRS 的相關詳細資訊，請參閱[區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式](../articles/storage/common/storage-redundancy-zrs.md)。 如需其他複寫選項的詳細資訊，請參閱 [Azure 儲存體複寫](../articles/storage/common/storage-redundancy.md)。
