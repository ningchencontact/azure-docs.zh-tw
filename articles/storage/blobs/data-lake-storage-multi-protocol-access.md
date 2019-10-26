---
title: Azure Data Lake Storage 上的多重通訊協定存取（預覽） |Microsoft Docs
description: 使用 Blob Api 和搭配 Azure Data Lake Storage Gen2 使用 Blob Api 的應用程式。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6e3b368d1f5615c34a49b155d288d3c37c28bc26
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933127"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage-preview"></a>Azure Data Lake Storage 上的多重通訊協定存取（預覽）

Blob Api 現在可與具有階層式命名空間的帳戶搭配使用。 這可將整個工具、應用程式和服務的生態系統，以及具有階層命名空間之帳戶的所有 Blob 儲存體功能解除鎖定。

到目前為止，您可能必須為物件儲存體和分析儲存體維護個別的儲存體解決方案。 這是因為 Azure Data Lake Storage Gen2 有限的生態系統支援。 它也具有 Blob 服務功能的有限存取權，例如診斷記錄。 分散的儲存解決方案很難維護，因為您必須在帳戶之間移動資料，才能完成各種案例。 您不再需要這麼做。

> [!NOTE]
> Data Lake Storage 上的多重通訊協定存取處於公開預覽狀態，且適用于所有區域。 您不需要註冊公開預覽，因為它會自動提供給所有具有階層式命名空間的帳戶使用。 若要查看限制，請參閱[已知問題](data-lake-storage-known-issues.md)一文。

## <a name="use-the-entire-ecosystem-of-applications-tools-and-services"></a>使用應用程式、工具和服務的整個生態系統

透過 Data Lake Storage 的多重通訊協定存取，您可以使用整個工具、應用程式和服務的生態系統來處理所有資料。 這包括 azure 服務，例如[azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction)、 [IOT 中樞](https://docs.microsoft.com/azure/iot-hub/)、 [Power BI](https://docs.microsoft.com/power-bi/desktop-data-sources)及其他許多。 如需完整清單，請參閱[整合 Azure Data Lake Storage 與 Azure 服務](data-lake-store-integrate-with-azure-services.md)。

這也包括協力廠商工具和應用程式。 您可以將它們指向具有階層式命名空間的帳戶，而不需要加以修改。 這些應用程式即使呼叫 Blob Api 也*會*運作，因為 Blob api 現在可以在具有階層式命名空間之帳戶中的資料上運作。

> [!NOTE]
> 若要查看限制，請參閱[已知問題](data-lake-storage-known-issues.md)一文。

## <a name="use-all-blob-storage-features"></a>使用所有 Blob 儲存體功能

如[診斷記錄](../common/storage-analytics-logging.md)、[存取層](storage-blob-storage-tiers.md)和[blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md)等 blob 儲存體功能，現在可以使用具有階層式命名空間的帳戶。 因此，您可以在 blob 儲存體帳戶上啟用階層式命名空間，而不會失去這些重要功能的存取權。 

> [!NOTE]
> 若要查看限制，請參閱[已知問題](data-lake-storage-known-issues.md)一文。

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Data lake storage 上的多重通訊協定存取如何運作

Blob Api 和 Data Lake Storage Gen2 Api 可以在具有階層命名空間之儲存體帳戶中的相同資料上運作。 Data Lake Storage Gen2 會透過階層命名空間來路由傳送 Blob Api，讓您可以取得第一個類別目錄作業和 POSIX 相容存取控制清單（Acl）的優點。 

![Data Lake Storage 概念的多重通訊協定存取](./media/data-lake-storage-interop/interop-concept.png) 

使用 Blob API 的現有工具和應用程式會自動取得這些優點。 開發人員不需要修改它們。 無論工具和應用程式用來存取資料的通訊協定為何，Data Lake Storage Gen2 都會一致地套用目錄和檔案層級的 Acl。 

## <a name="next-steps"></a>後續步驟

查看[已知問題](data-lake-storage-known-issues.md)




