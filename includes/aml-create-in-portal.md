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
ms.date: 07/21/2019
ms.openlocfilehash: 51bd3dfb33b1f445db8672e1b987ee6c6242e09c
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370949"
---
1. 使用您所使用之 Azure 訂用帳戶的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

1. 在入口網站的左上角，選取 [建立資源]  。

   ![在 Azure 入口網站中建立資源](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. 使用搜尋列尋找 [Machine Learning 服務工作區]  。

1. 選取 [Machine Learning 服務工作區]  。

1. 在 [ML 服務工作區]  窗格中選取 [建立]  來開始操作。

1. 藉由提供工作區名稱、訂用帳戶、資源群組和位置來設定新的工作區。

    ![建立工作區](./media/aml-create-in-portal/workspace-create-main-tab.png)

   欄位|說明
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。  
   Subscription |選取您要使用的 Azure 訂用帳戶。
   Resource group | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 
   Location | 選取最接近您的使用者與資料資源的位置，以建立工作區。

1. 在完成工作區的設定後，選取 [建立]  。 

   建立工作區可能需要一些時間。

   程序完成後，會出現部署成功訊息。 它也會出現在通知區段中。 若要檢視新的工作區，選取 [前往資源]  。

   ![工作區建立狀態](./media/aml-create-in-portal/notifications.png)
