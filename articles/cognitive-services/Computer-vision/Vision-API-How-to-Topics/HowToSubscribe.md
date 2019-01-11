---
title: 取得訂用帳戶金鑰 - 電腦視覺
titlesuffix: Azure Cognitive Services
description: 了解如何取得訂用帳戶金鑰以呼叫 Azure 認知服務中的電腦視覺 API。
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 820531cc2254d9cbc7aaf7e758dd0457b282d892
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580802"
---
# <a name="how-to-obtain-subscription-keys"></a>如何取得訂用帳戶金鑰

電腦視覺服務需要特殊的訂用帳戶金鑰。 每次呼叫電腦視覺 API 時，都需要訂用帳戶金鑰。 這個金鑰必須透過查詢字串參數傳遞，或是在要求標頭中指定。

若要註冊訂用帳戶金鑰，請參閱[訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)。 註冊完全免費。 這些服務的定價隨時會變動。

>[!NOTE]
您的訂用帳戶金鑰僅對於其中一個 [Microsoft Azure 區域](https://azure.microsoft.com/regions/)有效。 

| 區域 | 位址 |
|---|---|
| 美國西部 | westus.api.cognitive.microsoft.com |
| 美國東部 2 | eastus2.api.cognitive.microsoft.com |
| 美國中西部 | westcentralus.api.cognitive.microsoft.com |
| 西歐 | westeurope.api.cognitive.microsoft.com |
| 東南亞 | southeastasia.api.cognitive.microsoft.com |

如果您使用電腦視覺免費試用版註冊，您的訂用帳戶對於 **westcentral**區域 (`https://westcentralus.api.cognitive.microsoft.com/`) 有效。 這是最常見的案例。 不過，如果您透過 [https://azure.microsoft.com/](https://azure.microsoft.com/) 網站使用您的 Microsoft Azure 帳戶註冊電腦視覺，您可以從先前的區域清單中指定試用的區域。

例如，如果您使用 Microsoft Azure 帳戶註冊電腦視覺，而且對於您的區域指定 `westus`，您必須對於 REST API 呼叫 (`https://westus.api.cognitive.microsoft.com/`) 使用 `westus` 區域。

取得您的試用版金鑰之後，如果您忘記訂用帳戶金鑰的區域，可以在 [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/) 找到您的區域。

### <a name="related-links"></a>相關連結：

* [Azure 認知服務 API 的定價選項](https://azure.microsoft.com/pricing/details/cognitive-services/)
