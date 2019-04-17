---
title: 跨訂用帳戶移轉臉部資料 - 臉部 API
titleSuffix: Azure Cognitive Services
description: 本指南示範如何將已儲存的臉部資料從一個臉部 API 訂用帳戶移轉至另一個。
services: cognitive-services
author: lewlu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: lewlu
ms.openlocfilehash: 30ceb0e396597530071c70c4448761d914acb4ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548399"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>將您的臉部資料移轉至其他臉部訂用帳戶

本指南示範如何使用「快照集」功能，將臉部資料 (例如，已儲存的 **PersonGroup** 臉部) 移動到其他 臉部 API 訂用帳戶。 這可讓您在移動或擴充您的作業時，避免重複建立和定型 **PersonGroup** 或是 **FaceList**。 例如，您可能已使用免費試用版訂用帳戶建立 **PersonGroup**，但現在想要將它移轉至您的付費訂用帳戶，或您可能需要將臉部資料同步處理至跨區域的大型企業作業。

相同的移轉策略也適用於 **LargePersonGroup** 和 **LargeFaceList** 物件。 如果您不熟悉本指南中的概念，請在[詞彙](../Glossary.md)中參閱其定義。 本指南搭配使用臉部 API .NET 用戶端程式庫和 C#。

## <a name="prerequisites"></a>必要條件

- 兩個臉部 API 訂用帳戶金鑰 (一個含現有資料，另一個是要移轉至的位置)。 依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱臉部 API 服務並取得您的金鑰。
- 對應到目標訂用帳戶的臉部 API 訂用帳戶識別碼 (可在 Azure 入口網站中的 [概觀] 刀鋒視窗中找到)。 
- 任何 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 版本。


## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

本指南將使用簡單的主控台應用程式來執行臉部資料移轉。 如需完整的實作，請參閱 GitHub 上的[臉部 API 快照集範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)。

1. 在 Visual Studio 中建立新的**主控台應用程式 (.NET Framework)** 專案，並將它命名為 **FaceApiSnapshotSample**。 
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [管理 NuGet 套件]。 按一下 [瀏覽] 索引標籤，並選取 [包含發行前版本]；然後尋找並安裝下列套件：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) \(英文\)


## <a name="create-face-clients"></a>建立臉部用戶端

在 *Program.cs* 的 **Main** 方法中，為您的來源和目標訂用帳戶建立兩個 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 執行個體。 在此範例中，我們將使用位在東亞區域的臉部訂用帳戶作為來源，並使用美國西部訂用帳戶作為目標。 這樣可以示範如何將資料從一個 Azure 區域移轉至另一個。 如果您的訂用帳戶位在不同區域，您將需要變更 `Endpoint` 字串。

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

您將需要填入來源和目標訂用帳戶的訂用帳戶金鑰值和端點 URL。


## <a name="prepare-a-persongroup-for-migration"></a>備妥移轉的 PersonGroup

若要將您來源訂用帳戶中的 **PersonGroup** 移轉到目標訂用帳戶，您會需要它的識別碼。 使用 **[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)** 方法來擷取您 **PersonGroup** 物件的清單，然後取得 **[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)** 屬性。 此程序會根據您擁有的 **PersonGroup** 而有所不同。 在本指南中，來源 **PersonGroup** 識別碼儲存在 `personGroupId` 中。

> [!NOTE]
> [程式碼範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)會建立並定型要移轉的新 **PersonGroup**，但在大部分情況下，您應該已經有可使用的 **PersonGroup**。

## <a name="take-snapshot-of-persongroup"></a>建立 PersonGroup 的快照集

快照集是某些臉部資料類型的暫時性遠端存放。 將資料從一個訂用帳戶複製到另一個訂用帳戶時，它的功能是類似剪貼簿。 首先，使用者「建立」來源訂用帳戶中資料的快照集，然後他們將它「套用」至目標訂用帳戶中的資料物件。

使用來源訂用帳戶的 **FaceClient** 執行個體來建立 **PersonGroup** 的快照集，搭配使用 **[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet)**、**PersonGroup** 識別碼和目標訂用帳戶的識別碼。 如果您有多個目標訂用帳戶，您可以在第三個參數中以陣列的形式新增它們。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 建立和套用快照集的程序不會影響對來源或目標 **PersonGroup** (或 **FaceList**) 的任何正常呼叫。 不過，我們不建議進行變更的來源物件的同時呼叫 ([FaceList 管理呼叫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或[PersonGroup 訓練](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)呼叫，例如)，因為快照集作業可能會執行這些作業的前後，或可能會發生錯誤。

## <a name="retrieve-the-snapshot-id"></a>擷取快照集識別碼

採取方法快照集是非同步的因此您必須等候其完成 （快照集無法取消作業）。 在此程式碼中，`WaitForOperation` 方法會監視非同步呼叫 (每 100ms 檢查一次狀態)。 作業完成後，您將能夠擷取作業識別碼。 您可以藉由剖析 `OperationLocation` 欄位來取得。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

一般的 `OperationLocation` 值看起來如下：

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation` 協助程式方法如下：

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

當作業狀態標示為 `Succeeded` 時，您就可以剖析傳回之 **OperationStatus** 執行個體的 `ResourceLocation` 欄位來取得快照集識別碼。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

一般的 `resourceLocation` 值看起來如下：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-snapshot-to-target-subscription"></a>將快照集套用到目標訂用帳戶

接下來，使用隨機產生的識別碼在目標訂用帳戶中建立新的 **PersonGroup**。 然後使用目標訂用帳戶的 **FaceClient** 執行個體將快照集套用至這個 PersonGroup，傳遞快照集識別碼和新的 **PersonGroup** 識別碼。 

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Snapshot 物件的有效時間只有 48 小時。 您應該在即將使用快照集進行資料移轉時才建立它。

套用快照集的要求會傳回另一個作業識別碼。 您可以藉由剖析傳回之 **applySnapshotResult** 執行個體的 `OperationLocation` 欄位來取得此識別碼。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

快照集套用程序也是非同步的，因此也是使用 `WaitForOperation` 來等候它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>測試資料移轉

套用快照集之後，原始的臉部資料應該就會填入到目標訂用帳戶中的新 **PersonGroup**。 根據預設，系統也會複製定型結果，因此新的 **PersonGroup** 將可用於臉部辨識呼叫，而不需要重新定型。

若要測試資料移轉，您可以執行下列作業，並比較列印到主控台的結果。

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

使用下列協助程式方法：

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

您現在可以開始使用目標訂用帳戶中的新 **PersonGroup**。 

如果您未來想要再次更新目標 **PersonGroup**，您將需要建立新的 **PersonGroup** (按照本指南的步驟) 來接收快照集。 單一 **PersonGroup** 物件只能套用一次快照集。

## <a name="clean-up-resources"></a>清除資源

一旦您完成臉部資料移轉，我們建議您手動刪除快照集物件。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="related-topics"></a>相關主題

- [快照集參考文件 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [臉部 API 快照集範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [如何新增臉部](how-to-add-faces.md)
- [如何偵測影像中的人臉](HowtoDetectFacesinImage.md)
- [如何識別影像中的臉部](HowtoIdentifyFacesinImage.md)
