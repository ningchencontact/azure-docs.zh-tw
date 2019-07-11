---
title: 執行 Azure 容器執行個體
titleSuffix: Azure Cognitive Services
description: 臉部容器部署到 Azure 容器執行個體，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: e67846b6b304b5425f7e8334eb3a4499a029d5ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711910"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>將臉部容器部署至 Azure Container Instances

了解如何部署認知服務[臉部](../face-how-to-install-containers.md)至 Azure 的容器[Container Instances](https://docs.microsoft.com/azure/container-instances/)。 此程序示範如何建立 Azure 臉部資源。 然後我們會討論提取相關聯的容器映像。 最後，我們反白顯示能夠練習從瀏覽器兩個協調流程。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>要求私人容器登錄的存取

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]