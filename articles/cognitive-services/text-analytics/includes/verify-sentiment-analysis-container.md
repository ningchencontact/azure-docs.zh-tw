---
title: 確認情感分析的容器實例
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證情感分析容器實例。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229316"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>確認情感分析的容器實例

1. 選取 [**總覽**] 索引標籤, 然後複製 [IP 位址]。
1. 開啟新的瀏覽器索引標籤, 並輸入 IP 位址。 例如, 輸入`http://<IP-address>:5000 (http://55.55.55.55:5000`)。 容器的首頁隨即顯示, 讓您知道容器正在執行。

    ![查看容器首頁以確認它正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. 選取 [**服務 API 描述**] 連結, 以移至容器的 swagger 頁面。

1. 選擇任何**張貼**api, 然後選取 [立即**試用**]。系統會顯示參數, 包括此範例輸入:

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

1. 使用下列 JSON 內容來取代輸入:

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

1. 將**showStats**設定為 true。

1. 選取 [**執行**] 以決定文字的情感。

    封裝在容器中的模型會產生介於0到1之間的分數, 其中0是負數, 1 是正數。

    傳回的 JSON 回應包含更新文字輸入的情感:

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

我們現在可以將回應裝載`id`的 JSON 資料檔案與原始要求裝載檔`id`相互關聯。 我們看到的分數大於`.98`, 表示強烈的正面情感。