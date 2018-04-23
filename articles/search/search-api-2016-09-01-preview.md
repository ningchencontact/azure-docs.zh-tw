---
title: Azure 搜尋服務 REST API 版本 2016-09-01-Preview | Microsoft Docs
description: Azure 搜尋服務 REST API 版本 2016-09-01-Preview 包含同義字和 moreLikeThis 搜尋等實驗性功能。
author: mhko
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: bbfdb2641d68f1262cc6293462c7022701357374
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure 搜尋服務 REST API：版本 2016-09-01-Preview
本文是 `api-version=2016-09-01-Preview`的參考文件。 這個預覽版本可藉由提供下列實驗性功能，來擴充公開上市版本 [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx)：

* [同義字 API](search-synonyms.md)，可上傳同義字對應並擴大搜尋。
* [`moreLikeThis` 查詢參數](search-more-like-this.md)，可尋找與特定文件相關的文件。

請確定以預覽 API 版本 `api-version=2016-09-01-Preview` 為目標，嘗試這些實驗性功能。 下列範例說明如何在提出 more-like-this 查詢時，指定預覽 API 版本。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> 預覽功能適用於目標為收集意見反應的測試和實驗，而且可能會有所變更。 **我們強烈建議您避免在實際執行的應用程式中使用預覽 API。**

Azure 搜尋服務可以在多個版本中使用。 如需詳細資訊，請參閱 [搜尋服務版本設定](http://msdn.microsoft.com/library/azure/dn864560.aspx) 。
