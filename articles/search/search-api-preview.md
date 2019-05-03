---
title: 預覽 REST API for Azure 搜尋 2019年-05-06-Preview-Azure 搜尋服務
description: Azure 搜尋服務 REST API 版本 2019年-05-06-Preview 包含客戶管理的加密金鑰等知識的存放區的實驗性功能。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 91c58507d8758a65772110afba71354deecd3b12
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024275"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure 搜尋服務 REST api 版本 2019年-05-06-Preview
本文描述 Azure 搜尋服務 REST API 的 `api-version=2019-05-06-Preview` 版本，並提供下列尚未提供的實驗性功能。

> [!NOTE]
> 預覽功能適用於目標為收集意見反應的測試和實驗，而且可能會有所變更。 我們強烈建議您避免在實際執行的應用程式中使用預覽 API。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-Preview 的新功能

[**知識存放區**](knowledge-store-concept-intro.md)是以 AI 為基礎的擴充管線的新的目的地。 除了索引，您現在可以保存在 Azure 儲存體編製索引期間建立的擴展的資料結構。 您可以控制您的技能組合，包括資料的成形，資料是儲存在資料表儲存體或 Blob 儲存體，以及是否有多個檢視中的項目中資料的實體結構。

[**客戶管理的加密金鑰**](search-security-manage-encryption-keys.md)服務端加密待用也是新的預覽功能。 除了內建-待用加密由 Microsoft 管理，您可以套用額外的所在索引鍵的唯一擁有者的加密。

## <a name="other-preview-features"></a>其他預覽功能

舊版預覽中宣布的功能仍處於公開預覽。 如果您使用舊版預覽 api-version 呼叫 API，則可以繼續使用該版本或切換到 `2019-05-06-Preview`，而不會更改預期的行為。

+ [moreLikeThis 查詢參數](search-more-like-this.md)，可尋找與特定文件相關的文件。 這個功能已存在舊版預覽中。 


## <a name="how-to-call-a-preview-api"></a>如何呼叫預覽 API

較舊的預覽仍可運作，但是經過一段時間後會變成過時。 如果您的程式碼呼叫 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些呼叫都仍然有效。 但是，只有最新預覽版本會重新整理改進功能。 

下列的範例語法說明對預覽 API 版本的呼叫。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 搜尋服務可以在多個版本中使用。 如需詳細資訊，請參閱 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>後續步驟

檢閱 Azure 搜尋服務 REST API 參考文件。 如果您遇到問題，說明上尋求[StackOverflow](https://stackoverflow.com/)或是[連絡支援人員](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜尋服務 REST API 參考](https://docs.microsoft.com/rest/api/searchservice/)