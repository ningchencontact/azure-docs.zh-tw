---
title: Azure 搜尋服務 REST API 版本 2017-11-11-Preview | Microsoft Docs
description: Azure 搜尋服務 REST API 版本 2017-11-11-Preview 包含同義字和 moreLikeThis 搜尋等實驗性功能。
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2018
ms.author: HeidiSteen
ms.openlocfilehash: 3a9ff6697357dec443691b261ae6fc0477603a9c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786717"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Azure 搜尋服務 REST API：版本 2017-11-11-Preview
本文描述 Azure 搜尋服務 REST API 的 `api-version=2017-11-11-Preview` 版本，並提供下列實驗性功能：

+ [認知搜尋](cognitive-search-concept-intro.md)是 Azure 搜尋索引中的新擴充功能，可在非文字來源和無差異文字中尋找潛在資訊，並將其轉換成 Azure 搜尋服務中可全文檢索搜尋的內容。

預覽 API 相當於正式推出的 API，但下列兩項作業除外：

+ [建立技能集 (api-version=2017-11-11-Preview)](ref-create-skillset.md)
+ [建立索引子 (api-version=2017-11-11-Preview)](ref-create-indexer.md)

請務必在評估發行前版本功能時，以預覽 API 版本 `api-version=2017-11-11-Preview` 為目標。 下列的範例語法說明對預覽 API 版本的呼叫。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

> [!NOTE]
> 預覽功能適用於目標為收集意見反應的測試和實驗，而且可能會有所變更。 **我們強烈建議您避免在實際執行的應用程式中使用預覽 API。**

Azure 搜尋服務可以在多個版本中使用。 如需詳細資料，請參閱 [API 版本](search-api-versions.md)。
