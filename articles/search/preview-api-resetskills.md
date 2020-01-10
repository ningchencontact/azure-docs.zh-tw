---
title: 重設技能（api 版本 = 2019-050 06-01.5.1-預覽）
titleSuffix: Azure Cognitive Search
description: 當您需要技能集的完整或部分重新處理時，預覽 REST API 用於增量擴充。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832165"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>重設技能（api 版本 = 2019-05 06-01.5.1-Preview）

> [!IMPORTANT] 
> 增量擴充目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

「**重設技能**」要求會指定下一個索引子執行時要處理哪些技能。 對於已啟用快取的索引子，您可以針對索引子無法偵測到的技能更新明確要求處理。 例如，如果您進行外部變更（例如自訂技能的修訂），您可以使用此 API 來重新執行技能。 系統會根據快取中的可重複使用資料，以及每項更新的技能的新內容，來重新整理輸出，例如知識存放區或搜尋索引。

您可以使用 HTTP PUT 來重設現有的[技能集](https://docs.microsoft.com/rest/api/searchservice/create-skillset)，並在要求 URI 上指定要更新的技能集名稱。 您必須在要求上使用**api 版本 = 2019-05 06-01.5.1-Preview** 。

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

或者，使用 POST，並將索引子名稱放在要求的主體中：

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>要求標頭  

 下表說明必要及選用的要求標頭。  

|要求標頭|說明|  
|--------------------|-----------------|  
|*Content-Type:*|必要。 請設為 `application/json`|  
|*api-key:*|必要。 `api-key` 可用來驗證搜尋服務的要求。 它是服務專屬的唯一字串值。 **重設技能集**要求必須包含設為您系統管理金鑰（相對於查詢金鑰）的 `api-key` 標頭。|  

您也必須提供服務名稱，才能建構要求 URL。 您可以從 Azure 入口網站中的服務 [總覽] 頁面取得服務名稱和 `api-key`。 如需頁面導覽說明，請參閱[建立 Azure 認知搜尋服務](https://docs.microsoft.com/azure/search/search-create-service-portal)。  

## <a name="request-body-syntax"></a>要求本文語法  

要求的主體是技能名稱的陣列。

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>回應  

狀態碼：回應成功時會傳回「204 沒有內容」。 

## <a name="see-also"></a>請參閱

+ [搜尋 REST Api](https://docs.microsoft.com/rest/api/searchservice)
+ [HTTP 狀態碼](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [AI 擴充總覽](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [設定快取和增量擴充](search-howto-incremental-index.md)
+ [累加擴充](cognitive-search-incremental-indexing-conceptual.md)