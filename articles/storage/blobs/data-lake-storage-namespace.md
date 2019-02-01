---
title: Azure Data Lake Storage Gen2 預覽版階層式命名空間
description: 說明 Azure Data Lake Storage Gen2 預覽版階層式命名空間的概念
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 967e24ae6e004fe6ce2b1c0aa6c039f46be2598c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244499"
---
# <a name="azure-data-lake-storage-gen2-preview-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 預覽版階層式命名空間

若要允許 Azure Data Lake Storage Gen2 預覽版以物件儲存體的規模和價格提供檔案系統效能，其關鍵機制在於新增**階層式命名空間**。 這會收集帳戶中的物件/檔案集合，並整理成階層式目錄和巢狀子目錄，此方式與整理電腦上的檔案系統方式相同。 啟用階層命名空間後，儲存體帳戶便能提供物件儲存體的延展性和成本效益，並具有分析引擎及架構所熟悉的檔案系統語意。

## <a name="the-benefits-of-the-hierarchical-namespace"></a>階層式命名空間的優勢

以下優勢是關於在 blob 資料上執行階層式命名空間的檔案系統：

- **不可部分完成的目錄操作：** 物件存放區會採用在物件名稱中內嵌斜線 (/) 以表示路徑線段的慣例，來模仿目錄階層的架構。 雖然此慣例適用於整理物件，但此慣例不提供移動、重新命名或刪除目錄等協助動作。 如果沒有真正的目錄，應用程式就必須處理可能數百萬個單獨的 blob 才能達到目錄層級的工作。 相較之下，階層式命名空間以更新單一項目 (父目錄) 的方式來處理這些工作。

    這種大幅最佳化對於許多巨量資料分析架構來說尤為重要。 Hive 和 Spark 等工具經常將輸出寫入暫時位置，然後在作業結束時重新命名該位置。 如果沒有階層式命名空間，重新命名所花費的時間通常會比分析流程本身更長。 較低的作業延遲等於較低的分析工作負載擁有權總成本 (TCO)。

- **熟悉的介面樣式：** 開發人員和使用者都很熟悉檔案系統。 當您前往雲端時不需要學習新的儲存體範例，因為 Data Lake Storage Gen2 公開的檔案系統介面與大小和電腦使用的範例相同。

以往物件儲存不支援階層式命名空間的原因之一，是因為階層式命名空間限制了規模。 但是，Data Lake Storage Gen2 階層式命名空間能夠線性擴展，不會降低資料容量或效能。

## <a name="when-to-enable-the-hierarchical-namespace"></a>階層式命名空間啟用時機

對於針對操作目錄的檔案系統所設計的儲存體工作負載而言，建議啟用階層式命名空間。 這包括主要用來分析處理的所有工作負載。 需要有效組織的資料集也會因啟用階層式命名空間而受益。

啟用階層式命名空間的原因由 TCO 分析確定。 一般而言，若要改善因儲存體加速而導致的工作負載延遲，需要計算資源才能縮短時間。 由於階層式命名空間啟用的不可部分完成目錄操作，許多工作負載的延遲可能因此獲得改善。 在許多工作負載中，計算資源佔總成本的 85％ 以上，因此即使適度減少工作負載延遲也相當於大量節省 TCO。 即使啟用階層式命名空間會提高儲存體成本，但由於計算成本降低，TCO 仍會降低。

## <a name="when-to-disable-the-hierarchical-namespace"></a>階層式命名空間停用時機

某些物件儲存工作負載可能無法因啟用階層式命名空間而受益。 這些工作負載範例包括備份、影像儲存及其他應用程式，其中物件組織與物件本身會分開儲存 (*例如*在單獨的資料庫中)。


## <a name="next-steps"></a>後續步驟

- [建立儲存體帳戶](./data-lake-storage-quickstart-create-account.md)
