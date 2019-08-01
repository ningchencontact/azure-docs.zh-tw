---
title: 執行 Azure 容器實例-文字分析
titleSuffix: Azure Cognitive Services
description: 將具有情感分析映射的文字分析容器部署至 Azure 容器實例, 並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552535"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>將情感分析容器部署至 Azure 容器實例

瞭解如何使用情感分析映射將認知服務[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)容器部署至 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式會」舉例說明建立文字分析資源、建立相關聯的情感分析映射, 以及從瀏覽器執行這兩項協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外, 改為專注于應用程式開發。

## <a name="prerequisites"></a>先決條件

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>後續步驟 

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)
* 使用[文字分析聯機服務](../vs-text-connected-service.md)
