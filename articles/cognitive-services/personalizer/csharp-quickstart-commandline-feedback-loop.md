---
title: 快速入門：建立意見反應迴圈 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 使用個人化工具服務將此 C# 快速入門中的內容個人化。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 54aa23071fef09058a1702218d6b7fc920363518
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662804"
---
# <a name="quickstart-personalize-content-using-c"></a>快速入門：使用 C# 將內容個人化 

使用個人化工具服務顯示此 C# 快速入門中的個人化內容。

這個範例會示範如何使用適用於 C# 的個人化工具用戶端程式庫來執行下列動作： 

 * 為個人化的動作清單排名。
 * 報告要根據使用者為指定事件所做的選擇，配置給最佳排名動作的報酬。

開始使用個人化工具會涉及下列步驟：

1. 參考 SDK 
1. 撰寫程式碼來針對您要顯示給使用者的動作進行排名
1. 撰寫程式碼來傳送用於將迴圈定型的報酬。

## <a name="prerequisites"></a>必要條件

* 您需要[個人化工具服務](how-to-settings.md)以取得訂用帳戶金鑰及端點服務 URL。 
* [Visual Studio 2015 或 2017](https://visualstudio.microsoft.com/downloads/).
* [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272) \(英文\) SDK NuGet 套件。 下面會提供安裝指示。

## <a name="change-the-model-update-frequency"></a>變更模型更新頻率

在 Azure 入口網站的個人化資源中，將 [模型更新頻率]  變更為 10 秒。 如此可快速定型服務，讓您查看最上次的動作如何針對每個反覆項目變更。

第一次具現化個人化工具迴圈時，因為已經沒有可進行訓練的獎勵 API 呼叫，所以沒有模型。 排名呼叫會針對每個項目傳回相等的機率。 您的應用程式應該仍會一直使用 RewardActionId 的輸出來排名內容。

![變更模型更新頻率](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>建立新的主控台應用程式並參考個人化工具 SDK 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. 在 Visual Studio 中建立 Visual C# 主控台應用程式。
1. 安裝個人化工具用戶端程式庫 NuGet 套件。 在功能表上，選取 [工具]  ，選取 [NuGet 套件管理員]  ，然後選取 [管理解決方案的 NuGet 套件]  。
1. 勾選 [包含搶鮮版]  。
1. 選取 [瀏覽]  索引標籤，然後在 [搜尋]  方塊中鍵入 `Microsoft.Azure.CognitiveServices.Personalizer`。
1. 在 **Microsoft.Azure.CognitiveServices.Personalizer** 出現時加以選取。
1. 選取專案名稱旁邊的核取方塊，然後選取 [安裝]  。

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>新增程式碼並於其中放入個人化工具和 Azure 金鑰

1. 以下列程式碼取代 Program.cs。 
1. 將 `serviceKey` 值取代為有效的個人化工具訂用帳戶金鑰。
1. 將 `serviceEndpoint` 取代為服務端點。 例如 `https://westus2.api.cognitive.microsoft.com/`。
1. 執行程式。

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>新增程式碼來針對您要顯示給使用者的動作進行排名

下列 C# 程式碼是完整的清單，其可使用 SDK 將使用者資訊、_features 和內容的相關資訊 (動作  ) 傳給個人化工具。 個人化工具會傳回排名最佳的動作以對使用者顯示。  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

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

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
                var request = new RankRequest(actions, currentContext, excludeActions, eventId);
                RankResponse response = client.Rank(request);

                Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

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

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
        static string GetUsersTimeOfDay()
        {
            string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

            Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
            if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
                timeIndex = 1;
            }

            return timeOfDayFeatures[timeIndex - 1];
        }

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
        static string GetUsersTastePreference()
        {
            string[] tasteFeatures = new string[] { "salty", "sweet" };

            Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
            if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
            {
                Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
                tasteIndex = 1;
            }

            return tasteFeatures[tasteIndex - 1];
        }

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
        static IList<RankableAction> GetActions()
        {
            IList<RankableAction> actions = new List<RankableAction>
            {
                new RankableAction
                {
                    Id = "pasta",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
                },

                new RankableAction
                {
                    Id = "ice cream",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
                },

                new RankableAction
                {
                    Id = "juice",
                    Features =
                    new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
                },

                new RankableAction
                {
                    Id = "salad",
                    Features =
                    new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
                }
            };

            return actions;
        }

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>執行程式

建置並執行程式。 快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>清除資源
當您完成快速入門時，請移除在本快速入門中建立的所有檔案。 

## <a name="next-steps"></a>後續步驟

[個人化工具的運作方式](how-personalizer-works.md)


