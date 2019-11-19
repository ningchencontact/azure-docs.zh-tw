---
title: 更新 Azure HPC Cache 儲存體目標
description: 如何編輯 Azure HPC Cache 儲存體目標
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168510"
---
# <a name="edit-storage-targets"></a>編輯儲存體目標

您可以編輯儲存體目標，以修改其部分屬性。 針對不同類型的儲存體，可以編輯不同的屬性：

* 針對 Blob 儲存體目標，您可以變更命名空間路徑。

* 針對 NFS 儲存體目標，您可以變更下列屬性：

  * 命名空間路徑
  * 使用方式模型
  * 匯出
  * 匯出子目錄

您無法編輯儲存體目標的名稱、類型或後端儲存系統（Blob 容器或 NFS 主機名稱/IP 位址）。 如果您需要變更這些屬性，請刪除儲存體目標，並建立新值的取代。

若要修改儲存體目標，請按一下儲存體目標名稱，以開啟其詳細資料頁面。 頁面中的某些欄位是可編輯的。

![NFS 儲存體目標之 [編輯] 頁面的螢幕擷取畫面](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>更新 NFS 儲存體目標

針對 NFS 儲存體目標，您可以更新數個屬性。 （如需範例 [編輯] 頁面，請參閱上面的螢幕擷取畫面）。

* **使用方式模型**-使用量模型會影響快取保留資料的方式。 若要深入瞭解，[請參閱選擇使用方式模型](hpc-cache-add-storage.md#choose-a-usage-model)。
* **虛擬命名空間路徑**-用戶端用來掛接此儲存體目標的路徑。 如需詳細資訊，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。
* **NFS 匯出路徑**-要用於此命名空間路徑的儲存體系統匯出。
* **子目錄路徑**-要與這個命名空間路徑相關聯的子目錄（在匯出下）。 如果您不需要指定子目錄，請將此欄位保留空白。

每個命名空間路徑都需要匯出和子目錄的唯一組合。 也就是說，您無法在後端儲存體系統上，將兩個不同的用戶端對應路徑放到完全相同的目錄。

進行變更之後，請按一下 **[確定]** 以更新儲存體目標，或按一下 [**取消**] 來捨棄變更。

## <a name="update-an-azure-blob-storage-target"></a>更新 Azure Blob 儲存體目標

Blob 儲存體目標的 [詳細資料] 頁面可讓您修改虛擬命名空間路徑。

![blob 儲存體目標之 [編輯] 頁面的螢幕擷取畫面](media/hpc-cache-edit-storage-blob.png)

完成後，按一下 **[確定]** 以更新儲存體目標，或按一下 [**取消**] 以捨棄變更。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解這些選項，請參閱[新增儲存體目標](hpc-cache-add-storage.md)。
* 如需使用虛擬路徑的更多秘訣，請參閱[規劃匯總的命名空間](hpc-cache-namespace.md)。
