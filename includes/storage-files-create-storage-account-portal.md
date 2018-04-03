---
title: storage-files-create-storage-account-portal
description: 如何建立 Azure 檔案服務的儲存體帳戶。
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
儲存體帳戶是您可在其中部署 Azure 檔案共用或其他儲存體資源 (例如 blob 或佇列) 的共用儲存體集區。 儲存體帳戶可包含無限制數目的共用，而共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。

建立儲存體帳戶：

1. 在左功能表中，按一下 **+** 以建立資源。
2. 在搜尋方塊 (1) 中輸入**儲存體帳戶** 並選取 [儲存體帳戶 - Blob、檔案、資料表、佇列] (2)，然後按一下 [建立]。
    ![資源搜尋對話方塊中儲存體帳戶項目外觀的螢幕擷取畫面](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. 在 [名稱] 中，輸入 mystorageacct 且後面加上幾個隨機數字，直到出現綠色核取記號，表示它唯一的名稱為止。 儲存體帳戶名稱必須全部小寫，並且是全域唯一的。 請記下您的儲存體帳戶名稱，因為您稍後會使用到它。 
4. 在 [部署模型] 中，保留 [Resource Manager] 的預設值。 若要深入了解 Azure Resource Manager 與傳統部署之間的詳細資訊，請參閱[了解部署模型和資源的狀態](../articles/azure-resource-manager/resource-manager-deployment-model.md)。
5. 在 [帳戶種類] 中選取 [StorageV2]。 若要深入了解不同種類的儲存體帳戶，請參閱[了解 Azure 儲存體帳戶](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
6. 在 [效能] 中，保留 [標準儲存體] 的預設值。 Azure 檔案服務目前僅支援標準儲存體；即使您選取進階儲存體，檔案共用仍會儲存在標準儲存體上。
7. 在 [複寫] 中，選取 [本地備援儲存體 (LRS)]。 
8. 在 [需要安全傳輸] 中，我們建議一律選取 [已啟用]。 若要深入了解這個選項，請參閱[了解傳輸中加密](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
9. 在 [訂用帳戶] 中，選取要在其中建立儲存體帳戶的訂用帳戶。 如果您只有一個訂用帳戶，它應該是預設訂用帳戶。
10. 在 [資源群組] 中，選取 [新建] 並輸入 myResourceGroup 作為名稱。
11. 在 [位置] 中，選取 [美國東部]。
12. 在 [虛擬網路] 中，將預設選項保留為 [已停用]。 
13. 選取 [釘選到儀表板]，讓您更輕鬆地尋找儲存體帳戶。
14. 當您完成時，請按一下 [建立] 以開始部署。