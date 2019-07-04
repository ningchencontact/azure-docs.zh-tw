---
title: Azure 容器執行個體配方
titleSuffix: Azure Cognitive Services
description: 了解如何部署 Azure 容器執行個體上的認知服務容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 45a03a0912681b4fc33ef8df88fa00fd5458f720
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445823"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>部署和執行容器 Azure 容器執行個體 (ACI)

使用下列步驟中，調整在雲端中輕鬆地使用 Azure 的 Azure 認知服務應用程式[容器執行個體](https://docs.microsoft.com/azure/container-instances/)(ACI)。 本文協助您專注於建置您的應用程式，而不是管理基礎結構中。

## <a name="prerequisites"></a>必要條件

此解決方案適用於任何認知服務容器。 認知服務資源必須建立在 Azure 入口網站，才能使用此配方。 每個支援容器的認知服務有特別針對安裝和設定容器服務的 < 如何安裝 > 文件。 因為某些服務需要檔案或一組檔案，做為容器的輸入時，很重要，您會了解，並以前用過容器已成功使用此解決方案。

* 在 Azure 入口網站中建立認知服務資源。
* 認知服務**端點 URL** -檢閱您特定服務的 「 如何安裝 」 容器的內容，若要尋找其中的端點 URL 是從 Azure 入口網站中，以及正確的範例，該 url 看起來像。 確切的格式可以變更的服務。
* 認知服務**金鑰**-機碼位於**金鑰**Azure 資源的頁面。 您只需要兩個金鑰中的其中一個。 此金鑰是 32 個英數字元字串。
* 單一認知服務上的容器本機主機 （您的電腦）。 請確定您可以：
  * 提取映像`docker pull`命令。
  * 使用具有所有必要的組態設定已成功執行本機容器`docker run`命令。
  * 呼叫容器的端點，取回 2xx 回應和 JSON 回應。

角括弧中的所有變數`<>`，需要換成您自己的值。 這項取代包含角括號。

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](./includes/create-aci-resource.md)]

## <a name="use-the-container-instance"></a>使用容器執行個體

1. 選取 **概觀**並複製 IP 位址。 它會是數字的 IP 位址，例如`55.55.55.55`。
1. 開啟新的瀏覽器索引標籤，並使用的 IP 位址，例如`http://<IP-address>:5000 (http://55.55.55.55:5000`)。 您會看到容器的首頁上，讓您知道容器正在執行。

1. 選取 **服務的 API 描述**來檢視容器的 swagger 頁面。

1. 選取任一**POST** Api，然後選取**試試看**。參數會顯示包括輸入。 填入的參數。

1. 選取  **Execute**將要求傳送到您的容器執行個體。

    您已成功建立和 Azure 容器執行個體中使用認知服務容器。
