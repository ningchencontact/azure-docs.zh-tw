---
title: 快速入門：適用於 Node.js 的個人化工具用戶端程式庫 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 使用學習迴圈來開始使用適用於 Node.js 的個人化工具用戶端程式庫。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: a7c02c92ed61e4c8c8bd6a634cc9c6ad3538396e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883687"
---
# <a name="quickstart-personalize-client-library-for-nodejs"></a>快速入門：適用於 Node.js 的個人化工具用戶端程式庫

使用個人化工具服務顯示此 Node.js 快速入門中的個人化內容。

開始使用適用於 Node.js 的個人化工具用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

 * 為個人化的動作清單排名。
 * 回報獎勵分數，指出已成功排列出名次最高的動作。

[程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-personalizer) | [套件 (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-personalizer) | [範例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版的 [Node.js](https://nodejs.org)。

## <a name="setting-up"></a>設定

### <a name="create-a-personalizer-azure-resource"></a>建立個人化工具 Azure 資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立個人化工具的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源。

從試用版訂用帳戶或資源取得金鑰後，請建立兩個[環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `PERSONALIZER_KEY` 用於資源金鑰。
* `PERSONALIZER_ENDPOINT` 用於資源端點。

在 Azure 入口網站中，可以從 [快速入門]  頁面取得金鑰和端點值。


### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行 `npm init -y` 命令以建立 `package.json` 檔案。 

```console
npm init -y
```

### <a name="install-the-nodejs-library-for-personalizer"></a>安裝 Node.js 個人化工具程式庫

使用下列命令安裝適用於 Node.js 的個人化工具用戶端程式庫：

```console
npm install @azure/cognitiveservices-personalizer --save
```

安裝本快速入門的其餘 NPM 套件：

```console
npm install @azure/ms-rest-azure-js @azure/ms-rest-js readline-sync uuid --save
```

### <a name="change-the-model-update-frequency"></a>變更模型更新頻率

在 Azure 入口網站的個人化資源中，將 [模型更新頻率]  變更為 10 秒。 如此可快速定型服務，讓您查看最上次的動作如何針對每個反覆項目變更。

![變更模型更新頻率](./media/settings/configure-model-update-frequency-settings.png)

第一次具現化個人化工具迴圈時，因為已經沒有可進行訓練的獎勵 API 呼叫，所以沒有模型。 排名呼叫會針對每個項目傳回相等的機率。 您的應用程式應該仍會一直使用 RewardActionId 的輸出來排名內容。

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 PersonalizerClient 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的排名，請建立 RankRequest，然後將其傳至 client.Rank 方法。 Rank 方法會傳回包含排名內容的 RankResponse。 

若要將獎勵傳送至個人化工具，請建立 RewardRequest，然後將其傳至 client.Reward 方法。 

在本快速入門中，決定獎勵是很簡單的。 在生產系統中，判斷影響[獎勵分數](concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 這應該是個人化工具架構中的一個主要設計決策。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 Node.js 的個人化工具用戶端程式庫來執行下列動作：

* [建立個人化工具用戶端](#create-a-personalizer-client)
* [要求排名](#request-a-rank)
* [傳送獎勵](#send-a-reward)

## <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在您慣用的編輯器或名為 `sample.js` 的 IDE 中，建立新的 Node.js 應用程式。 

## <a name="add-the-dependencies"></a>新增相依性

在您慣用的編輯器或 IDE 中，開啟 **sample.js** 檔案。 新增下列 `requires` 以新增 NPM 套件：

[!code-javascript[Add module dependencies](~/samples-personalizer/quickstarts/node/sample.js?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>新增個人化工具資源資訊

針對從環境變數中提取的資源 Azure 金鑰和端點 (名稱為 `PERSONALIZER_KEY` 和 `PERSONALIZER_ENDPOINT`) 建立變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 這些方法稍後會在本快速入門中建立。

[!code-javascript[Add Personalizer resource information](~/samples-personalizer/quickstarts/node/sample.js?name=AuthorizationVariables)]

## <a name="create-a-personalizer-client"></a>建立個人化工具用戶端

接下來，建立可傳回個人化工具用戶端的方法。 方法的參數是 `PERSONALIZER_RESOURCE_ENDPOINT`，而 ApiKey 是 `PERSONALIZER_RESOURCE_KEY`。

[!code-javascript[Create a Personalizer client](~/samples-personalizer/quickstarts/node/sample.js?name=Client)]

## <a name="get-content-choices-represented-as-actions"></a>取得以動作表示的內容選擇

動作代表您想要讓個人化工具進行排名的內容選擇。 將下列方法新增至 [程式] 類別，以從命令列取得一天時間和目前食物喜好的使用者輸入。

[!code-javascript[Create user features](~/samples-personalizer/quickstarts/node/sample.js?name=createUserFeatureTimeOfDay)]

[!code-javascript[Create actions](~/samples-personalizer/quickstarts/node/sample.js?name=getActions)]

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是[排名](#request-a-rank)和[獎勵](#send-a-reward)呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務在內容排名上的成效。 

下列迴圈程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以進行排名，然後向客戶顯示已排名的選取項目，讓他們從清單中選擇，接著傳送獎勵給個人化工具，告知服務在排名選取項目上的成效為何。

[!code-javascript[Create the learning loop](~/samples-personalizer/quickstarts/node/sample.js?name=mainLoop)]

請仔細查看下列各節中的排名和報酬呼叫。

## <a name="request-a-rank"></a>要求排名

為了完成排名要求，程式會詢問使用者的喜好來建立內容選擇。 程式可以建立從排名中排除的內容，如 `excludeActions` 所示。 排名要求需要 [actions](concepts-features.md#actions-represent-a-list-of-options)、currentCoNtext、excludeActions 和唯一排名事件識別碼 (作為 GUID)，才能接收排名的回應。 

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](concept-feature-evaluation.md)[動作和特性](concepts-features.md)可能不是簡單的事。  

[!code-javascript[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/node/sample.js?name=rank)]

## <a name="send-a-reward"></a>傳送獎勵

若要完成獎勵要求，程式會從命令列取得使用者的選取項目，將數值指派給每個選取項目，然後將唯一的排名事件識別碼和數值傳送給獎勵方法。

本快速入門會指派簡單的數字作為獎勵，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](concept-rewards.md)呼叫的時機和內容可能不是簡單的事。 

[!code-javascript[The Personalizer learning loop sends a reward.](~/samples-personalizer/quickstarts/node/sample.js?name=reward)]

## <a name="run-the-program"></a>執行程式

使用 Node.js 從您的應用程式目錄執行應用程式。

```console
node sample.js
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](./media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

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
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/node/sample.js) 上找到。
