---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8d1f9ca5a964787f0ee68e6d99fe870cd7b5994b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673264"
---
Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 Blob 儲存體已針對儲存大量非結構化資料最佳化。 非結構化資料是指不遵守特定資料模型或定義的資料，例如文字或二進位資料。

## <a name="about-blob-storage"></a>關於 Blob 儲存體

Blob 儲存體設計用來：

* 直接提供映像或文件給瀏覽器。
* 儲存檔案供分散式存取。
* 串流傳輸視訊和音訊。
* 寫入記錄檔。
* 儲存備份和還原、災害復原和封存資料。
* 儲存資料供內部部署或 Azure 託管服務進行分析。

使用者或用戶端應用程式可以從世界各地透過 HTTP/HTTPS 存取 Blob 儲存體中的物件。 Blob 儲存體中的物件可透過 [Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 儲存體用戶端程式庫存取。 用戶端程式庫提供多種語言，包括 [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)、[Node.js](https://azure.github.io/azure-storage-node)、[Python](https://docs.microsoft.com/python/azure/)、[Go](https://github.com/azure/azure-storage-blob-go/)、[PHP](https://azure.github.io/azure-storage-php/) 和 [Ruby](https://azure.github.io/azure-storage-ruby)。

## <a name="about-azure-data-lake-storage-gen2"></a>有關 Azure Data Lake Storage Gen2

Blob 儲存體支援 Azure Data Lake Storage Gen2，這是適用於雲端的 Microsoft 企業巨量資料分析解決方案。 Azure Data Lake Storage Gen2 提供階層式檔案系統和 Blob 儲存體的各項優點，包括低成本、分層式儲存體、高可用性、強式一致性，以及災害復原功能。

如需 Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../articles/storage/data-lake-storage/introduction.md)。
