---
title: 自訂視覺 ONNX 模型與 Windows ML - 認知服務 | Microsoft Docs
description: 了解如何建立 Windows UWP 應用程式來使用從認知服務匯出的 ONNX 模型。
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939403"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>教學課程：搭配使用自訂視覺中的 ONNX 模型與 Windows ML (預覽)

了解如何搭配使用從自訂視覺匯出的 ONNX 模型與 Windows ML (預覽)。

本文件中的資訊會示範如何搭配使用從自訂視覺服務匯出的 ONNX 檔案與 Windows ML。 我們會提供 Windows UWP 的範例應用程式。 範例中會包含可辨識狗和貓的已訓練模型。 也會提供如何使用自有模型來搭配此範例的步驟。

> [!div class="checklist"]
> * 關於範例應用程式
> * 取得範例程式碼
> * 執行範例
> * 使用自有模型

## <a name="prerequisites"></a>先決條件

* 具備以下項目的 Windows 10 裝置：

    * 相機。

    * 啟用__通用 Windows 平台開發__工作負載的 Visual Studio 2017 15.7 版或更新版本。

    * 已啟用開發人員模式。 如需詳細資訊，請參閱[啟用您的裝置以用於開發](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)文件。

* (選擇性) 從自訂視覺服務匯出的 ONNX 檔案。 如需詳細資訊，請參閱[匯出您的模型以用於行動裝置](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)文件。

    > [!NOTE]
    > 若要使用您自己的模型，請遵循[使用自有模型](#use-your-own-model)一節中的步驟。

## <a name="about-the-example-app"></a>關於範例應用程式

應用程式是一般的 Windows UWP 應用程式。 它會使用 Windows 10 裝置上的相機來將影像提供給模型。 模型傳回的標籤和分數會顯示在影片預覽下方。

* 當資料從相機傳入時，[MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) 會用來擷取個別畫面。 畫面會傳送至模型以進行計分。

* 模型會傳回其訓練時所依據的標籤，以及指出影像有多大機率會包含該項目的浮點值。

### <a name="the-ui"></a>UI

範例應用程式的 UI 會使用 __CaptureElement__ 和 __TextBlock__ 控制項來建立。 CaptureElement 會顯示相機中影片的預覽，而 TextBlock 會顯示從模型傳回的結果。 

### <a name="the-model"></a>模型

範例提供的模型 (`cat-or-dog.onnx`) 會使用認知服務的自訂視覺服務來加以建立和訓練。 接著，已訓練的模型會匯出為 ONNX 模型。 如需有關使用此服務的詳細資訊，請參閱[如何建置分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)和[匯出您的模型以用於行動裝置](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)文件。

> [!IMPORTANT]
> 我們會使用一小部分狗和貓的影像來訓練此範例提供的模型。 因此，這可能不是辨識狗和貓的最佳模型。

### <a name="the-model-class-file"></a>模型類別檔案

當您將 ONNX 檔案新增至 Windows UWP 應用程式時，它會建立 .cs 檔案。 此檔案會與 `.onnx` 檔案具有相同名稱 (在此範例中為 `cat-or-dog`)，並且包含用來從 C# 使用模型的類別。 不過，在所產生類別中的實體，其名稱可能會類似 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`。 您可以放心地將這些項目重新命名為好記的名稱 (以滑鼠右鍵按一下即可重新命名)。

> [!NOTE]
> 範例程式碼已重構產生的類別和方法名稱，如下所示：
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>相機存取

`Package.appxmanifest` 檔案中的 __[功能]__ 索引標籤會設定為允許存取網路攝影機和麥克風。

> [!NOTE]
> 雖然此範例不使用音訊，但我必須啟用麥克風，才能存取我裝置上的相機。

應用程式會嘗試取得您裝置背面的相機 (如果有的話)。 它會使用 [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) 類別從相機啟動影片拍攝。 [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) 會用來擷取影片畫面，並將其傳送至模型。

## <a name="get-the-example-code"></a>取得範例程式碼

範例應用程式可在 [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP) 取得。

## <a name="run-the-example"></a>執行範例

1. 使用 `F5` 鍵從 Visual Studio 中啟動應用程式。 系統可能會提示您啟用開發人員模式。 如需詳細資訊，請參閱[啟用您的裝置以用於開發](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)文件。

2. 出現提示時，請允許應用程式存取您裝置上的相機和麥克風。

3. 將相機指向狗或貓。 影像中是否包含狗或貓的計分會顯示在應用程式中的預覽下方。

    > [!TIP]
    > 如果您附近沒有狗或貓，您可以使用狗或貓的相片。

## <a name="use-your-own-model"></a>使用自有模型

若要使用您自己的模型，請使用下列步驟：

> [!IMPORTANT]
> 本節中的步驟會重新命名目前的模型 (cat-or-dog.cs)，並且重構新模型的類別和方法名稱。 這是為了避免與範例模型發生命名衝突。

1. 使用自訂視覺服務訓練模型。 如需如何訓練模型的資訊，請參閱[如何建置分類器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)。

2. 將已訓練的模型匯出為 ONNX 模型。 如需如何匯出模型的資訊，請參閱[匯出您的模型以用於行動裝置](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)文件。

3. 在 [方案總管] 中，以滑鼠右鍵按一下 __cat-or-dog.cs__，並將它重新命名為 __cat-or-dog.txt__。 將其重新命名可避免與新模型發生名稱衝突。

    > [!TIP]
    > 您也可以針對新模型中的類別名稱使用不同名稱，但重複使用現有名稱比較簡單。

4. 在 [方案總管] 中，以滑鼠右鍵按一下 __VisionApp__ 項目，然後選取 [新增] > [現有項目...]。

5. 若要產生模型的類別，請選取要匯入的 ONNX 檔案，然後選取 [新增] 按鈕。 與 ONNX 檔案同名的新類別 (但副檔名為 `.cs`) 會新增至 [方案總管] 中。

6. 開啟產生的 .cs 檔案，並尋找下列項目的名稱：

    > [!IMPORTANT]
    > 使用 `cat-or-dog.txt` 檔案範例作為識別類別和函式的指南。

    * 定義模型輸入的類別。 產生的名稱可能類似 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`。 將此類別重新命名為 __ModelInput__。
    * 定義模型輸出的類別。 產生的名稱可能類似 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`。 將此類別重新命名為 __ModelOutput__。
    * 定義模型的類別。 產生的名稱可能類似 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`。 將此類別重新命名為 __Model__。
    * 建立模型的方法。 產生的名稱可能類似 `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`。 將此方法重新命名為 __CreateModel__。

7. 在 [方案總管] 中，將 `.onnx` 檔案移至 __Assets__ 資料夾。 

8. 若要在應用程式套件中包含 ONNX 檔案，請選取 `.onnx` 檔案，並將屬性中的 [建置動作]設定為 [內容]。

9. 開啟 __MainPage.xaml.cs__ 檔案。 尋找下列這行，並將檔案名稱變更為新的 `.onnx` 檔案：

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    這項變更會在執行階段載入新的模型。

10. 建置並執行應用程式。 應用程式現在會使用新的模型來對影像進行計分。

## <a name="next-steps"></a>後續步驟

若要探索其他匯出及使用自訂視覺模型的方法，請參閱下列文件：

* [匯出您的模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [在 Android 應用程式中使用匯出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [在 Swift iOS 應用程式中使用匯出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)
* [搭配使用 iOS 應用程式中的 CoreML 模型與 Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

如需有關如何搭配使用 ONNX 模型與 Windows ML 的詳細資訊，請參閱[使用 Windows ML 將模型整合到您的應用程式中](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model)文件。
