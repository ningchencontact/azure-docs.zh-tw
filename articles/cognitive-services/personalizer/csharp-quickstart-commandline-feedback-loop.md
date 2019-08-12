---
title: 快速入門：適用於 .NET 的個人化工具用戶端程式庫 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 使用學習迴圈來開始使用適用於 .NET 的個人化工具用戶端程式庫。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/5/2019
ms.author: diberry
ms.openlocfilehash: 3b583fa7d9c7bab89accabf68034df407cb89a9c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839946"
---
# <a name="quickstart-personalize-client-library-for-net"></a>快速入門：適用於 .NET 的個人化工具用戶端程式庫

使用個人化工具服務顯示此 C# 快速入門中的個人化內容。

開始使用適用於 .NET 的個人化工具用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

 * 為個人化的動作清單排名。
 * 回報獎勵分數，指出已成功排列出名次最高的動作。

[參考文件](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [範例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-personalizer-azure-resource"></a>建立個人化工具 Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立個人化工具的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源。

<!-- rename TBD_KEY to something meaningful for your service, like TEXT_ANALYTICS_KEY -->
從試用版訂用帳戶或資源取得金鑰後，請建立兩個[環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `PERSONALIZER_RESOURCE_KEY` 用於資源金鑰。
* `PERSONALIZER_RESOURCE_ENDPOINT` 用於資源端點。

### <a name="change-the-model-update-frequency"></a>變更模型更新頻率

在 Azure 入口網站的個人化資源中，將 [模型更新頻率]  變更為 10 秒。 如此可快速定型服務，讓您查看最上次的動作如何針對每個反覆項目變更。

![變更模型更新頻率](./media/settings/configure-model-update-frequency-settings.png)

第一次具現化個人化工具迴圈時，因為已經沒有可進行訓練的獎勵 API 呼叫，所以沒有模型。 排名呼叫會針對每個項目傳回相等的機率。 您的應用程式應該仍會一直使用 RewardActionId 的輸出來排名內容。

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。 

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `personalizer-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。 

```console
dotnet new console -n personalizer-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>安裝 SDK

在應用程式目錄中，使用下列命令安裝適用於 .NET 的個人化工具用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的排名，請建立 [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)，然後將其傳遞給 [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) 方法。 Rank 方法會傳回包含排名內容的 RankResponse。 

若要將獎勵傳送給個人化工具，請建立 [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)，然後將其傳遞至 [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) 方法。 

在本快速入門中，決定獎勵是很簡單的。 在生產系統中，判斷影響[獎勵分數](concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 這應該是個人化工具架構中的一個主要設計決策。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的個人化工具用戶端程式庫來執行下列動作：

* [建立個人化工具用戶端](#create-a-personalizer-client)
* [要求排名](#request-a-rank)
* [傳送獎勵](#send-a-reward)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 **Program.cs** 檔案。 將現有的 `using` 程式碼取代為下列 `using` 指示詞：

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>新增個人化工具資源資訊

在 [程式]  類別中，針對從環境變數中提取的資源 Azure 金鑰和端點 (名稱為 `PERSONALIZER_RESOURCE_KEY` 和 `PERSONALIZER_RESOURCE_ENDPOINT`) 建立變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 這些方法稍後會在本快速入門中建立。

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>建立個人化工具用戶端

接下來，建立可傳回個人化工具用戶端的方法。 方法的參數是 `PERSONALIZER_RESOURCE_ENDPOINT`，而 ApiKey 是 `PERSONALIZER_RESOURCE_KEY`。

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-content-choices-represented-as-actions"></a>取得以動作表示的內容選擇

動作代表您想要讓個人化工具進行排名的內容選擇。 將下列方法新增至 [程式] 類別，以從命令列取得一天時間和目前食物喜好的使用者輸入。

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

這兩個方法都會使用 `GetKey` 方法從命令列讀取使用者的選取。 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是排名和獎勵呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務在內容排名上的成效。 

在程式的 `main` 方法中，下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以進行排名，然後向客戶顯示已排名的選取項目，讓他們從清單中選擇，接著傳送獎勵給個人化工具，告知服務在排名選取項目上的成效為何。

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Initialize Personalizer client.
    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        // <rank>
        // Get context information from the user.
        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        // Create current context from user specified data.
        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        // Exclude an action for personalizer ranking. This action will be held at its current position.
        // This simulates a business rule to force the action "juice" to be ignored in the ranking.
        // As juice is excluded, the return of the API will always be with a probability of 0.
        IList<string> excludeActions = new List<string> { "juice" };

        // Generate an ID to associate with the request.
        string eventId = Guid.NewGuid().ToString();

        // Rank the actions
        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);
        // </rank>

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        // <reward>
        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        // Send the reward for the action based on user response.
        client.Reward(response.EventId, new RewardRequest(reward));
        // </reward>

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

## <a name="request-a-rank"></a>要求排名

為了完成排名要求，程式會詢問使用者的喜好，以建立內容選擇的 `currentContent`。 程式可以建立從排名中排除的內容，如 `excludeActions` 所示。 排名要求需要 actions、currentCoNtext、excludeActions 和唯一排名事件識別碼 (作為 GUID)，才能接收排名的回應。 

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](concept-feature-evaluation.md)[動作和特性](concepts-features.md)可能不是簡單的事。  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>傳送獎勵

若要完成獎勵要求，程式會從命令列取得使用者的選取項目，將數值指派給每個選取項目，然後將唯一的排名事件識別碼和數值傳送給獎勵方法。

本快速入門會指派簡單的數字作為獎勵，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](concept-rewards.md)呼叫的時機和內容可能不是簡單的事。 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>執行程式

從應用程式目錄使用 dotnet `run` 命令來執行應用程式。

```dotnet
dotnet run
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[本快速入門的原始程式碼](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs)可在個人化工具範例 GitHub 存放庫中取得。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[個人化工具的運作方式](how-personalizer-works.md)

* [什麼是個人化工具？](what-is-personalizer.md)
* [個人化工具可以應用在何處？](where-can-you-use-personalizer.md)
* [疑難排解](troubleshooting.md)

