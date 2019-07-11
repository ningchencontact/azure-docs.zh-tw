---
title: 執行 Azure 容器執行個體
titleSuffix: Azure Cognitive Services
description: LUIS 容器部署到 Azure 容器執行個體，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711906"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Language Understanding (LUIS) 容器部署到 Azure 容器執行個體

了解如何部署認知服務[LUIS](luis-container-howto.md)至 Azure 的容器[Container Instances](https://docs.microsoft.com/azure/container-instances/)。 此程序示範如何建立異常偵測器資源。 然後我們會討論提取相關聯的容器映像。 最後，我們反白顯示能夠練習從瀏覽器兩個協調流程。 使用容器可以轉移遠離管理基礎結構，以改為專注於應用程式開發的開發人員的注意。

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
