---
title: Azure 媒體服務 v3 SDK - Azure
description: 本文概述如何使用 SDK，透過媒體服務 v3 API 開始進行開發。
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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564503"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>使用 SDK 對媒體服務 v3 API 進行開發

身為開發人員，您可以使用媒體服務 [REST API](https://aka.ms/ams-v3-rest-ref)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 [媒體服務 v3](https://aka.ms/ams-v3-rest-sdk) API 以 OpenAPI 規格 (先前稱為 Swagger) 作為基礎。

> [!NOTE]
> Azure 媒體服務 v3 SDK 不一定是安全執行緒。 在開發多執行緒應用程式時，您應新增本身的執行緒同步處理邏輯以保護用戶端，或為每個執行緒使用新的 AzureMediaServicesClient 物件。 您也應留意程式碼提供給用戶端 (例如 .NET 中的 HttpClient 執行個體) 的選擇性物件所引起的多執行緒處理問題。

本主題會提供 SDK、工具和操作指南的連結。

## <a name="prerequisites"></a>必要條件

若要開始針對媒體服務進行開發，您需要：

- 有效的 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 。
- [了解基本概念](concepts-overview.md)
- 檢閱[透過媒體服務 v3 API 開始進行開發](media-services-apis-overview.md)
- [建立媒體服務帳戶 - CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>開始使用 SDK 開發

### <a name="net"></a>.NET

使用 [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk) [連線到媒體服務](configure-connect-dotnet-howto.md)。

探索媒體服務 [.NET 參考](https://aka.ms/ams-v3-dotnet-ref)文件。

### <a name="java"></a>Java

使用 [Java SDK](https://aka.ms/ams-v3-java-sdk) [連線到媒體服務](configure-connect-java-howto.md)。

檢閱媒體服務 [Java 參考](https://aka.ms/ams-v3-java-ref)文件。

### <a name="nodejs"></a>Node.js

使用 [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) [連線到媒體服務](configure-connect-nodejs-howto.md)。

探索媒體服務 [Node.js 參考](https://aka.ms/ams-v3-nodejs-ref)文件，並查看[範例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)示範如何搭配使用媒體服務 API 與 Node.js。

### <a name="python"></a>Python

使用 [Python SDK](https://aka.ms/ams-v3-python-sdk)。

檢閱媒體服務 [Python 參考](https://aka.ms/ams-v3-python-ref)文件。

### <a name="go"></a>Go

使用 [Go SDK](https://aka.ms/ams-v3-go-sdk)。

檢閱媒體服務 [Go 參考](https://aka.ms/ams-v3-go-ref)文件。

### <a name="ruby"></a>Ruby

使用 [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)。

## <a name="azure-media-services-explorer"></a>Azure 媒體服務總管

[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是想要了解媒體服務的 Windows 客戶可用的工具。 AMSE 是 Winforms/C# 應用程式，可利用媒體服務上傳、下載、編碼、串流 VOD 和即時內容。 AMSE 工具適用於想要測試媒體服務，而不要撰寫任何程式碼的用戶端。 AMSE 程式碼會當作資源提供給想要使用媒體服務開發的客戶。

AMSE 是一個開放原始碼專案，由社群提供支援 (可將問題回報給 https://github.com/Azure/Azure-Media-Services-Explorer/issues)。 此專案採用 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱[管理辦法常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)，如有任何其他問題或意見請連絡 opencode@microsoft.com。

## <a name="next-steps"></a>後續步驟

[概觀](media-services-overview.md)
