---
title: Azure 搜尋服務2019-05-06 的預覽 REST API-Preview-Azure 搜尋服務
description: Azure 搜尋服務服務 REST API 版本 2019-05-06-Preview 包含實驗性功能, 例如知識存放區和客戶管理的加密金鑰。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 29d079c4e21352ced5fdcde44acaee66b79f6af9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876647"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure 搜尋服務服務 REST api-版本 2019-05-06-預覽
本文描述 Azure 搜尋服務 REST API 的 `api-version=2019-05-06-Preview` 版本，並提供下列尚未提供的實驗性功能。

> [!NOTE]
> 預覽功能適用於目標為收集意見反應的測試和實驗，而且可能會有所變更。 我們強烈建議您避免在實際執行的應用程式中使用預覽 API。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-Preview 的新功能

[**知識存放區**](knowledge-store-concept-intro.md)是以 AI 為基礎的擴充管線的新目的地。 除了索引之外, 您現在還可以保存在 Azure 儲存體中編制索引期間所建立的已填入資料結構。 您可以透過技能集中的專案來控制資料的實體結構, 包括資料的成形方式、資料是否儲存在資料表儲存體或 Blob 儲存體中, 以及是否有多個視圖。

服務端的[**客戶管理加密金鑰**](search-security-manage-encryption-keys.md)-待用加密也是新的預覽功能。 除了由 Microsoft 所管理的內建待用加密之外, 您還可以套用額外的加密層級, 其中您是金鑰的唯一擁有者。

## <a name="other-preview-features"></a>其他預覽功能

舊版預覽中宣布的功能仍處於公開預覽。 如果您使用舊版預覽 api-version 呼叫 API，則可以繼續使用該版本或切換到 `2019-05-06-Preview`，而不會更改預期的行為。

+ [moreLikeThis 查詢參數](search-more-like-this.md)，可尋找與特定文件相關的文件。 這個功能已存在舊版預覽中。 
* [CSV blob 索引](search-howto-index-csv-blobs.md)會建立一行檔, 而不是每個文字 blob 一份檔。
* [Cosmos DB 索引子的 MONGODB API 支援](search-howto-index-cosmosdb.md)目前處於預覽階段。


## <a name="how-to-call-a-preview-api"></a>如何呼叫預覽 API

較舊的預覽仍可運作，但是經過一段時間後會變成過時。 如果您的程式碼呼叫 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些呼叫都仍然有效。 但是，只有最新預覽版本會重新整理改進功能。 

下列的範例語法說明對預覽 API 版本的呼叫。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 搜尋服務可以在多個版本中使用。 如需詳細資訊，請參閱 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 搜尋服務服務 REST API 參考檔。 如果您遇到問題, 請向我們尋求[StackOverflow](https://stackoverflow.com/)或[連絡人支援](https://azure.microsoft.com/support/community/?product=search)的協助。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)