---
title: Azure 媒體服務 v3 SDK - Azure
description: 本文概述如何使用 SDK/工具，透過媒體服務 v3 API 開始進行開發。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/22/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: b9c89b6cb6dbc6cb217be671eb0e52c0416811ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889840"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>使用 SDK/工具透過媒體服務 v3 API 開始進行開發

身為開發人員，您可以使用媒體服務 [REST API](https://aka.ms/ams-v3-rest-ref)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 [媒體服務 v3](https://aka.ms/ams-v3-rest-sdk) API 以 OpenAPI 規格 (先前稱為 Swagger) 作為基礎。

本主題會提供 SDK、工具和文件的連結。 也會提供一些適用於不同開發環境的實用資訊。

## <a name="prerequisites"></a>必要條件

若要開始針對媒體服務進行開發，您需要：

- 有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。
- [建立媒體服務帳戶](create-account-cli-how-to.md)

## <a name="start-developing"></a>開始開發

Azure 媒體服務支援下列 SDK/工具 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure 媒體服務總管

[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是想要了解媒體服務的 Windows 客戶可用的工具。 AMSE 是 Winforms/C# 應用程式，可利用媒體服務上傳、下載、編碼、串流 VOD 和即時內容。 AMSE 工具適用於想要測試媒體服務，而不要撰寫任何程式碼的用戶端。 AMSE 程式碼會當作資源提供給想要使用媒體服務開發的客戶。

AMSE 是一個開放原始碼專案，由社群提供支援 (可將問題回報給 https://github.com/Azure/Azure-Media-Services-Explorer/issues)。 此專案採用 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱[管理辦法常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)，如有任何其他問題或意見請連絡 opencode@microsoft.com。

## <a name="rest"></a>REST

若要開始使用媒體服務 REST API 進行開發，請安裝 REST 用戶端。 例如，**Postman**、具有 REST 外掛程式的 **Visual Studio Code**，或 **Telerik Fiddler**。 我們會使用 [Postman](media-rest-apis-with-postman.md) 來示範媒體服務 v3 範例。

請探索媒體服務 [REST API 參考](https://aka.ms/ams-v3-rest-ref)文件，並看看這些範例：

- [教學課程：編碼以 URL 為基礎的遠端檔案及串流處理影片 - REST](stream-files-tutorial-with-rest.md)
- [將檔案上傳至媒體服務帳戶 - REST](upload-files-rest-how-to.md)
- [使用媒體服務建立篩選 - REST](filters-dynamic-manifest-rest-howto.md)
- [以 Azure Resource Manager 為基礎的 REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

請探索媒體服務 [Azure CLI 參考](https://aka.ms/ams-v3-cli-ref)文件，並看看這些範例：

- [調整媒體保留單元 - CLI](media-reserved-units-cli-how-to.md)
- [建立媒體服務帳戶 - CLI](./scripts/cli-create-account.md) 
- [重設帳戶認證 - CLI](./scripts/cli-reset-account-credentials.md)
- [建立資產 - CLI](./scripts/cli-create-asset.md)
- [上傳檔案 - CLI](./scripts/cli-upload-file-asset.md)
- [建立轉換 - CLI](./scripts/cli-create-transform.md)
- [建立作業 - CLI](./scripts/cli-create-jobs.md)
- [建立 EventGrid - CLI](./scripts/cli-create-event-grid.md)
- [發佈資產 - CLI](./scripts/cli-publish-asset.md)
- [篩選 - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

請探索媒體服務 [.NET 參考](https://aka.ms/ams-v3-dotnet-ref)文件，並看看這些範例：

- [教學課程：上傳、編碼和串流影片 - .NET](stream-files-tutorial-with-api.md) 
- [教學課程：透過媒體服務 v3 進行即時串流 - .NET](stream-live-tutorial-with-api.md)
- [教學課程：透過媒體服務 v3 分析影片 - .NET](analyze-videos-tutorial-with-api.md)
- [從本機檔案建立作業輸入 - .NET](job-input-from-local-file-how-to.md)
- [從 HTTPS URL 建立作業輸入 - .NET](job-input-from-http-how-to.md)
- [使用自訂轉換進行編碼 - .NET](customize-encoder-presets-how-to.md)
- [使用 AES-128 動態加密和金鑰傳遞服務 - .NET](protect-with-aes128.md)
- [使用 DRM 動態加密與授權傳遞服務 - .NET](protect-with-drm.md)
- [從現有原則取得簽署金鑰 - .NET](get-content-key-policy-dotnet-howto.md)
- [使用媒體服務建立篩選 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [搭配使用 Azure Functions v2 與媒體服務 v3 的進階影片隨選範例](https://aka.ms/ams3functions)

## <a name="java"></a>Java

檢閱媒體服務 [Java 參考](https://aka.ms/ams-v3-java-ref)文件。

## <a name="nodejs"></a>Node.js

探索媒體服務 [Node.js 參考](https://aka.ms/ams-v3-nodejs-ref)文件，並查看[範例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)示範如何搭配使用媒體服務 API 與 Node.js。

## <a name="python"></a>Python

檢閱媒體服務 [Python 參考](https://aka.ms/ams-v3-python-ref)文件。

## <a name="go"></a>Go

檢閱媒體服務 [Go 參考](https://aka.ms/ams-v3-go-ref)文件。

## <a name="next-steps"></a>後續步驟

- [建立帳戶 - CLI](create-account-cli-how-to.md)
- [存取 API - CLI](access-api-cli-how-to.md)

