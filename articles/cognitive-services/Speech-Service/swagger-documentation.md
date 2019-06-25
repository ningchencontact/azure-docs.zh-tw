---
title: Swagger 文件 - 語音服務
titleSuffix: Azure Cognitive Services
description: Swagger 文件可用於為多種程式設計語言自動產生 SDK。 Swagger 支援我們服務中的所有作業
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 04/12/2019
ms.author: erhopf
ms.openlocfilehash: 6cf3ab6480900aa763598120e6ff7e308f5044e1
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743212"
---
# <a name="swagger-documentation"></a>Swagger 文件

語音服務提供了一個 Swagger 規格，用於與少數 REST API 互動，這些 REST API 用來匯入資料、建立模型、測試模型精確度、建立自訂端點、排入批次轉譯佇列和管理訂用帳戶。 透過自訂語音入口網站提供的大部分作業都可以使用這些 API 以程式設計方式來完成。 

> [!NOTE]
> 支援語音轉換文字和文字轉換語音的作業作為 REST API，然後在 Swagger 規格中進行記錄。

## <a name="generating-code-from-the-swagger-specification"></a>從 Swagger 規格產生程式碼

[Swagger 規格](https://cris.ai/swagger/ui/index)具有可讓您快速測試各種路徑的選項。 但是，有時需要為所有路徑產生程式碼，建立一個未來解決方案可以作為基礎的單一呼叫程式庫。 讓我們看看產生 Python 程式庫的流程。

您需要將 Swagger 設定為與語音服務訂用帳戶相同的區域。 您可以在 [語音服務] 資源下的 Azure 入口網站中確認您的區域。 如需支援的區域的完整清單，請參閱[區域](regions.md)。

1. 移至 https://editor.swagger.io 。
2. 按一下 [檔案]  ，然後按一下 [匯入] 
3. 輸入 swagger URL，包括語音服務訂用帳戶的區域 `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. 按一下 [產生用戶端]  ，然後選取 [Python]
5. 儲存用戶端程式庫

您可以使用[在 GitHub 上使用語音服務範例](https://aka.ms/csspeech/samples)產生的 Python 程式庫。

## <a name="reference-docs"></a>參考文件

* [REST (Swagger)：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)

## <a name="next-steps"></a>後續步驟

* [GitHub 上的語音服務範例](https://aka.ms/csspeech/samples)。
* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
