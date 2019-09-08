---
title: 裝載 Azure HPC 快取
description: 如何將用戶端連線至 Azure HPC 快取服務
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 0ccd256ed399fff7c364883ada45d2d15c75a120
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775347"
---
# <a name="mount-the-azure-hpc-cache"></a>裝載 Azure HPC 快取

建立快取之後，NFS 用戶端就可以使用簡單的 mount 命令來存取它。

使用 [快取總覽] 頁面上所列的掛接位址，以及您在建立儲存體目標時所設定的虛擬命名空間路徑。 

![Azure HPC 快取實例 [總覽] 頁面的螢幕擷取畫面，並在右下方的 [掛接位址] 清單周圍加上醒目提示方塊](media/mount-addresses.png)

> [!NOTE] 
> 快取掛接位址會對應至快取子網內的網路介面。 這些 nic 會出現在名稱結尾`-cluster-nic-`為和數位的資源群組中。 請勿更改或刪除這些介面，否則快取將會變成無法使用。

虛擬命名空間路徑會顯示在 [**儲存體目標**] 頁面中。 
![快取 [儲存體目標] 面板的螢幕擷取畫面，在資料表的 [路徑] 資料行中有一個專案周圍的醒目提示方塊](media/storage-target-path.png)

## <a name="mount-command-syntax"></a>掛接命令語法

使用掛接命令，如下所示：

> sudo mount *cache_mount_address*：/*namespace_path* *local_path* {*options*}

範例： 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

此命令成功之後，儲存體匯出的內容應該會顯示在用戶端上``hpccache``的目錄中。 

> [!NOTE] 
> 您的用戶端必須能夠存取裝載快取的虛擬網路和子網。 例如，在相同的虛擬網路內建立用戶端 Vm，或使用端點、閘道或虛擬網路中的其他解決方案，從外部進行存取。 請記住，快取的子網內不能裝載任何其他東西。

### <a name="mount-command-options"></a>掛接命令選項

若是健全的用戶端掛接，請在您的 mount 命令中傳遞這些設定和引數： 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 建議的掛接命令設定 | |
--- | --- 
``hard`` | 對 vFXT 叢集執行軟掛接可能會產生應用程式失敗和資料遺失的狀況。 
``proto=netid`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=netid`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=n`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

## <a name="next-steps"></a>後續步驟

* 若要將資料移至快取的儲存體目標，請閱讀[填入新的 Azure Blob 儲存體](hpc-cache-ingest.md)。
