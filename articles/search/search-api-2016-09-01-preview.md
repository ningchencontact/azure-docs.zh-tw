---
title: Azure 搜尋服務 REST API 版本 2016-09-01-Preview | Microsoft Docs
description: Azure 搜尋服務 REST API 版本 2016-09-01-Preview 包含 moreLikeThis 搜尋等實驗性功能。
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181618"
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure 搜尋服務 REST API：版本 2016-09-01-Preview
本文是 `api-version=2016-09-01-Preview`的參考文件。 這個預覽版本可藉由提供下列實驗性功能，來擴充公開上市版本 [api-version=2016-09-01](https://docs.microsoft.com/rest/api/searchservice)：

* [`moreLikeThis` 查詢參數](search-more-like-this.md)，可尋找與特定文件相關的文件。

請確定以預覽 API 版本 `api-version=2016-09-01-Preview` 為目標，嘗試這些實驗性功能。 下列範例說明如何在提出 more-like-this 查詢時，指定預覽 API 版本。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> 預覽功能適用於目標為收集意見反應的測試和實驗，而且可能會有所變更。 **我們強烈建議您避免在實際執行的應用程式中使用預覽 API。**

Azure 搜尋服務可以在多個版本中使用。 如需詳細資訊，請參閱 [搜尋服務版本設定](https://docs.microsoft.com/azure/search/search-api-versions) 。
