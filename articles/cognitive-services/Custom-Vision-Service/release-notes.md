---
title: 版本資訊 - 自訂視覺服務
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 2b0d8b8a86c3105b1bda7fb0d72cbcb72ed82995
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785720"
---
# <a name="custom-vision-service-release-notes"></a>自訂視覺服務版本資訊

## <a name="march-26-2019"></a>2019 年 3 月 26 日

- 在 Azure 上，自訂視覺服務已進入正式運作 ！
- 新增進階訓練新的機器學習來改善效能，特別是在上一項挑戰資料集和精確的分類的後端功能。 利用進階的訓練課程，您可以指定適用於定型和自訂的願景的計算時間預算會根據識別最佳的訓練和增強指定設定。 針對快速反覆項目，您可以繼續使用現有的快速定型。
- 導入 3.0 Api。 宣布即將取代前 3.0 Api 於 2019 年 10 月 1 日。 請參閱文件快速入門[.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial)， [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial)，[節點](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial)， [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)，或[移](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial)範例如何開始使用。
- 取代 [預設反覆項目] 與發佈/取消發佈 3.0 中的 Api。
- 已加入新的模型匯出目標。 Dockerfile 匯出已升級為支援 ARM 適用於 Raspberry Pi 3。 匯出支援已加入[願景 AI 開發套件](https://visionaidevkit.com/)。
- 限制提高為標記的每個專案到 500 S0 層。 限制提高為映像的每個專案為 S0 層的 100,000。
- 移除成人的網域。 建議改為一般的網域。
- 宣布[定價](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)正式運作。  

## <a name="february-25-2019"></a>2019 年 2 月 25日日

- 宣佈的有限的試用版專案 （專案不相關聯的 Azure 資源），結尾為自訂視覺接近完成其移至 Azure 的公開預覽狀態。 從開始 2019 年 3 月 25 日，CustomVision.ai 站台只支援與 Azure 資源，例如可用的自訂視覺資源相關聯的檢視專案。 透過 2019 年 10 月 1 日，您仍然可以存取現有有限試用版專案透過自訂視覺 Api。 這可讓您的時間更新為您撰寫自訂的願景與任何應用程式的 API 金鑰。 在 2019 年 10 月 1 日之後, 將會刪除任何限制的試用版專案，您還沒有移轉至 Azure。

## <a name="january-22-2019"></a>2019 年 1 月 22 日

- 對於新 Azure 區域新增的支援：美國西部 2、美國東部、美國東部 2、西歐、北歐、東南亞、澳洲東部、印度中部、英國南部、日本東部和美國中北部。 對於美國中南部繼續提供支援。

## <a name="december-12-2018"></a>2018 年 12 月 12 日

- 支援匯出物件偵測模型 (導入了物件偵測 Compact 網域)。
- 已修正一些協助工具問題，改善螢幕閱讀程式和鍵盤瀏覽支援。
- 影像檢視器的 UX 更新，改善物件偵測標記體驗，以便更快速標記。  
- 更新物件偵測網域的基底模型，以獲得更佳品質的物體偵測。
- Bug 修正。

## <a name="november-6-2018"></a>2018 年 11 月 6 日

- 新增物件偵測中標誌網域的支援。

## <a name="october-9-2018"></a>2018 年 10 月 9 日

- 物件偵測進入付費預覽。 您現在可以使用 Azure 資源建立物件偵測專案。
- 新增「移至 Azure」功能到網站，讓有限的試用版專案升級連結到 Azure 更輕鬆。 與資源連結的專案 (F0 或 S0)。您可以在您產品的 [設定] 頁面上找到這項功能。  
- 新增匯出至 ONNX 1.2 以支援 Windows ML 的 Windows 2018 十月更新版。
Bug 修正，包括 ONNX 匯出含有特殊字元。

## <a name="august-14-2018"></a>2018 年 8 月 14 日

- customvision.ai 網站已新增「開始使用」小工具，可引導使用者進行專案訓練。
- 機器學習服務管線的進一步增強，有利於多標籤專案 (新遺失層)。

## <a name="june-28-2018"></a>2018 年 6 月 28 日

- Bug 修正和後端的改進。
- 啟用的映像，有一個標籤的專案的多類別分類。 在預測多級的模式，機率會加總至其中一個 （所有映像分類在您指定的標記之間）。

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

- 付費的 preview 及上的架到 Azure 入口網站上輸入。 專案現在可以連結至 F0 (免費) 或 S0 (標準) 層的 Azure 資源。 導入了 S0 層專案，允許使用多達 100 個標記和 25,000 個影像。
- 後端變更為機器學習管線/正規化參數。 這會讓客戶在調整機率閾值時，對準確率與召回率有更好的取捨控制能力。 在進行這些變更的過程中，CustomVision.ai 入口網站中的預設機率閾值設為 50%。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 除了先前發行的匯出至 iOS (CoreML) 外，已新增匯出至 Android (TensorFlow)。這可讓經過訓練的精簡模型匯出功能，在應用程式中離線執行。
- 已新增零售和地標「精簡」網域，以啟用這些網域的模型匯出功能。
- 已發行 [1.2 版訓練 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 版預測 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支援模型匯出、不會將影像儲存至「預測」的新預測作業，並為訓練 API 引進了批次作業。
- UX 調整，包括可查看使用了哪些網域來訓練反覆項目。
- 已更新 [C# SDK 和範例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。