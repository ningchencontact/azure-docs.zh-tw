---
title: Azure 轉譯參考架構 - Azure Batch
description: 使用 Azure Batch 和其他 Azure 服務，藉由高載至雲端來擴充內部部署轉譯伺服器陣列的架構
services: batch
ms.service: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: ae4680c948ce8e1efd32207dc37821d61182f2d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774141"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure 轉譯的參考架構

本文將針對要將內部部署轉譯伺服器陣列擴充 (或「高載」) 至 Azure 的案例，提供高階架構圖表。 各個範例會顯示 Azure 計算、網路和儲存體服務的不同選項。

## <a name="hybrid-with-nfs-or-cfs"></a>混合使用 NFS 或 CFS

下圖顯示包含下列 Azure 服務的混合式案例：

* **計算** - Azure Batch 集區或虛擬機器擴展集。

* **網路** - 內部部署：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **儲存體** - 輸入和輸出檔案：使用 Azure VM 的 NFS 或 CFS，可透過 Azure 檔案同步或 RSync 來與內部部署儲存體同步處理。 或者：用 Avere vFXT 從使用 NFS 的內部部署 NAS 裝置輸入或輸出檔案。

  ![雲端高載 - 混合使用 NFS 或 CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>混合使用 Blobfuse

下圖顯示包含下列 Azure 服務的混合式案例：

* **計算** - Azure Batch 集區或虛擬機器擴展集。

* **網路** - 內部部署：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **儲存體** - 輸入和輸出檔案：Blob 儲存體，可透過 Azure Blobfuse 掛接至計算資源。

  ![雲端高載 - 混合使用 Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>混合式計算和儲存體

下圖顯示計算和儲存體已完整連線的混合式案例，其中包含下列 Azure 服務：

* **計算** - Azure Batch 集區或虛擬機器擴展集。

* **網路** - 內部部署：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **儲存體** - 跨單位：Avere vFXT。 選擇性地透過 Azure 資料箱將內部部署檔案封存至 Blob 儲存體，或內部部署 Avere FXT for NAS 加速。

  ![雲端高載 - 混合式計算和儲存體](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>後續步驟

* 深入了解如何透過 Azure Batch 使用[轉譯管理員](batch-rendering-render-managers.md)。

* 深入了解 [Azure 中的轉譯](batch-rendering-service.md)選項。