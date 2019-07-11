---
title: 執行 Azure 容器執行個體
titleSuffix: Azure Cognitive Services
description: 語音服務容器部署到 Azure 容器執行個體，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711896"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>將語音服務容器部署至 Azure Container Instances

了解如何部署認知服務[Speech Service](speech-container-howto.md)至 Azure 的容器[Container Instances](https://docs.microsoft.com/azure/container-instances/)。 此程序示範如何建立 Azure 語音服務資源。 然後我們會討論提取相關聯的容器映像。 最後，我們反白顯示能夠練習從瀏覽器兩個協調流程。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成與提交[認知服務語音容器要求表單](https://aka.ms/speechcontainerspreview/)要求存取至容器。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
