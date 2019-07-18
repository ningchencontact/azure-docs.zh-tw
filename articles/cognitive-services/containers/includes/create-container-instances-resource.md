---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 瞭解如何建立 Azure 容器實例資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229300"
---
## <a name="create-an-azure-container-instance-resource"></a>建立 Azure 容器實例資源

1. 移至容器實例的 [[建立](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)] 頁面。

2. 在 [**基本**] 索引標籤上, 輸入下列詳細資料:

    |設定|值|
    |--|--|
    |Subscription|選取您的訂用帳戶。|
    |Resource group|選取可用的資源群組, 或建立一個新的, `cognitive-services`例如。|
    |容器名稱|輸入名稱, 例如`cognitive-container-instance`。 名稱必須是小寫。|
    |Location|選取要部署的區域。|
    |映像類型|`Public`|
    |映像名稱|輸入認知服務容器位置。 此位置可與`docker pull`命令中所使用的相同, 請參閱[容器存放庫和映射](../../cognitive-services-container-support.md#container-repositories-and-images), 以取得可用的映射名稱及其對應的儲存機制。|
    |OS 類型|`Linux`|
    |Size|針對您特定的認知服務容器, 將大小變更為建議的建議:<br>2個 CPU 核心<br>4 GB

3. 在 [**網路**功能] 索引標籤上, 輸入下列詳細資料:

    |設定|值|
    |--|--|
    |連接埠|將 TCP 埠設定為`5000`。 公開端口5000上的容器。|

4. 在 [ **Advanced** ] 索引標籤上, 輸入 ACI 資源的容器帳單設定所需的**環境變數**:

    | Key | 值 |
    |--|--|
    |`apikey`|從資源的 [**金鑰**] 頁面複製。 它是32英數位元字串, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`不含空格或連字號。|
    |`billing`|從資源的 [**總覽**] 頁面複製的。|
    |`eula`|`accept`|

1. 按一下 [**審查並建立**]
1. 通過驗證之後, 按一下 [**建立**] 以完成建立程式
1. 當資源部署成功時, 即已準備就緒