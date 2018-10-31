---
title: 包含檔案
description: 包含檔案
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e499a0f7bec47e672c599c729a15cc3e3d04a28a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471610"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>建立事件處理器主機的儲存體帳戶
事件處理器主機是一個智慧型代理程式，透過管理持續檢查點以及平行接收，簡化來自事件中樞的事件接收作業。 若要使用檢查點，事件處理器主機需要儲存體帳戶。 下列範例會示範如何建立儲存體帳戶，以及如何取得其存取金鑰：

1. 在 Azure 入口網站中，選取畫面左上方的 [建立資源]。

2. 選取 [儲存體]，然後選取 [儲存體帳戶 - Blob、檔案、資料表、佇列]。
   
    ![選取儲存體帳戶](./media/event-hubs-create-storage/create-storage1.png)

3. 在 [建立儲存體帳戶] 頁面上，採取以下步驟︰ 

    1. 輸入儲存體帳戶的名稱。 
    2. 選擇含有事件中樞的 Azure 訂用帳戶。
    3. 選取具有事件中樞的資源群組。
    4. 選取要在其中建立資源的位置。 
    5. 然後按一下 [檢閱 + 建立]。
   
    ![建立儲存體帳戶 - 頁面](./media/event-hubs-create-storage/create-storage2.png)

4. 在 [檢閱 + 建立] 頁面上，檢閱每個值，然後選取 [建立]。 

    ![檢閱儲存體帳戶設定並建立](./media/event-hubs-create-storage/review-create-storage-account.png)
5. 在看到「部署成功」訊息之後，選取頁面頂端的 [前往資源]。 您也可以從資源清單中選取儲存體帳戶來啟動 [儲存體帳戶] 頁面。  

    ![選取部署中的儲存體帳戶](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. 在 [基本資訊] 視窗中，選取 [Blob]。 

    ![選取 [Blob] 服務](./media/event-hubs-create-storage/select-blobs-service.png)
1. 選取頂端的 [+ 容器]輸入容器的 [名稱]，然後選取 [確定]。 

    ![建立 Blob 容器](./media/event-hubs-create-storage/create-blob-container.png)
1. 選取左側窗格中的 [存取金鑰]，然後複製 [Key1] 的值。 

    將下列值儲存到「記事本」或一些其他暫存位置。
    - 儲存體帳戶的名稱
    - 儲存體帳戶的存取金鑰
    - 容器的名稱