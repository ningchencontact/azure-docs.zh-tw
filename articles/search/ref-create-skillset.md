---
title: 建立技能集 (REST api-version=2017-11-11-Preivew) - Azure 搜尋服務 | Microsoft Docs
description: 技能集是構成擴充管線的認知技術集合。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786907"
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>建立技能集 (api-version=2017-11-11-Preview)

**適用範圍：** api-version-2017-11-11-Preview

技能集是用於自然語言處理和其他轉換的認知技術集合。 這些技能包括具名實體擷取、關鍵片語擷取、將文字區塊處理成邏輯分頁等等。

若要使用技能集，請在 Azure 搜尋服務索引子中參考，然後執行索引子以匯入資料、叫用轉換和擴充，並將輸出欄位對應到索引。 技能集是高層級的資源，但只能在索引子處理內運作。 因為是高層級的資源，所以您可以設計一次技能集，然後在多個索引子中參考。 

技能集在 Azure 搜尋服務中透過 HTTP PUT 或 POST 要求來表示。 如果使用 PUT，則要求的本文是指定所要叫用技能的 JSON 結構描述。 技能會透過輸入輸出關聯而鏈結在一起，其中某個轉換的輸出會變成另一個轉換的輸入。

技能集至少必須有一個技能。 技能的最大數目理論上沒有限制，但是三到五個是常見的設定。  


> [!NOTE]
> 認知搜尋為公開預覽，且目前免費提供技能集執行。 我們將在不久後宣布此功能的定價。

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>要求  
 所有服務要求都需使用 HTTPS。 **建立技能集**要求可以使用 PUT 方法 (技能集名稱是 URL 一部分) 來建構。 如果沒有技能集，便會加以建立。 如果已有索引，就會使用新的定義來更新。 請注意，使用 PUT 時，一次只能建立一個技能集。  

 技能集名稱必須符合下列要求：

- 小寫
- 以字母或數字為開頭和結尾
- 沒有斜線或點
- 少於 128 個字元 

將技能集名稱的開頭設為字母或數字之後，名稱的其餘部分就能包含任意字母、數字及破折號 (但不可為連續破折號)。  

需要 **api-version** 參數。 唯一可用的版本是 `2017-11-11-Preview`。 請參閱 [Azure 搜尋中的 API 版本](https://go.microsoft.com/fwlink/?linkid=834796)，以取得所有版本的清單。 


### <a name="request-headers"></a>要求標頭  

 下表說明必要及選用的要求標頭。  

|要求標頭|說明|  
|--------------------|-----------------|  
|*Content-Type:*|必要。 請設為 `application/json`|  
|*api-key:*|必要。 `api-key` 可用來驗證搜尋服務的要求。 它是服務專屬的唯一字串值。 **建立技能集**要求必須包含已設為系統管理金鑰 (相對於查詢金鑰) 的 `api-key` 標頭。|  

您也必須提供服務名稱，才能建構要求 URL。 您可以透過 Azure 入口網站的服務儀表板，以取得服務名稱和 `api-key`。 如需頁面導覽說明，請參閱 [在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md) 。  

### <a name="request-body-syntax"></a>要求本文語法  

要求的本文包含技能集定義。定義是由一或多個完整指定的技能，以及選擇性的名稱和描述參數所組成。  

下列為建構要求承載的語法。 本文稍後會提供範例要求，此要求也位於[如何定義技能集](cognitive-search-defining-skillset.md)中。  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>要求範例
 下列範例會建立用於擴充財務文件集合的技能集。

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

要求的本文是 JSON 文件。 這個特定的技能集以非同步方式使用兩種技能，將 `contents` 的內容獨立處理為兩個不同的轉換。 或者，您可以將某個轉換的輸出導向為另一個轉換的輸入。 如需詳細資訊，請參閱[如何定義技能集](cognitive-search-defining-skillset.md)。

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>回應  

 若要求成功，應該會看到狀態碼 "201 Created"。  

 根據預設，回應本文將包含所建立技能集定義的 JSON。 不過，如果將 Prefer 要求標頭設為 return=minimal，回應本文將會空白，而成功狀態碼會是 "204 No Content"，而不是 "201 Created"。 無論使用 PUT 或 POST 建立技能集，都會發生此情況。   

## <a name="see-also"></a>另請參閱

+ [認知搜尋概觀](cognitive-search-concept-intro.md)
+ [快速入門：試用認知搜尋](cognitive-search-quickstart-blob.md)
+ [教學課程：擴充 Azure blob 的編製索引](cognitive-search-tutorial-blob.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md)
+ [如何對應欄位](cognitive-search-output-field-mapping.md)
+ [如何定義自訂介面](cognitive-search-custom-skill-interface.md)
+ [範例：建立自訂技能](cognitive-search-create-custom-skill-example.md)
+ [預先定義的技能](cognitive-search-predefined-skills.md)