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
ms.date: 05/21/2019
ms.openlocfilehash: 2ec45b67367198c14fc9d03cdb659a51aed8a504
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841527"
---
1. 使用您所使用之 Azure 訂用帳戶的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

   ![Azure 入口網站](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. 在入口網站的左上角，選取 [建立資源]  。

   ![在 Azure 入口網站中建立資源](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. 若要使用搜尋列，請選取 **Machine Learning 服務工作區**。

   ![搜尋工作區](./media/aml-create-in-portal/allservices-search.png)

1. 在 [ML 服務工作區]  窗格中選取 [建立]  來開始操作。

    ![建立按鈕](./media/aml-create-in-portal/portal-create-button.png)

1. 在 [ML 服務工作區]  窗格中，設定您的工作區。

    ![建立工作區](./media/aml-create-in-portal/workspace-create-main-tab.png)

   欄位|說明
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。  
   Subscription |選取您要使用的 Azure 訂用帳戶。
   Resource group | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 
   Location | 選取最接近您的使用者與資料資源的位置。 此位置是建立工作區的所在位置。

1. 檢閱工作區設定，並選取 [建立]  。 建立工作區可能需要一些時間。

1. 程序完成後，會出現部署成功訊息。 它也會出現在通知區段中。 若要檢視新的工作區，選取 [前往資源]  。

   ![工作區建立狀態](./media/aml-create-in-portal/notifications.png)
