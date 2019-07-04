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
ms.openlocfilehash: 702aed12860c090e83b997e6b56d56e06b416568
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65913799"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>將您的臉部資料移轉至其他臉部訂用帳戶

本指南將說明如何將臉部資料，例如儲存的 PersonGroup 物件的表面，以移至不同的 Azure 認知服務臉部 API 訂用帳戶。 若要移動資料，您可以使用快照集功能。 如此一來您避免重複建立和定型 PersonGroup 或 FaceList 物件，當您移動或擴充您的作業。 比方說，或許您使用免費的試用版訂用帳戶所建立的 PersonGroup 物件而現在想要將它移轉至付費訂用帳戶。 或者，您可能需要臉部資料同步處理跨區域的大型企業的作業。

這個相同的移轉策略也適用於 LargePersonGroup 和 LargeFaceList 物件。 如果您不熟悉本指南中的概念，請參閱在其定義[臉部辨識概念](../concepts/face-recognition.md)指南。 本指南搭配使用臉部 API .NET 用戶端程式庫和 C#。

## <a name="prerequisites"></a>必要條件

您需要下列項目：

- 兩人臉識別 API 訂用帳戶金鑰，一個與現有的資料，移轉到的另一個。 若要訂閱的人臉識別 API 服務，並取得您的金鑰，請依照下列中的指示[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。
- 人臉識別 API 訂用帳戶 ID 字串對應至目標訂用帳戶。 若要尋找它，請選取**概觀**在 Azure 入口網站中。 
- 任何 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 版本。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

本指南會使用簡單的主控台應用程式執行臉部資料移轉。 如需完整的實作，請參閱[人臉識別 API snapshot 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)GitHub 上。

1. 在 Visual Studio 中建立新的主控台應用程式的.NET Framework 專案。 命名**FaceApiSnapshotSample**。
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取**管理 NuGet 套件**。 選取 **瀏覽**索引標籤，然後選取**包含發行前版本**。 尋找並安裝下列套件：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.3.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview) \(英文\)

## <a name="create-face-clients"></a>建立臉部用戶端

在  **Main**方法中的*Program.cs*，建立兩個[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)來源和目標訂用帳戶的執行個體。 此範例會使用在東亞地區臉部的訂用帳戶，為來源，並做為目標的美國西部訂用帳戶。 此範例示範如何將資料從一個 Azure 區域移轉至另一個。 如果您的訂用帳戶位於不同區域中，變更`Endpoint`字串。

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

填入訂用帳戶金鑰值和來源和目標訂用帳戶的端點 Url。


## <a name="prepare-a-persongroup-for-migration"></a>備妥移轉的 PersonGroup

您需要 PersonGroup 的識別碼來源訂用帳戶移轉到目標訂用帳戶。 使用[PersonGroupOperationsExtensions.ListAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync?view=azure-dotnet)方法來擷取一份 PersonGroup 物件。 然後取得[PersonGroup.PersonGroupId](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId)屬性。 此程序看起來不同根據哪些 PersonGroup 物件您有。 在本指南中，來源 PersonGroup 識別碼會儲存在`personGroupId`。

> [!NOTE]
> [範例程式碼](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)建立並定型新的 PersonGroup 移轉。 在大部分情況下，您應該已經有使用 PersonGroup。

## <a name="take-a-snapshot-of-a-persongroup"></a>建立快照集的 PersonGroup

快照集是針對特定臉部資料類型的暫存遠端儲存體。 將資料從一個訂用帳戶複製到另一個訂用帳戶時，它的功能是類似剪貼簿。 首先，您可以採取快照集的資料來源訂用帳戶中。 然後您將它套用至目標訂用帳戶中新的資料物件。

若要建立快照集的 PersonGroup 中使用來源訂用帳戶的 FaceClient 執行個體。 使用[TakeAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync?view=azure-dotnet) PersonGroup 識別碼與目標訂用帳戶的識別碼。 如果您有多個目標訂用帳戶，請將他們新增為第三個參數中的陣列項目。

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> 取得並套用快照集的程序不會中斷任何來源的一般呼叫或 Persongroup 或 Facelist 為目標。 不要變更來源物件，例如的同時呼叫[FaceList 管理呼叫](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)或[PersonGroup 訓練](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)呼叫，例如。 快照集作業之前或之後這些作業可能會執行，或可能會遇到錯誤。

## <a name="retrieve-the-snapshot-id"></a>擷取快照集識別碼

用來建立快照集的方法是非同步的因此您必須等候其完成。 無法取消快照集作業。 在此程式碼，`WaitForOperation`方法會監視非同步呼叫。 它會檢查每 100 毫秒的狀態。 在作業完成之後，擷取作業識別碼，藉由剖析`OperationLocation`欄位。 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

一般`OperationLocation`值看起來像這樣：

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

之後的作業狀態會顯示`Succeeded`，藉由剖析取得快照 ID`ResourceLocation`欄位傳回的 OperationStatus 執行個體。

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

一般`resourceLocation`值看起來像這樣：

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>將快照集套用到目標訂用帳戶

接下來，在目標訂用帳戶中建立新的 PersonGroup 使用隨機產生的識別碼。 要套用至這個 PersonGroup 的 快照集，然後使用目標訂用帳戶的 FaceClient 執行個體。 傳入的快照集識別碼和新的 PersonGroup 識別碼。

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> 只有 48 小時的快照集物件是有效的。 只有建立快照集，如果您想要將它用於資料移轉之後。

快照集套用要求會傳回另一個作業識別碼。 若要取得這個識別碼，剖析`OperationLocation`傳回的 applySnapshotResult 執行個體的欄位。 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

快照集應用程式處理序也是非同步，因此一次使用`WaitForOperation`等候它完成。

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>測試資料移轉

套用快照集之後，新的 PersonGroup 目標訂用帳戶中會填入原始的臉部資料。 根據預設，也會複製定型的結果。 新的 PersonGroup 是供臉部識別呼叫的不需要重新訓練。

若要測試資料移轉，執行下列作業，並比較的結果，其會列印到主控台：

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

現在您可以使用新的 PersonGroup 目標訂用帳戶中。 

若要在未來再次更新目標 PersonGroup，建立新的 PersonGroup 接收快照集。 若要這樣做，請遵循本指南中的步驟。 單一的 PersonGroup 物件可以有快照集套用一次。

## <a name="clean-up-resources"></a>清除資源

在您完成移轉後的資料，以手動方式刪除快照集的物件。

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>後續步驟

接下來，請參閱相關的 API 參考文件，瀏覽範例應用程式使用快照集功能，或遵循開始使用這裡所述的其他 API 作業的操作說明指南：

- [快照集參考文件 (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)
- [人臉識別 API 快照集範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [新增臉部](how-to-add-faces.md)
- [偵測影像中的臉部](HowtoDetectFacesinImage.md)
- [識別影像中的臉部](HowtoIdentifyFacesinImage.md)
