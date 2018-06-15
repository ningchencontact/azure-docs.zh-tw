---
title: 了解如何在邏輯應用程式中使用 Twitter 連接器 | Microsoft Docs
description: 搭配 REST API 參數來使用 Twitter 連接器的概觀
services: ''
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: b44a973a94043f71f2fd9803abca47652363d8a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296538"
---
# <a name="get-started-with-the-twitter-connector"></a>開始使用 Twitter 連接器
使用 Twitter 連接器，您可以：

* 張貼推文並取得推文
* 存取時間軸、好友和粉絲
* 執行本文所述的任何其他動作和觸發程序

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)來開始。  

## <a name="connect-to-twitter"></a>連接到 Twitter
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

### <a name="create-a-connection-to-twitter"></a>建立 Twitter 連線
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>使用 Twitter 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)。

在此範例中，您會使用「**當有新推文張貼時**」觸發程序來搜尋 #Seattle。 如果找到 #Seattle，則使用推文中的文字更新 Dropbox 中的檔案。 在企業範例中，您可以搜尋您的公司名稱，並以推文中的文字更新 SQL Database。

1. 在 Logic Apps 設計工具的搜尋方塊中輸入 *twitter*，然後選取 [Twitter - 當有新推文張貼時] 觸發程序   
   ![Twitter 觸發程序圖 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. 在 [搜尋文字] 控制項中輸入 *#Seattle*  
   ![Twitter 觸發程序圖 2](./media/connectors-create-api-twitter/trigger-2.png) 

此時，邏輯應用程式已設有觸發程序，該觸發程序會開始執行工作流程中的其他觸發程序和動作。 

> [!NOTE]
> 為了讓邏輯應用程式能正常運作，它必須包含至少一個觸發程序和一個動作。 請使用下一節中的步驟新增動作。

## <a name="add-a-condition"></a>新增條件
我們只關注粉絲有 50 位以上的使用者貼文。 因此，確認粉絲人數的條件會先新增至邏輯應用程式。  

1. 選取 [+ 新步驟] 來新增您想要在新推文中找到 #Seattle 時採取的動作  
   ![Twitter 動作圖 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. 選取 [新增條件] 連結。  
   ![Twitter 條件圖 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   這會開啟可供您檢查條件的 [條件] 控制項，例如「等於」、「小於」、「大於」、「包含」等等。  
   ![Twitter 條件圖 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. 選取 [選擇值] 控制項。 在此控制項中，您可以從任何先前的動作或觸發程序選取一或多個屬性。 此屬性值的條件會評估為 true 或 false。
   ![Twitter 條件圖 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. 選取 [...] 展開屬性清單，以便查看所有的可用屬性。        
   ![Twitter 條件圖 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. 選取 [粉絲計數] 屬性。    
   ![Twitter 條件圖 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. 請注意，[粉絲計數] 屬性現在位於值控制項中。    
   ![Twitter 條件影像 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. 從運算子清單選取 [大於]。    
   ![Twitter 條件圖 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. 輸入 50 做為「大於」運算子的運算元。  
   現已新增此條件。 使用功能表的 [儲存] 連結來儲存您的工作。    
   ![Twitter 條件圖 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>使用 Twitter 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)。  

現在已有了觸發程序，請新增動作以張貼包含觸發程序所找到之推文內容的新推文。 針對本逐步解說，只會張貼粉絲有 50 位以上的使用者推文。  

下一個步驟中會新增 Twitter 動作，若使用者的粉絲有 50 位以上，則此動作會使用該使用者每則貼文的部分屬性來張貼推文。  

1. 選取 [新增動作]。 此步驟會開啟搜尋控制項，您可以在其中搜尋其他動作和觸發程序。  
   ![Twitter 條件圖 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. 在搜尋方塊中輸入 *twitter*，然後選取 [Twitter - 張貼推文] 動作。 此步驟會開啟 [張貼推文] 控制項，您將在其中輸入所張貼推文的所有詳細資料。      
   ![Twitter 動作圖 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. 選取 [推文文字] 控制項。 前述動作和觸發程序的所有輸出現在可顯示在邏輯應用程式中。 您可以選取上述任何輸出，並使用它們做為新推文的部分推文文字。     
   ![Twitter 動作圖 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. 選取 [使用者名稱]   
5. 在推文文字控制項中的使用者名稱後，立即輸入「說：」。
6. 選取「推文文字」。       
   ![Twitter 動作圖 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. 啟動您的工作流程並儲存工作，然後傳送具有 #Seattle 主題標籤的推文。


## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/twitterconnector/)的所有限制。 

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)