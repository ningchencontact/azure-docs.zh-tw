---
title: 訂用帳戶金鑰
titleSuffix: Language Understanding - Azure Cognitive Services
description: 您不需要建立訂用帳戶金鑰，即可免費使用您的前 1000 個端點查詢。 如果您收到 HTTP 403 或 429 格式的_超出配額_錯誤，則需要建立金鑰，並將其指派給您的應用程式。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: dedc498ebc910b448b1684136c288b2045780e00
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797959"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>使用您的訂用帳戶金鑰搭配 LUIS 應用程式

當您第一次使用 Language Understanding (LUIS) 時，則您不需要建立訂用帳戶金鑰。 您可以 1000年端點查詢開始。 

僅針對測試和原型，使用免費 (F0) 層。 針對生產系統，使用[付費](https://aka.ms/luis-price-tier)層。 請勿在生產環境中對端點查詢使用[撰寫金鑰](luis-concept-keys.md#authoring-key)。


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>在 Azure 入口網站中建立預測端點執行階段資源

您建立[預測端點資源](get-started-portal-deploy-app.md#create-the-endpoint-resource)在 Azure 入口網站中。 此資源僅適用於端點預測查詢。 請勿使用此資源撰寫應用程式的變更。

您可以建立語言理解資源或認知服務資源。 如果您要建立語言理解資源，最好是的 postpend 的資源類型的資源名稱。 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>使用從 LUIS 入口網站的資源

如果您使用來自 LUIS 入口網站的資源，您不需要知道您的金鑰和位置。 相反地，您需要知道您的資源租用戶、 訂用帳戶和資源名稱。

您一次[指派](#assign-resource-key-to-luis-app-in-luis-portal)資源應用程式 LUIS，LUIS 入口網站、 金鑰和位置來提供查詢預測端點 url，在 [管理] 區段**金鑰和端點設定**頁面。
 
### <a name="using-resource-from-rest-api-or-sdk"></a>使用來自 REST API 或 SDK 資源

如果您使用來自 REST API(s) 或 SDK 的資源，您需要知道您的金鑰和位置。 這項資訊可在 管理 區段中的查詢預測端點 URL 的一部分**金鑰和端點設定**也如同 Azure 入口網站中，在 資源概觀 和 索引鍵的頁面上的頁面。

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>將資源金鑰指派給 LUIS 入口網站中的 LUIS 應用程式

每次您建立新的資源 LUIS，您需要[LUIS 應用程式指派資源](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)。 指派之後，除非您建立新的資源，否則不需要再次執行此步驟。 您可以建立新的資源，以擴充您的應用程式區域，或支援更多預測查詢數目。

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>取消指派資源
當您取消指派端點金鑰時，它並不會從 Azure 中刪除。 只會從 LUIS 取消連結。 

若未指定端點金鑰，或未指派給應用程式，任何對端點 URL 的要求都會傳回錯誤：`401 This application cannot be accessed with the current subscription`。 

### <a name="include-all-predicted-intent-scores"></a>Include all predicted intent scores \(包括所有預測意圖分數\)
[Include all predicted intent scores] \(包括所有預測意圖分數\)  核取方塊允許端點查詢回應包括每個意圖的預測分數。 

此設定可讓您的聊天機器人或 LUIS 通話應用程式根據所傳回意圖的分數進行程式設計決策。 一般而言，前兩個意圖最為有趣。 若最高分數為 None 意圖，則您的聊天機器人可以選擇詢問待處理問題，明確選擇 None 意圖與其他高計分意圖。 

這些意圖和其分數也會包括在端點記錄中。 您可以[匯出](luis-how-to-start-new-app.md#export-app)這些記錄，並分析分數。 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>啟用 Bing 拼字檢查工具 
在 [端點 URL 設定]  中，[Bing 拼字檢查工具]  切換開關會允許 LUIS 在預測之前更正錯字。 建立 **[Bing 拼字檢查金鑰](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** 。 

新增 **spellCheck=true** 查詢字串參數和 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** 。 將 `{YOUR_BING_KEY_HERE}` 取代為 Bing 拼字檢查金鑰。

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>發行區域

深入了解如何發佈[區域](luis-reference-regions.md)，包括在[歐洲](luis-reference-regions.md#publishing-to-europe)和[澳洲](luis-reference-regions.md#publishing-to-australia)發佈。 發佈區域與撰寫區域不同。 在您所要發佈區域的對應撰寫區域中，為查詢端點建立應用程式。

## <a name="assign-resource-without-luis-portal"></a>在不使用 LUIS 入口網站的情況下指派資源

基於自動化用途 (例如 CI/CD 管線)，您可能想要讓 LUIS 資源指派給 LUIS 應用程式的作業自動執行。 為此，您必須執行下列步驟：

1. 從這個[網站](https://resources.azure.com/api/token?plaintext=true)取得 Azure Resource Manager 權杖。 此權杖有使用期限，因此請立即使用。 要求會傳回 Azure Resource Manager 權杖。

    ![要求 Azure Resource Manager 權杖和接收 Azure Resource Manager 權杖](./media/luis-manage-keys/get-arm-token.png)

1. 從[取得 LUIS Azure 帳戶 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c) 使用權杖，要求您的使用者帳戶在各個訂用帳戶間有權存取的 LUIS 資源。 

    此 POST API 需要下列設定︰

    |標頭|值|
    |--|--|
    |`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。| 
    |`Ocp-Apim-Subscription-Key`|您的[撰寫金鑰](luis-how-to-account-settings.md)。|

    此 API 會傳回您 LUIS 訂用帳戶的 JSON 物件陣列，其中包含訂用帳戶識別碼、資源群組和資源名稱 (傳回作為帳戶名稱)。 請在此陣列中找出要指派給 LUIS 應用程式的 LUIS 資源項目。 

1. 使用[將 LUIS Azure 帳戶指派給應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，將權杖指派給 LUIS 資源。 

    此 POST API 需要下列設定︰

    |type|設定|值|
    |--|--|--|
    |標頭|`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。|
    |標頭|`Ocp-Apim-Subscription-Key`|您的[撰寫金鑰](luis-how-to-account-settings.md)。|
    |標頭|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 應用程式識別碼。 
    |本文||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功執行時，會傳回「201 - 已建立」狀態。 

## <a name="change-pricing-tier"></a>變更定價層

1.  在 [Azure](https://portal.azure.com) 中，尋找您的 LUIS 訂用帳戶。 選取 LUIS 訂用帳戶。
    ![尋找 LUIS 訂用帳戶](./media/luis-usage-tiers/find.png)
1.  選取 [定價層]  以查看可用的定價層。 
    ![檢視定價層](./media/luis-usage-tiers/subscription.png)
1.  選取定價層，然後選取 [選取]  以儲存變更。 
    ![變更 LUIS 付款層](./media/luis-usage-tiers/plans.png)
1.  定價變更完成時，快顯視窗會確認新的定價層。 
    ![驗證 LUIS 付款層](./media/luis-usage-tiers/updated.png)
1. 請記得在 [發佈]  頁面上[指派此端點金鑰](#assign-endpoint-key)，然後將它使用於所有端點查詢。 

## <a name="fix-http-status-code-403-and-429"></a>修正 HTTP 狀態碼 403 和 429

您收到 403 和 429 錯誤狀態碼時您超過每秒交易數或每月的定價層的交易。

### <a name="when-you-receive-an-http-403-error-status-code"></a>當您收到 HTTP 403 錯誤狀態碼

當您使用所有這些免費 1000年端點查詢或超過您的定價層每月交易配額時，您會收到 HTTP 403 錯誤狀態碼。 

若要修正這個錯誤，您必須[變更定價層](luis-how-to-azure-subscription.md#change-pricing-tier)更高的層次或[建立新的資源](get-started-portal-deploy-app.md#create-the-endpoint-resource)並[將它指派給您的應用程式](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)。

此錯誤的解決方案包括：

* 在  [Azure 入口網站](https://portal.azure.com)，在您了解資源的語言**資源管理]-> [定價層**，將您的定價層變更為較高的 TPS 層。 您不需要執行 Language Understanding 入口網站中的任何項目，如果您的資源已指派給您的 Language Understanding 應用程式。
*  如果您的使用量超過最高的定價層，新增更多的 Language Understanding 資源前面是負載平衡器。 [Language Understanding 容器](luis-container-howto.md)Kubernetes 或 Docker Compose 可以幫助以此方式。

### <a name="when-you-receive-an-http-429-error-status-code"></a>當您收到 HTTP 429 錯誤狀態碼

此狀態碼時，會傳回您每秒交易數超過您的定價層。  

解決方案包括：

* 您可以[增加您的定價層](#change-pricing-tier)，如果您不是在最高層級。
* 如果您的使用量超過最高的定價層，新增更多的 Language Understanding 資源前面是負載平衡器。 [Language Understanding 容器](luis-container-howto.md)Kubernetes 或 Docker Compose 可以幫助以此方式。
* 您可以閘道與您用戶端應用程式要求[重試原則](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)您自行實作時取得此狀態碼。 

## <a name="viewing-summary-usage"></a>檢視摘要使用量
您可以在 Azure 中檢視 LUIS 使用量資訊。 [概觀]  頁面會顯示近期摘要資訊，包括呼叫和錯誤。 如果您提出 LUIS 端點要求，然後立即觀看 [概觀] 頁面  ，允許使用量最多在五分鐘內出現。

![檢視摘要使用量](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>自訂使用量圖表
計量會提供資料的更詳細檢視。

![預設度量](./media/luis-usage-tiers/metrics-default.png)

您可以設定時間週期和計量類型的計量圖表。 

![自訂度量](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>交易總數閾值警示
如果您想要知道何時達到特定交易閾值 (例如 10,000 筆交易) 時，可以建立警示。 

![預設警示](./media/luis-usage-tiers/alert-default.png)

為一段時間的**呼叫總數**計量新增計量警示。 新增應收到警示的所有人員電子郵件地址。 為應收到警示的所有系統新增 Webhook。 您也可以在警示觸發時執行邏輯應用程式。 

## <a name="next-steps"></a>後續步驟

了解如何使用[版本](luis-how-to-manage-versions.md)來管理 LUIS 應用程式的變更。
