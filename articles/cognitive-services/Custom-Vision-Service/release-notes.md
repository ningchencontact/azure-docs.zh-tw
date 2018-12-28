---
title: 版本資訊 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: a4b6b1d267e9c86a03644129ee639b74ed214d88
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408352"
---
# <a name="custom-vision-service-release-notes"></a>自訂視覺服務版本資訊


## <a name="december-12-2018"></a>2018 年 12 月 12 日
- 支援匯出物件偵測模型 (導入了物件偵測 Compact 網域)。
- 已修正一些協助工具問題，改善螢幕閱讀程式和鍵盤瀏覽支援。 
- 影像檢視器的 UX 更新，改善物件偵測標記體驗，以便更快速標記。  
- 更新物件偵測網域的基底模型，以獲得更佳品質的物體偵測。 
- 錯誤修正。

## <a name="november-6-2018"></a>2018 年 11 月 6 日
- 新增物件偵測中標誌網域的支援。

## <a name="october-9-2018"></a>2018 年 10 月 9 日
- 物件偵測進入付費預覽。 您現在可以使用 Azure 資源建立物件偵測專案。
- 新增「移至 Azure」功能到網站，讓有限的試用版專案升級連結到 Azure 更輕鬆。 與資源連結的專案 (F0 或 S0)。您可以在您產品的 [設定] 頁面上找到這項功能。  
- 新增匯出至 ONNX 1.2 以支援 Windows ML 的 Windows 2018 十月更新版。
錯誤修正，包括含有特殊字元的 ONNX 匯出。 

## <a name="august-14-2018"></a>2018 年 8 月 14 日
- customvision.ai 網站已新增「開始使用」小工具，可引導使用者進行專案訓練。 
- 機器學習服務管線的進一步增強，有利於多標籤專案 (新遺失層)。

## <a name="june-28-2018"></a>2018 年 6 月 28 日
- 錯誤修正與後端的增強。
- 對於映像只有一個標籤的專案，已啟用多元分類。 在多元模式的預測中，機率會加總為 1 (所有映像均歸類於您指定的標籤)。

## <a name="june-13-2018"></a>2018 年 6 月 13 日
- UX 重新整理，著重於使用和協助工具的便利性。 
- 機器學習管線的步增強，有利於有許多標籤的多標籤專案。
- 修正在 TensorFlow 匯出的錯誤。 啟用匯出模型版本設定，以便可多次匯出反覆項目。 

## <a name="may-7-2018"></a>2018 年 5 月 7 日
- 導入了有限試用專案的預覽物體偵測功能。
- 升級為 2.0 API
- S0 層已擴充為多達 250 個標記和 50,000 個影像。 
- 影像分類專案的機器學習管線已進行重大的後端功能改進。 在 2018 年 4 月 27 日之後訓練的專案會受益於這些更新。
- 新增了目的地為 ONNX 的模型匯出功能，以用於 Windows ML。
- 新增了目的地為 Dockerfile 的模型匯出功能。 這可讓您下載成品來建置您自己的 Windows 或 Linux 容器，包括 DockerFile、TensorFlow 模型和服務程式碼。 
- 對於新訓練出來的模型，如果其匯出至一般 (精簡) 和地標 (精簡) 網域中的 TensorFlow，則[平均值現在為 (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)，以便讓所有專案保持一致。 

## <a name="march-1-2018"></a>2018 年 3 月 1 日
- 輸入了付費預覽並上架到 Azure 入口網站。 專案現在可以連結至 F0 (免費) 或 S0 (標準) 層的 Azure 資源。 導入了 S0 層專案，允許使用多達 100 個標記和 25,000 個影像。 
- 後端變更為機器學習管線/正規化參數。 這會讓客戶在調整機率閾值時，對準確率與召回率有更好的取捨控制能力。 在進行這些變更的過程中，CustomVision.ai 入口網站中的預設機率閾值設為 50%。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 除了先前發行的匯出至 iOS (CoreML) 外，已新增匯出至 Android (TensorFlow)。這可讓經過訓練的精簡模型匯出功能，在應用程式中離線執行。
- 已新增零售和地標「精簡」網域，以啟用這些網域的模型匯出功能。
- 已發行 [1.2 版訓練 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 版預測 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支援模型匯出、不會將影像儲存至「預測」的新預測作業，並為訓練 API 引進了批次作業。
- UX 調整，包括可查看使用了哪些網域來訓練反覆項目。
- 已更新 [C# SDK 和範例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。

