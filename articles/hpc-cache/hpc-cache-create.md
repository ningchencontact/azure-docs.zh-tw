---
title: 建立 Azure HPC Cache
description: 如何建立 Azure HPC Cache 執行個體
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: e1b69f17d964647944f23f4d16a0a1a5f112b60d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036996"
---
# <a name="create-an-azure-hpc-cache"></a>建立 Azure HPC Cache

使用 Azure 入口網站建立快取。

![Azure 入口網站中快取概觀的螢幕擷取畫面，其中 [建立] 按鈕位於底部](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>定義基本詳細資料

![Azure 入口網站中專案詳細資料頁面的螢幕擷取畫面](media/hpc-cache-create-basics.png)

在 [專案詳細資料]  中，選取將裝載 Azure HPC Cache 的訂用帳戶和資源群組。 請確定訂用帳戶位於[預覽權限](hpc-cache-prereqs.md#azure-subscription)清單上。

在 [服務詳細資料]  中，設定快取名稱和其他屬性：

* 位置 - 選取其中一個[支援的區域](hpc-cache-overview.md#region-availability)。
* 虛擬網路 - 您可以選取現有的虛擬網路，或建立一個新的虛擬網路。
* 子網路 - 選擇或建立子網路，必須至少有 64 個 IP 位址 (/24) 且只會用於 Azure HPC Cache。

## <a name="set-cache-capacity"></a>設定快取容量
<!-- change link in GUI -->

在 [快取]  頁面上，您必須設定 Azure HPC Cache 的容量。 此值決定您的快取可以保存多少資料，以及它服務用戶端要求的速度。 在公開預覽期間之後，容量也會影響快取的成本。

快取容量是以每秒輸入/輸出作業 (IOPS) 來測量。 藉由設定這兩個值來選擇容量：

* 快取 (輸送量) 的資料傳輸率上限 (以 GB/秒為單位)
* 配置給快取資料的儲存體數量 (以 TB 為單位)

選擇其中一個可用的輸送量值和快取儲存體大小。 IOPS 容量會計算出來，並顯示在值選取器底下。

請記住，實際的資料傳輸速率取決於工作負載、網路速度，以及儲存體目標的類型。 如果檔案不在快取中，或標示為過時，服務將會使用一些輸送量從後端儲存體擷取該檔案。 您選擇的值會設定整個快取的最大輸送量，而不是所有用戶端要求可使用的輸送輛。

針對快取儲存體，Azure HPC Cache 會管理要快取和預先載入哪些檔案，以最大化快取命中率。 系統會持續評估快取內容，並將較不常存取的檔案移至長期儲存體。 選擇可輕鬆保存使用中檔案集的快取儲存體大小，且有額外空間可容納中繼資料和其他負荷。

![快取大小頁面的螢幕擷取畫面](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>新增儲存體目標

儲存體目標是快取內容的後端長期儲存體。

您可以在建立快取時定義儲存體目標，但您也可以在稍後，使用入口網站中快取頁面 [設定]  區段中的連結來新增它們。

![儲存體目標頁面的螢幕擷取畫面](media/hpc-cache-storage-targets-pop.png)

按一下 [新增儲存體目標]  連結，以定義您的後端儲存體系統。 儲存體可以是 Azure Blob 容器或內部部署 NFS 系統。

您最多可以定義十個不同的儲存體目標。

[新增儲存體](hpc-cache-add-storage.md)中有新增儲存體目標的逐步指示。 Blob 儲存體和 NFS 匯出的程序不同。

以下是一些秘訣： 

* 對於這兩種類型的儲存體，您都必須指定如何尋找後端儲存體系統 (NFS 位址或 Blob 容器名稱) 和用戶端對應的命名空間路徑。

* 建立 Blob 儲存體目標時，請確定快取具有儲存體帳戶的存取權限，如[新增存取控制角色](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中所述。 如果您不確定角色設定是否會成功，請先建立快取，然後再新增 Blob 儲存體。

* 建立 NFS 儲存體目標時，請指定[使用量模型](hpc-cache-add-storage.md#choose-a-usage-model)。 使用量模型設定可協助快取將您的工作流程最佳化。

## <a name="add-resource-tags-optional"></a>新增資源標記 (選擇性)

[標籤]  頁面可讓您將[資源標籤](https://go.microsoft.com/fwlink/?linkid=873112) \(英文\) 新增至 Azure HPC Cache。 

## <a name="finish-creating-the-cache"></a>完成建立快取

設定新的快取之後，按一下 [檢閱 + 建立]  索引標籤。入口網站會驗證您的選擇，並讓您檢閱您的選擇。 如果所有設定都正確，請按一下 [建立]  。 

建立快取需要大約 10 分鐘。 您可以在 Azure 入口網站的通知面板中追蹤進度。 

![入口網站中快取建立的「部署進行中」和「通知」頁面螢幕擷取畫面](media/hpc-cache-deploy-status.png)

建立完成後，會出現包含新 Azure HPC Cache 執行個體連結的通知，而快取會出現在訂用帳戶的**資源**清單中。 

![Azure 入口網站中 Azure HPC Cache 執行個體的螢幕擷取畫面](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>後續步驟

當您的快取出現在 [資源]  清單之後，您可以掛接它供用戶端存取、使用它將您的工作集資料移至新的 Azure Blob 儲存體目標，或定義其他資料來源。

* [掛接 Azure HPC Cache](hpc-cache-mount.md)
* [針對 Azure HPC Cache 將資料移動至 Azure Blob 儲存體](hpc-cache-ingest.md)
* [新增儲存體目標](hpc-cache-add-storage.md)
