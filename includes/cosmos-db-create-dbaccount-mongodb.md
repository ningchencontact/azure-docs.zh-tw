---
title: 包含檔案
description: 包含檔案
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 8b5b7126a264d6a15010fc18288f4a4ba1263344
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2018
ms.locfileid: "53286160"
---
1. 在新的視窗中，登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在左側功能表中，依序按一下 [建立資源]、[資料庫]，然後在 [Azure Cosmos DB] 下按一下 [建立]。
   
   ![Azure 入口網站的螢幕擷取畫面，其中反白顯示 [其他服務] 和 Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. 在 [建立 Azure Cosmos DB 帳戶] 頁面中，輸入新的 Azure Cosmos DB 帳戶的設定。 
 
    設定|值|說明
    ---|---|---
    訂用帳戶|您的訂用帳戶|選取要用於此 Azure Cosmos DB 帳戶的 Azure 訂用帳戶。 
    資源群組|新建<br><br>然後輸入識別碼中所提供的同一個唯一名稱|選取 [建立新的]。 然後為您的帳戶輸入新的資源群組名稱。 為求簡化，請使用和識別碼相同的名稱。 
    帳戶名稱|輸入唯一名稱|輸入唯一名稱來識別您的 Azure Cosmos DB 帳戶。 因為 documents.azure.com 會附加到您所提供的識別碼以建立 URI，請使用唯一識別碼。<br><br>識別碼只能使用小寫字母、數字及連字號 (-) 字元。 長度必須介於 3 到 31 個字元之間。
    API|MongoDB|API 會決定要建立的帳戶類型。 Azure Cosmos DB 提供五個 API：Core(SQL) (適用於文件資料庫)、Gremlin (適用於圖形資料庫)、MongoDB (適用於文件資料庫)、Azure 資料表和 Cassandra。 目前，您必須為每個 API 建立個別個帳戶。 <br><br>選取 [MongoDB]，因為在此快速入門中，您會建立可搭配 MongoDB API 使用的資料表。|
    位置|選取最接近使用者的區域|選取用來裝載 Azure Cosmos DB 帳戶的地理位置。 使用最接近使用者的位置，以便他們能以最快速度存取資料。

    選取 [檢閱 + 建立]。 您可以略過 [網路] 和 [標記] 區段。 

    ![Azure Cosmos DB 的新帳戶頁面](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. 建立帳戶需要幾分鐘的時間。 等候入口網站顯示 [恭喜!您的 Azure Cosmos DB 帳戶及 MongoDB API 已就緒] 頁面。

    ![Azure 入口網站的 [通知] 窗格](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
