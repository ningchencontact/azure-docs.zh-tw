---
title: 快速入門：使用適用於 Go 的自訂視覺 SDK 建立影像分類專案
titlesuffix: Azure Cognitive Services
description: 建立專案、新增標籤、上傳影像、為您的專案定型，以及使用 Go SDK 進行預測。
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 2/25/2018
ms.author: areddish
ms.openlocfilehash: 9a45cc3f8aaae3fb000858f8903ed4aff248513c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885266"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>快速入門：使用自訂視覺 Go SDK 建立影像分類專案

本文提供資訊和範例程式碼，可協助您開始使用自訂視覺 SDK 與 Go 來建置影像分類模型。 建立它之後，您就可以新增標記、上傳影像、為專案定型、取得專案的預設預測端點 URL，並使用端點以程式設計方式測試影像。 請使用此範例作為範本來建置您自己的 Go 應用程式。 如果您想要進行「不用」程式碼來建置及使用分類模型的程序，請改為參閱[以瀏覽器為基礎的指引](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>必要條件

- [Go 1.8+](https://golang.org/doc/install)

## <a name="install-the-custom-vision-sdk"></a>安裝自訂視覺 SDK

若要安裝適用於 Go 的自訂視覺服務 SDK，請在 PowerShell 中執行下列命令：

```
go get -u github.com/Azure/azure-sdk-for-go/...
```

或者，如果您使用 dep，請在存放庫中執行：
```
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>新增程式碼

在您偏好的專案目錄中建立名為 sample.go 的新檔案。

### <a name="create-the-custom-vision-service-project"></a>建立自訂視覺服務專案

在指令碼中新增下列程式碼，以建立新的自訂視覺服務專案。 在適當的定義中插入訂用帳戶金鑰。

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.2/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample Project"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>在專案中建立標記

若要在專案中建立分類標記，請在 sample.go 結尾新增以下程式碼：

```go
    # Make two tags in the new project
    hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
    cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>上傳和標記影像

若要將範例影像新增到專案，在標記建立之後插入下列程式碼。 此程式碼會上傳每個影像及其對應標記。 您必須根據認知服務 Go SDK 範例專案的下載位置，來輸入基底影像 URL 路徑。

> [!NOTE]
> 您必須根據認知服務 Go SDK 範例專案稍早的下載位置，來變更影像的路徑。

```go
    fmt.Println("Adding images...")
    japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    for _, file := range hemLockImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
    }

    for _, file := range japaneseCherryImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
    }
```

### <a name="train-the-classifier"></a>為分類器定型

此程式碼會在專案中建立第一個反覆項目，並將其設定為預設反覆項目。 預設反覆項目會反映將會回應預測要求的模型版本。 每次重新訓練模型時，均應更新此反覆項目。

```go
    fmt.Println("Training...")
    iteration, _ := trainer.TrainProject(ctx, *project.ID)
    for {
        if *iteration.Status != "Training" {
            break
        }
        fmt.Println("Training status: " + *iteration.Status)
        time.Sleep(1 * time.Second)
        iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
    }
    fmt.Println("Training status: " + *iteration.Status)

    *iteration.IsDefault = true
    trainer.UpdateIteration(ctx, *project.ID, *iteration.ID, iteration)
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>取得並使用預設預測端點

若要將影像傳送到預測端點並擷取預測，在檔案結尾處新增以下程式碼：

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.PredictImage(ctx, *project.ID, ioutil.NopCloser(bytes.NewReader(testImageData)), iteration.ID, "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>執行應用程式

執行 sample.go。

```PowerShell
go run sample.go
```

應用程式的輸出應該會類似下列文字：

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

接著，您可以確認測試影像 (位於 **<base_image_url>/Images/Test/**) 的標記是否適當。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在您已經知道如何在程式碼中完成影像分類程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)