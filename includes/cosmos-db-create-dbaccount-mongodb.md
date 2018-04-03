---
title: 建立 Azure Cosmos DB MongoDB API 帳戶
description: 說明如何在 Azure 入口網站中建立 Azure Cosmos DB MongoDB API 帳戶
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
1. 在新的視窗中，登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在左側功能表中，依序按一下 [建立資源]、[資料庫]，然後在 [Azure Cosmos DB] 下按一下 [建立]。
   
   ![Azure 入口網站的螢幕擷取畫面，其中反白顯示 [其他服務] 和 Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. 在 [新增帳戶] 刀鋒視窗中指定 [MongoDB] 作為 API，並填入所需的 Azure Cosmos DB 帳戶組態。
 
    ![[新增 Azure Cosmos DB] 刀鋒視窗的螢幕擷取畫面](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * [識別碼] 必須是您要用來識別 Azure Cosmos DB 帳戶的唯一名稱。 它只能包含小寫字母、數字及 '-' 字元，且長度必須是 3 到 50 個字元。
    * [訂用帳戶] 是您的 Azure 訂用帳戶。 系統會自動加以填入。
    * [資源群組] 是您 Azure Cosmos DB 帳戶的資源群組名稱。
    * [位置] 是您的 Azure Cosmos DB 帳戶所在的地理位置。 請選擇最靠近使用者的位置。

4. 按一下 [建立]  來建立帳戶。
5. 在工具列上，按一下 [通知] 以監視部署程序。

    ![「部署已啟動」通知](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  當部署完成時，從 [所有資源] 圖格開啟新的帳戶。 

    ![[所有資源] 圖格上的 Azure Cosmos DB 帳戶](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
