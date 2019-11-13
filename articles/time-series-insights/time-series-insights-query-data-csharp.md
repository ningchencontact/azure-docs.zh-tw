---
title: 使用C#程式碼 AZURE 時間序列深入解析從 GA 環境查詢資料 |Microsoft Docs
description: 瞭解如何使用以撰寫的自訂應用程式從 Azure 時間序列深入解析環境查詢資料C#。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: 5946a1484a62b041772f388b0629d131afe37b92
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012554"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>使用從 Azure 時間序列深入解析 GA 環境查詢資料C#

這個C#範例示範如何從 Azure 時間序列深入解析 GA 環境查詢資料。

範例會顯示查詢 API 使用方式的數個基本範例︰

1. 在準備步驟中，透過 Azure Active Directory API 取得存取權杖。 在每個「查詢」API 要求的 `Authorization` 標頭中傳遞此權杖。 若要了解如何設定非互動式應用程式，請參閱[驗證與授權](time-series-insights-authentication-and-authorization.md)。 此外，請務必正確設定在此範例開頭定義的所有常數。
1. 取得使用者可存取的環境清單。 挑選其中一個環境作為使用環境，並針對此環境查詢進一步資料。
1. 在 HTTPS 要求的範例中，要求感興趣環境的可用性資料。
1. 在 Web 通訊端要求的範例中，要求感興趣環境的事件彙總資料。 要求整個可用性時間範圍內的資料。

> [!NOTE]
> 您可以在[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)取得範例程式碼。

## <a name="project-dependencies"></a>專案相依性

新增 `Microsoft.IdentityModel.Clients.ActiveDirectory` 和 `Newtonsoft.Json`的 NuGet 套件。

## <a name="c-example"></a>C# 範例

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解查詢，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 閱讀如何[使用用戶端 SDK 將 JavaScript 應用程式連接](https://github.com/microsoft/tsiclient)到時間序列深入解析。