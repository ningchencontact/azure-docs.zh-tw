---
title: 自訂視覺服務機器學習概觀 - Azure 認知服務 | Microsoft Docs
description: 自訂視覺服務是一項 Microsoft 認知服務，可讓您在 Azure 平台上建置自訂影像分類器。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087516"
---
# <a name="what-is-the-custom-vision-service"></a>什麼是自訂視覺服務？

自訂視覺服務是一項 Microsoft 認知服務，可讓您建置自訂影像分類器。 它可讓您輕鬆快速地建置、部署及改善影像分類器。 自訂視覺服務提供了 REST API 和 Web 介面供您上傳影像和訓練分類器。

## <a name="what-does-custom-vision-service-do-well"></a>自訂視覺服務有何功用？

當嘗試要分類的項目在影像中很顯著時，最適合使用自訂視覺服務。 

需要有幾個映像才能建立分類器或偵測器。 每個類別只要有 50 個影像就足以開始建立原型。 自訂視覺服務不容易受到差異所影響，所以只要一點點資料就可以開始建立原型。 這表示自訂視覺服務並不適合想要偵測些微差異的案例。 例如，品質保證案例中的輕微裂縫或凹痕。

## <a name="release-notes"></a>版本資訊

### <a name="may-7-2018"></a>2018 年 5 月 7 日
- 導入了有限試用專案的預覽物體偵測功能。
- 升級為 2.0 API
- S0 層已擴充為多達 250 個標記和 50,000 個影像。 
- 影像分類專案的機器學習管線已進行重大的後端功能改進。 在 2018 年 4 月 27 日之後訓練的專案會受益於這些更新。
- 新增了目的地為 ONNX 的模型匯出功能，以用於 Windows ML。
- 新增了目的地為 Dockerfile 的模型匯出功能。 這可讓您下載成品來建置您自己的 Windows 或 Linux 容器，包括 DockerFile、TensorFlow 模型和服務程式碼。 
- 對於新訓練出來的模型，如果其匯出至一般 (精簡) 和地標 (精簡) 網域中的 TensorFlow，則[平均值現在為 (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)，以便讓所有專案保持一致。 

### <a name="march-1-2018"></a>2018 年 3 月 1 日
- 輸入了付費預覽並上架到 Azure 入口網站。 專案現在可以連結至 F0 (免費) 或 S0 (標準) 層的 Azure 資源。 導入了 S0 層專案，允許使用多達 100 個標記和 25,000 個影像。 
- 後端變更為機器學習管線/正規化參數。 這會讓客戶在調整機率閾值時，對準確率與召回率有更好的取捨控制能力。 在進行這些變更的過程中，CustomVision.ai 入口網站中的預設機率閾值設為 50%。

### <a name="december-19-2017"></a>2017 年 12 月 19 日

- 除了先前發行的匯出至 iOS (CoreML) 外，已新增匯出至 Android (TensorFlow)。這可讓經過訓練的精簡模型匯出功能，在應用程式中離線執行。
- 已新增零售和地標「精簡」網域，以啟用這些網域的模型匯出功能。
- 已發行 [1.2 版訓練 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 版預測 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支援模型匯出、不會將影像儲存至「預測」的新預測作業，並為訓練 API 引進了批次作業。
- UX 調整，包括可查看使用了哪些網域來訓練反覆項目。
- 已更新 [C# SDK 和範例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。

## <a name="next-steps"></a>後續步驟

[了解如何建置分類器](getting-started-build-a-classifier.md)
