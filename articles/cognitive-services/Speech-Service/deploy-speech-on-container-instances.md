---
title: 執行 Azure 容器實例-語音服務
titleSuffix: Azure Cognitive Services
description: 將語音服務容器部署至 Azure 容器實例，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 690a45ad7f009a65686e27eba5095a5a2870039c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806264"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>將語音服務容器部署至 Azure 容器實例

瞭解如何將認知服務[語音服務](speech-container-howto.md)容器部署至 Azure[容器實例](https://docs.microsoft.com/azure/container-instances/)。 此程式示範如何建立 Azure 語音服務資源。 然後，我們會討論如何提取相關聯的容器映射。 最後，我們強調了從瀏覽器執行這兩個協調流程的能力。 使用容器可以將開發人員的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成並提交[認知服務的語音容器要求表單](https://aka.ms/speechcontainerspreview/)，以要求容器的存取權。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
