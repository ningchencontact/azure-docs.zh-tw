---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823128"
---
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左功能表上的 [所有服務]，然後選取 [行動] 區段中的 [通知中樞]。 選取服務名稱旁邊的星號 (`*`)，將它加到左功能表上的 [我的最愛] 區段。 在 [通知中樞] 加到 [我的最愛] 之後，在左功能表上予以選取。 

      ![Azure 入口網站 - 選取通知中樞](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. 在 [通知中樞] 頁面上，選取工具列上的 [新增]。 

      ![通知中樞 - 新增工具列按鈕](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. 在 [通知中樞] 頁面上，執行下列步驟： 
    1. 指定通知**中樞**的**名稱**。  
    2. 指定**命名空間**的**名稱**。
    3. 選取您要在其中建立通知中樞的**位置**。 
    4. 選取現有的資源群組，或針對新的**資源群組**輸入名稱。
    5. 選取 [建立] 。 

        ![Azure 入口網站 - 設定通知中樞屬性](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. 選取 [通知]\(鈴鐺圖示)，然後選取 [移至資源]，或重新整理 [通知中樞] 頁面中的清單，然後選取您的通知中樞。 

      ![Azure 入口網站 - 通知 -> 前往資源](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. 從清單中選取 [存取原則]。 請記下您可使用的兩個連接字串。 您稍後需要用到這些連接字串來處理推播通知。

      >[!IMPORTANT]
      >請**勿**在應用程式中使用 DefaultFullSharedAccessSignature。 這只能在後端使用。
      >

      ![Azure 入口網站 - 通知中樞連接字串](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
