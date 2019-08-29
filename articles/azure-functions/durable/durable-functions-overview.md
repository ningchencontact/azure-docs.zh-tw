---
title: Durable Functions 概觀 - Azure
description: Azure Functions 的 Durable Functions 擴充簡介。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 323ab530d8199dd154e5d3568c09f86f6f52d702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087201"
---
# <a name="what-are-durable-functions"></a>Durable Functions 是什麼？

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。

## <a name="benefits"></a>優點

此擴充功能可讓您使用[*協調器函式*](durable-functions-types-features-overview.md#orchestrator-functions)來定義具狀態工作流程，其可提供下列優勢：

* 您可以在程式碼中定義工作流程。 不需要 JSON 結構描述或設計工具。
* 能以同步和非同步方式呼叫其他函式。 所呼叫之函式的輸出可儲存至本機變數。
* 當函式進行等候時，會自動設定其進度的檢查點。 處理序回收或 VM 重新開機時，本機狀態決不會消失。

## <a name="application-patterns"></a>應用程式模式

Durable Functions 主要用來簡化無伺服器應用程式中複雜的具狀態協調需求。 下列是一些可受惠於 Durable Functions 的典型應用程式模式：

* [鏈結](durable-functions-concepts.md#chaining)
* [展開傳送/收合傳送](durable-functions-concepts.md#fan-in-out)
* [非同步的 HTTP API](durable-functions-concepts.md#async-http)
* [監視](durable-functions-concepts.md#monitoring)
* [人為互動](durable-functions-concepts.md#human)

## <a name="language-support"></a>支援的語言

Durable Functions 目前支援下列語言：

* **C#** ：[預先編譯的類別庫](../functions-dotnet-class-library.md)和 [C# 指令碼](../functions-reference-csharp.md)。
* **F#** ：預先編譯的類別庫和 F# 指令碼。 只有 1.x 版的 Azure Functions 執行階段才支援 F# 指令碼。
* **JavaScript**：只有 2.x 版的 Azure Functions 執行階段才支援。 需要 1.7.0 版的 Durable Functions 擴充功能，或更新版本。 

Durable Functions 的目標是支援所有的 [Azure Functions 語言](../supported-languages.md)。 請參閱 [Durable Functions 問題清單](https://github.com/Azure/azure-functions-durable-extension/issues)，以取得最新工作狀態來支援其他語言。

如同 Azure Functions，有一些範本可協助您使用 [Visual Studio 2019](durable-functions-create-first-csharp.md)、[Visual Studio Code](quickstart-js-vscode.md) 和 [Azure 入口網站](durable-functions-create-portal.md)開發 Durable Functions。

## <a name="billing"></a>計費

Durable Functions 的收費方式與 Azure Functions 相同。 如需詳細資訊，請參閱 [Azure Functions 價格](https://azure.microsoft.com/pricing/details/functions/)。

## <a name="jump-right-in"></a>立即參與

完成下列其中一個特定語言快速入門教學課程，即可在 10 分鐘內開始使用 Durable Functions：

* [使用 Visual Studio 2019 的 C#](durable-functions-create-first-csharp.md)
* [使用 Visual Studio Code 的 JavaScript](quickstart-js-vscode.md)

在這兩個快速入門中，您會在本機建立及測試 "hello world" 耐久函式。 接著會將函式程式碼發佈至 Azure。 您建立的函式會協調對其他函式的呼叫並鏈結在一起。

## <a name="learn-more"></a>深入了解

下列影片特別說明 Durable Functions 的優點：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

因為 Durable Functions 是 [Azure Functions](../functions-overview.md) 的進階擴充功能，因此並非所有應用程式都適用。 若要深入了解 Durable Functions，請參閱 [Durable Functions 模式和技術概念](durable-functions-concepts.md)。 如需與其他 Azure 協調流程技術的比較，請參閱[比較 Azure Functions 和 Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Durable Functions 模式和技術概念](durable-functions-concepts.md)
