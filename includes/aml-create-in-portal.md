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
ms.openlocfilehash: 05331c710817e575deb7729189c9b2d8ccbafd7d
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2019
ms.locfileid: "54489537"
---
1. 使用您所使用之 Azure 訂用帳戶的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

   ![Azure 入口網站](./media/aml-create-in-portal/portal-dashboard.png)

1. 在入口網站的左上角，選取 [建立資源]。

   ![在 Azure 入口網站中建立資源](./media/aml-create-in-portal/portal-create-a-resource.png)

1. 在搜尋列中輸入 **Machine Learning**。 選取 **Machine Learning Service 工作區**的搜尋結果。

   ![搜尋工作區](./media/aml-create-in-portal/allservices-search.PNG)

1. 在 [ML 服務工作區] 窗格中捲動至底部，並選取 [建立] 開始操作。

   ![建立](./media/aml-create-in-portal/portal-create-button.png)

1. 在 [ML 服務工作區] 窗格中，設定您的工作區。

   欄位|說明
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。  
   訂用帳戶 |選取您要使用的 Azure 訂用帳戶。
   資源群組 | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組是存放 Azure 方案相關資源的容器。 在此範例中，我們使用 **docs-aml**。 
   位置 | 選取最接近您的使用者與資料資源的位置。 此位置是建立工作區的所在位置。

   ![建立工作區](./media/aml-create-in-portal/workspace-create.png)

1. 若要開始執行建立程序，請選取 [建立]。 建立工作區可能需要一些時間。

1. 若要檢查部署狀態，請選取工具列上的通知圖示 (鈴鐺)。

1. 程序完成後，會出現部署成功訊息。 它也會出現在通知區段中。 若要檢視新的工作區，選取 [前往資源]。

   ![工作區建立狀態](./media/aml-create-in-portal/notifications.png)
