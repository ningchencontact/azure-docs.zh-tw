---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210174"
---
1. 登入 [Azure 入口網站][Azure portal]。
1. 選取 [建立資源]。 然後，選取 [整合] > [轉送]。 如果清單中未顯示 [轉送]，請選取右上角的 [查看全部]。
1. 選取 [**建立**]，然後在 [**名稱**] 欄位中輸入命名空間名稱。 Azure 入口網站檢查是否有該名稱可用。
1. 選擇要在其中建立命名空間的 Azure 訂用帳戶。
1. 針對 [[資源群組](../articles/azure-resource-manager/manage-resource-groups-portal.md)]，選擇要在其中放置命名空間的現有資源群組，或建立一個新的。  
1. 選取您的命名空間應裝載所在的國家或地區。

    ![建立命名空間][create-namespace]

1. 選取 [建立]。 Azure 入口網站會建立您的命名空間並加以啟用。 幾分鐘後，系統便會為您的帳戶佈建資源。

### <a name="get-management-credentials"></a>取得管理認證

1. 選取 [**所有資源**]，然後選擇新建立的命名空間名稱。
1. 選取 [共用存取原則]。  
1. 在 [共用存取原則] 下，選取 [RootManageSharedAccessKey]。
1. 在 **[SAS 原則] 底下：RootManageSharedAccessKey**中，選取 [**主要連接字串**] 旁邊的 [**複製**] 按鈕。 此動作會將連接字串複製到您的剪貼簿，以供稍後使用。 將此值貼到記事本或一些其他暫存位置。
1. 重複前一個步驟，複製 [主要金鑰] 的值並貼到暫存位置以供稍後使用。  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
