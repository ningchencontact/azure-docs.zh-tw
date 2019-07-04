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
ms.openlocfilehash: 127d67cc3b5dcd0ddd585470821eb1baa08c2388
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174525"
---
您現在可以使用 [資料總管] 將資料新增至您的新集合。

1. 在 [資料總管] 中，新的資料庫會出現在 [集合] 窗格中。 依序展開 [工作]  資料庫、[項目]  集合，按一下 [文件]  ，然後按一下 [新增文件]  。 

   ![在 Azure 入口網站的 [資料總管] 中建立新文件](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. 現在將文件新增至具有下列結構的集合。

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. 將 json 新增至 [文件]  索引標籤後，按一下 [儲存]  。

    ![將 json 資料複製在 Azure 入口網站的 [資料總管] 中並按一下 [儲存]](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  多建立並儲存一份文件，以便在其中插入 `id` 屬性的唯一值，並適當變更其他屬性。 新文件可擁有您想要的任何結構，因為 Azure Cosmos DB 不會對您的資料強加任何結構描述。