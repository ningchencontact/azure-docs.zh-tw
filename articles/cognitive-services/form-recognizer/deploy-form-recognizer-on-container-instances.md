---
title: 執行 Azure 容器執行個體
titleSuffix: Azure Cognitive Services
description: 表單的辨識器容器部署到 Azure 容器執行個體，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3c424465678a9989940d92910c5d288fa2fb1cab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711874"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>將表單的辨識器容器部署至 Azure Container Instances

了解如何部署認知服務[形式辨識器](form-recognizer-container-howto.md)至 Azure 的容器[Container Instances](https://docs.microsoft.com/azure/container-instances/)。 此程序示範如何為 Azure 形式辨識器資源的建立。 然後我們會討論提取相關聯的容器映像。 最後，我們反白顯示能夠練習從瀏覽器兩個協調流程。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>要求存取容器登錄

您必須先完成與提交[認知服務形式辨識器容器存取要求表單](https://aka.ms/FormRecognizerRequestAccess)要求存取至容器。 這樣也會將您註冊電腦視覺。 您不需要個別登入電腦視覺要求表單。 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
