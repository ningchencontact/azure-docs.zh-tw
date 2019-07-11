---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解如何建立 Azure 容器執行個體資源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717183"
---
## <a name="create-an-azure-container-instance-resource"></a>建立 Azure 容器執行個體資源

1. 移至[建立](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)容器執行個體 頁面。

2. 在 **基本概念**索引標籤上，輸入下列詳細資料：

    |設定|值|
    |--|--|
    |訂用帳戶|選取您的訂用帳戶。|
    |資源群組|選取可用的資源群組，或建立一個新例如`cognitive-services`。|
    |容器名稱|輸入名稱，例如`cognitive-container-instance`。 名稱必須是在較低的大寫字。|
    |Location|選取部署的區域。|
    |映像類型|`Public`|
    |映像名稱|輸入認知服務容器位置。 此位置可以是用於相同`docker pull`命令，請參閱[容器儲存機制和映像](../../cognitive-services-container-support.md#container-repositories-and-images)可用映像名稱和其對應的儲存機制。|
    |OS 類型|`Linux`|
    |Size|變更大小的建議的建議，為您特定的認知服務容器：<br>2 個 CPU 核心<br>4 GB

3. 在 **網路**索引標籤上，輸入下列詳細資料：

    |設定|值|
    |--|--|
    |連接埠|若要設定的 TCP 連接埠`5000`。 會公開連接埠 5000 上的容器。|

4. 在上**進階**索引標籤上，輸入必要**環境變數**容器[計費設定](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments)的 ACI 資源：

    | Key | 值 |
    |--|--|
    |`apikey`|從複製**金鑰**Text Analytics 資源頁面。 它是 32 的英數字元字串，不含空格或連字號`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|從複製**概觀**Text Analytics 資源頁面。 範例： `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. 按一下 **檢閱並建立**
1. 通過驗證之後，請按一下**建立**完成建立程序
1. 已成功部署資源時，即準備