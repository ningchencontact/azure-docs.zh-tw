---
title: 執行 Azure 容器執行個體
titleSuffix: Text Analytics - Azure Cognitive Services
description: 文字分析將容器部署與情感分析映像到 Azure 容器執行個體，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446449"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>將情感分析容器部署至 Azure Container Instances (ACI)

了解如何部署認知服務[文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)情感分析映像至 Azure 容器[Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI)。 建立文字分析的資源，建立相關聯的情感分析映像，以及能夠練習從瀏覽器兩個此協調流程，舉例說明此程序。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

## <a name="prerequisites"></a>必要條件

* 使用 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>後續步驟 

* 使用更多[認知服務容器](../../cognitive-services-container-support.md)
* 使用[文字分析已連線服務](../vs-text-connected-service.md)
