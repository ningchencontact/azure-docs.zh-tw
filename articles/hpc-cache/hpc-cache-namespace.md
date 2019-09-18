---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036849"
---
# <a name="configure-aggregated-namespace"></a>設定匯總命名空間
<!-- change link in GUI -->

Azure HPC 快取可讓用戶端透過會隱藏後端儲存系統詳細資料的虛擬命名空間，來存取各種不同的儲存系統。

當您新增儲存體目標時，會設定用戶端面向的 filepath。 用戶端電腦會掛接這個 filepath。 您可以變更與該路徑相關聯的儲存體目標。 例如，您可以更換硬體儲存系統與雲端存放裝置，而不需要重寫用戶端面向的程式。

## <a name="aggregated-namespace-example"></a>匯總命名空間範例

規劃您的匯總命名空間，讓用戶端電腦可以方便地觸達所需的資訊，而且系統管理員和工作流程工程師可以輕鬆區分這些路徑。

例如，假設有一個系統，其中 Azure HPC 快取實例用來處理儲存在 Azure Blob 中的資料。 分析需要儲存在內部部署資料中心內的範本檔案。

範本資料會儲存在資料中心，而此作業所需的資訊會儲存在這些子目錄中：

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

資料中心儲存體系統會公開這些匯出： 

    /
    /goldline
    /goldline/templates

使用[CLFSLoad 公用程式](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)，將要分析的資料複製到名為 "sourcecollection" 的 Azure Blob 儲存體容器。

若要允許透過快取輕鬆存取，請考慮使用這些虛擬命名空間路徑來建立儲存體目標：

| 後端 NFS filepath 或 Blob 容器 | 虛擬命名空間路徑 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source               |

因為 NFS 來源路徑是相同匯出的子目錄，所以您必須從相同的儲存體目標定義多個命名空間路徑。 

| 儲存體目標主機名稱  | NFS 匯出路徑      | 子目錄路徑 | 命名空間路徑    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 位址或主機名稱* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP 位址或主機名稱* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

用戶端應用程式可以掛接快取，並輕鬆地存取匯總的命名空間 filepaths/source、/templates/sku798 和/templates/sku980。

## <a name="next-steps"></a>後續步驟

在決定如何設定虛擬檔案系統之後，請[建立儲存體目標](hpc-cache-add-storage.md)，以將後端儲存體對應至用戶端對向虛擬 filepaths。
