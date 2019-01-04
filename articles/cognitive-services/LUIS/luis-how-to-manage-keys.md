---
title: 管理金鑰
titleSuffix: Language Understanding - Azure Cognitive Services
description: 在 Azure 入口網站中建立 LUIS 端點金鑰之後，將金鑰指派給 LUIS 應用程式並取得正確的端點 URL。 使用此端點 URL 來取得 LUIS 預測。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088097"
---
# <a name="add-an-azure-luis-resource-to-app"></a>將 Azure LUIS 資源新增至應用程式

在 Azure 入口網站中建立 LUIS 資源之後，將資源指派給 LUIS 應用程式並取得正確的端點 URL。 使用此端點 URL 來取得 LUIS 預測。

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


## <a name="assign-resource-in-luis-portal"></a>在 LUIS 入口網站中指派資源

1. 在 [Azure 入口網站](https://portal.azure.com)上建立 LUIS 金鑰。 如需進一步的指示，請參閱[使用 Azure 建立端點金鑰](luis-how-to-azure-subscription.md)。
 
2. 選取右上方功能表中的 [管理]，然後選取 [金鑰和端點]。

    [ ![金鑰和端點頁面](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. 若要新增 LUIS，請選取 [指派資源 +]。

    ![將資源指派給應用程式](./media/luis-manage-keys/assign-key.png)

4. 在與您用來登入 LUIS 網站的電子郵件地址相關聯的對話方塊中選取租用戶。  

5. 選擇與您要新增的 Azure 資源相關聯的 [訂用帳戶名稱]。

6. 選取 [LUIS 資源名稱]。 

7. 選取 [指派資源]。 

8. 在資料表中尋找新的資料列並複製端點 URL。 以正確方式建構，可對 LUIS 端點提出 HTTP GET 要求進行預測。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>取消指派資源
當您取消指派端點金鑰時，它並不會從 Azure 中刪除。 只會從 LUIS 取消連結。 

若未指定端點金鑰，或未指派給應用程式，任何對端點 URL 的要求都會傳回錯誤：`401 This application cannot be accessed with the current subscription`。 

### <a name="include-all-predicted-intent-scores"></a>Include all predicted intent scores \(包括所有預測意圖分數\)
[Include all predicted intent scores] \(包括所有預測意圖分數\) 核取方塊允許端點查詢回應包括每個意圖的預測分數。 

此設定可讓您的聊天機器人或 LUIS 通話應用程式根據所傳回意圖的分數進行程式設計決策。 一般而言，前兩個意圖最為有趣。 若最高分數為 None 意圖，則您的聊天機器人可以選擇詢問待處理問題，明確選擇 None 意圖與其他高計分意圖。 

這些意圖和其分數也會包括在端點記錄檔中。 您可以[匯出](luis-how-to-start-new-app.md#export-app)這些記錄，並分析分數。 

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
在 [端點 URL 設定] 中，[Bing 拼字檢查工具] 切換開關會允許 LUIS 在預測之前更正錯字。 建立 **[Bing 拼字檢查金鑰](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**。 

新增 **spellCheck=true** 查詢字串參數和 **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**。 將 `{YOUR_BING_KEY_HERE}` 取代為 Bing 拼字檢查金鑰。

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

    |頁首|值|
    |--|--|
    |`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。| 
    |`Ocp-Apim-Subscription-Key`|您的[撰寫金鑰](luis-how-to-account-settings.md)。|

    此 API 會傳回您 LUIS 訂用帳戶的 JSON 物件陣列，其中包含訂用帳戶識別碼、資源群組和資源名稱 (傳回作為帳戶名稱)。 請在此陣列中找出要指派給 LUIS 應用程式的 LUIS 資源項目。 

1. 使用[將 LUIS Azure 帳戶指派給應用程式](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API，將權杖指派給 LUIS 資源。 

    此 POST API 需要下列設定︰

    |類型|設定|值|
    |--|--|--|
    |頁首|`Authorization`|`Authorization` 的值為 `Bearer {token}`。 請注意，權杖值的開頭必須加上 `Bearer` 一字和空格。|
    |頁首|`Ocp-Apim-Subscription-Key`|您的[撰寫金鑰](luis-how-to-account-settings.md)。|
    |頁首|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS 應用程式識別碼。 
    |body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    此 API 成功執行時，會傳回「201 - 已建立」狀態。 

## <a name="next-steps"></a>後續步驟

在 [發佈應用程式] 頁面中，使用您的金鑰發佈應用程式。 如需發佈的指示，請參閱[發佈應用程式](luis-how-to-publish-app.md)。

請參閱 [LUIS 中的金鑰](luis-concept-keys.md)以了解 LUIS 編寫和端點金鑰概念。
