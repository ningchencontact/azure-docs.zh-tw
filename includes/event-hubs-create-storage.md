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
ms.openlocfilehash: 690e0da160042b7ac432b1a36c024a8a8f439823
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125109"
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
5. 您會看到之後**部署成功**訊息，請選取**移至資源**頁面的頂端。 您也可以從資源清單中選取儲存體帳戶來啟動 [儲存體帳戶] 頁面。  

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
