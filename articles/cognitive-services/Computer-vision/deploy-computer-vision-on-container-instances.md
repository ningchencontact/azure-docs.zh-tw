---
title: 執行 Azure 容器執行個體
titleSuffix: Azure Cognitive Services
description: 電腦視覺容器部署到 Azure 容器執行個體，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711914"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>電腦視覺容器部署到 Azure 容器執行個體

了解如何部署認知服務[Computer Vision](computer-vision-how-to-install-containers.md)至 Azure 的容器[Container Instances](https://docs.microsoft.com/azure/container-instances/)。 此程序示範如何建立電腦視覺資源。 然後我們會討論提取相關聯的容器映像。 最後，我們反白顯示能夠練習從瀏覽器兩個協調流程。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]