---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/24/2018
ms.openlocfilehash: 99c31293168fd5ff3e6b95a70dc481e01e4ac8b4
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400179"
---
使用您所使用之 Azure 訂用帳戶的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 如果您沒有 Azure 訂用帳戶，請立即建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

只有在 Edge、Chrome 與 Firefox 瀏覽器上才支援入口網站的工作區儀表板。

   ![Azure 入口網站](./media/aml-create-in-portal/portal-dashboard.png)

在入口網站的左上角，選取 [建立資源]。

   ![在 Azure 入口網站中建立資源](./media/aml-create-in-portal/portal-create-a-resource.png)

在搜尋列中輸入 **Machine Learning**。 選取 **Machine Learning Service 工作區**的搜尋結果。

   ![搜尋工作區](./media/aml-create-in-portal/allservices-search.PNG)

在 [Machine Learning Service 工作區] 窗格中捲動至底部，並選取 [建立] 開始操作。

   ![建立](./media/aml-create-in-portal/portal-create-button.png)

在 [ML 服務工作區] 窗格中，設定您的工作區。

   欄位|說明
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此我們使用 docs-ws。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。  
   訂用帳戶 |選取您要使用的 Azure 訂用帳戶。 如果您有多個訂用帳戶，請選取用於計費的訂用帳戶。
   資源群組 | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組是存放 Azure 方案相關資源的容器。 在此我們使用 docs-aml。 
   位置 | 選取最接近您的使用者與資料資源的位置。 此位置是建立工作區的所在位置。

   ![建立工作區](./media/aml-create-in-portal/workspace-create.png)

若要開始執行建立程序，請選取 [建立]。 建立工作區可能需要一些時間。

若要檢查部署狀態，請選取工具上的通知圖示 (鈴鐺)。

   ![工作區建立狀態](./media/aml-create-in-portal/notifications.png)

程序完成後，會出現部署成功訊息。 它也會出現在通知區段中。 若要檢視新的工作區，選取 [前往資源]。
