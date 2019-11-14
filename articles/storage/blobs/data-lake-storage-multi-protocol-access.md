---
title: Azure Data Lake Storage 上的多重通訊協定存取 |Microsoft Docs
description: 使用 Blob Api 和搭配 Azure Data Lake Storage Gen2 使用 Blob Api 的應用程式。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3470e27f64a672db0165b0132996db91a1447c08
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033972"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Azure Data Lake Storage 上的多重通訊協定存取

Blob Api 現在可與具有階層式命名空間的帳戶搭配使用。 這會解除鎖定工具、應用程式和服務的生態系統，以及具有階層式命名空間之帳戶的數個 Blob 儲存體功能。

到目前為止，您可能必須為物件儲存體和分析儲存體維護個別的儲存體解決方案。 這是因為 Azure Data Lake Storage Gen2 有限的生態系統支援。 它也具有 Blob 服務功能的有限存取權，例如診斷記錄。 分散的儲存解決方案很難維護，因為您必須在帳戶之間移動資料，才能完成各種案例。 您不再需要這麼做。

透過 Data Lake Storage 上的多重通訊協定存取，您可以使用工具、應用程式和服務的生態系統來處理您的資料。 這也包括協力廠商工具和應用程式。 您可以將它們指向具有階層式命名空間的帳戶，而不需要加以修改。 這些應用程式即使呼叫 Blob Api 也*會*運作，因為 Blob api 現在可以在具有階層式命名空間之帳戶中的資料上運作。

如[診斷記錄](../common/storage-analytics-logging.md)、[存取層](storage-blob-storage-tiers.md)和[blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md)等 blob 儲存體功能，現在可以使用具有階層式命名空間的帳戶。 因此，您可以在 blob 儲存體帳戶上啟用階層式命名空間，而不會失去這些重要功能的存取權。 

> [!NOTE]
> Data Lake Storage 上的多重通訊協定存取已正式運作，並可在所有區域使用。 某些由多重通訊協定存取啟用的 Azure 服務或 blob 儲存體功能仍在預覽階段。 如需詳細資訊，請參閱本文的每一節中的表格。 

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake storage 上的多重通訊協定存取如何運作

Blob Api 和 Data Lake Storage Gen2 Api 可以在具有階層命名空間之儲存體帳戶中的相同資料上運作。 Data Lake Storage Gen2 會透過階層命名空間來路由傳送 Blob Api，讓您可以取得第一個類別目錄作業和 POSIX 相容存取控制清單（Acl）的優點。 

![Data Lake Storage 概念的多重通訊協定存取](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的現有工具和應用程式會自動取得這些優點。 開發人員不需要修改它們。 無論工具和應用程式用來存取資料的通訊協定為何，Data Lake Storage Gen2 都會一致地套用目錄和檔案層級的 Acl。 

## <a name="blob-storage-feature-support"></a>Blob 儲存體功能支援

Data Lake Storage 上的多重通訊協定存取可讓您將更多的 Blob 儲存體功能與您的 Data Lake Storage 搭配使用。 下表列出 Data Lake Storage 上的多重通訊協定存取所啟用的功能。 

因為 Blob 儲存體功能的支援持續擴充，所以此資料表中出現的專案會隨著時間而改變。 

> [!NOTE]
> 雖然 Data Lake Storage 的多重通訊協定存取已正式推出，但其中某些功能的支援仍處於預覽狀態。 

|Blob 儲存體功能 | 支援層級 |
|---|---|
|[非經常性存取層](storage-blob-storage-tiers.md)|正式推出|
|Blob Sdk |正式推出|
|Blob REST Api|一般可用|
|[PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell) |正式推出|
|[CLI](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-cli) |正式推出|
|[診斷記錄](../common/storage-analytics-logging.md)| 預覽|
|[生命週期管理原則](storage-lifecycle-management-concepts.md)| 預覽|
|[透過 Azure 事件方格的通知](data-lake-storage-events.md)|預覽|
|[封存存取層](storage-blob-storage-tiers.md)| 預覽|
|[blobfuse](storage-how-to-mount-container-linux.md)|尚不支援|
|[不可變的儲存體](storage-blob-immutable-storage.md)|尚不支援|
|[快照集](storage-blob-snapshots.md)|尚不支援|
|[虛刪除](storage-blob-soft-delete.md)|尚不支援|
|[靜態網站](storage-blob-static-website.md)|尚不支援|

若要深入瞭解 Azure Data Lake Storage Gen2 的一般已知問題和限制，請參閱[已知問題](data-lake-storage-known-issues.md)。

## <a name="azure-ecosystem-support"></a>Azure 生態系統支援

Data Lake Storage 上的多重通訊協定存取也可讓您將更多 Azure 服務與您的 Data Lake Storage 連線。 下表列出 Data Lake Storage 上的多重通訊協定存取所啟用的服務。 

就像支援的 Blob 儲存體功能清單一樣，出現在此資料表中的專案會隨著時間持續擴充，而變更為 Azure 服務的支援。 

> [!NOTE]
> 雖然 Data Lake Storage 的多重通訊協定存取已正式推出，但其中某些服務的支援仍處於預覽狀態。 

|Azure 服務 | 支援層級 |
|---|---|
|[Azure 資料箱](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|正式推出|
|[Azure 事件中樞 capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|正式推出|
|[Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal)|正式推出|
|[IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|正式推出|
|[邏輯應用程式](https://azure.microsoft.com/services/logic-apps/)|正式推出|
|[Azure 認知搜尋](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|預覽|

如需 Data Lake Storage Gen2 的 Azure 生態系統支援完整清單，請參閱[整合 Azure Data Lake Storage 與 azure 服務](data-lake-storage-integrate-with-azure-services.md)。

若要深入瞭解 Azure Data Lake Storage Gen2 的一般已知問題和限制，請參閱[已知問題](data-lake-storage-known-issues.md)。

## <a name="next-steps"></a>後續步驟

查看[已知問題](data-lake-storage-known-issues.md)




