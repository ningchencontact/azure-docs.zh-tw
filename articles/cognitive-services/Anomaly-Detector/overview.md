---
title: 什麼是異常偵測器 API？ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 您可以使用異常偵測器 API 的進階的演算法來識別時間序列資料中的異常狀況。
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "64415811"
---
# <a name="what-is-the-anomaly-detector-api"></a>什麼是異常偵測器 API？

異常偵測器 API 可讓您監視和偵測時間序列資料使用 machine learning 中的異常。 異常偵測器 API 會適應自動識別，並將最適合的模型套用至您的資料，不論產業、 案例中或資料磁碟區。 使用時間序列資料，API 會決定界限進行異常偵測，預期的值，和哪些資料點彼此異常狀況。

![偵測服務要求中的模式變更](./media/anomaly_detection2.png)

使用異常偵測器並不需要任何先前的經驗，在機器學習服務，和 RESTful API 可讓您輕鬆地將服務整合到您的應用程式和處理程序。

## <a name="features"></a>功能

利用異常偵測器中，您可以自動偵測到異常整個時間序列資料，或即時發生。 

|功能  |說明  |
|---------|---------|
|即時發生，請偵測異常狀況。 | 偵測異常的資料流中，使用先前看到的資料點，以判斷您的最新一個是否異常狀況。 這項作業會產生模型，使用您傳送，並判斷目標點是否異常資料點。 藉由呼叫與每個新的資料點，您所產生的 API，您可以在建立監視您的資料。 |
|偵測異常狀況，在您的資料集做為批次。 | 您可以使用時間序列偵測在您的資料可能會有任何異常狀況。 這項作業會產生整個時間序列資料，使用相同的模型分析的每個點的模型。         |
| 取得您的資料相關的其他資訊。 | 取得您的資料和任何觀察到的異常狀況，包括預期的值、 異常界限和位置的有用詳細資料。 |
| 調整異常偵測界限。 | 異常偵測器 API 會自動建立界限進行異常偵測。 調整以增加或減少 API 的敏感性資料異常狀況，這些界限，更能符合您的資料。 |

## <a name="demo"></a>示範

若要快速開始使用異常偵測器 API，請嘗試[線上示範](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)，可以在您的瀏覽器中執行。 這段示範影片在 web 裝載的 Jupyter notebook 中執行，並示範如何傳送 API 要求，並以視覺化方式檢視結果。

若要執行的示範，請完成下列步驟：

1. 取得有效的異常偵測器 API 訂用帳戶金鑰和 API 端點。 下一節中的註冊的指示。 
2. 登入，然後按一下右上角的 再製。
3. 按一下 **免費計算上執行**
4. 選取其中一個針對此範例的 notebook。
5. 將有效的異常偵測器 API 訂用帳戶金鑰，加入`subscription_key`變數。 變更`endpoint`變數設為您的端點。 例如：`https://westus2.api.cognitive.microsoft.com`
1. 在頂端功能表列中，按一下**儲存格**，然後**全部執行**。

## <a name="workflow"></a>工作流程

異常偵測器 API 是 RESTful web 服務，輕鬆地從任何程式設計語言，可以進行 HTTP 要求，並剖析 JSON 呼叫。

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

在註冊後：

1. 採用時間序列資料，並將它轉換成有效的 JSON 格式。 使用[最佳做法](concepts/anomaly-detection-best-practices.md)準備您的資料，以取得最佳的結果時。
1. 傳送要求給異常偵測器 API，與您的資料。
1. 剖析傳回的 JSON 訊息以處理 API 回應。

## <a name="next-steps"></a>後續步驟

* [快速入門：偵測異常的時間序列資料使用異常偵測器 REST API](quickstarts/detect-data-anomalies-csharp.md)
* 異常偵測器 API[線上示範](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* 異常偵測器[REST API 參考](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)