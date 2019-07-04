---
title: 容器支援
titleSuffix: Azure Cognitive Services
description: 了解如何驗證情感分析容器執行個體。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455079"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>確認情感分析容器執行個體

1. 選取 **概觀**索引標籤，然後將複製的 IP 位址。
1. 開啟新的瀏覽器索引標籤，並使用的 IP 位址，例如`http://<IP-address>:5000 (http://55.55.55.55:5000`)。 容器的 [首頁] 頁面出現時，讓您知道容器正在執行。

    ![檢視容器首頁上確認它正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選取 **服務的 API 描述**瀏覽至容器的 swagger 頁面的連結。

1. 選擇任一**POST** Api，然後選取**試試看**。參數就會顯示包括範例輸入：

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. 取代為下列 JSON 中的輸入：

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. 設定**showStats**設為 true。

1. 選取  **Execute**來判斷文字的情感。

    封裝在容器中的模型產生分數，範圍從 0 到 1，0 為負數，1 是正數。

    傳回的 JSON 回應會包含更新輸入的文字的情緒：

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

我們現在可以將文件`id`的原始要求裝載文件的回應裝載 JSON `id`，並查看發生的分數是透過`.98`表示非常正面的情感。