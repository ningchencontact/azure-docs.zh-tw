---
title: Azure Functions 執行階段版本概觀
description: Azure Functions 支援多個執行階段版本。 了解其間的差異以及如何選擇最適合您的版本。
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 8ba12f21d76d8deded047f40489c46657c9380b8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38602112"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 執行階段版本概觀

 Azure Functions 執行階段有兩個主要版本：1.x 和 2.x。 只有 1.x 已核准用於生產環境。 本文會說明 2.x (預覽狀態) 中的新增功能。

| 執行階段 | 狀態 |
|---------|---------|
|1.x|正式上市 (GA)|
|2.x|預覽|

> [!NOTE] 
> 本文所介紹的是雲端服務 Azure Functions。 如需可執行 Azure Functions 內部部署的詳細資訊，請參閱 [Azure Functions 執行階段概觀](functions-runtime-overview.md)。

## <a name="cross-platform-development"></a>跨平台開發

執行階段 1.x 僅支援在入口網站或 Windows 上進行開發和裝載。 執行階段 2.x 會在 .NET Core 上執行，這表示它可以在受到 .NET Core 支援的所有平台上執行，包括 macOS 和 Linux。 這可實現跨平台部署和裝載案例，1.x 則無法做到這一點。

## <a name="languages"></a>語言

執行階段 2.x 使用新的語言擴充性模型。 一開始，JavaScript 和 Java 會運用這個新模型。 Azure Functions 1.x 實驗語言尚未更新為使用新模型，因此在 2.x 中不受支援。 下表說明列出每個執行階段版本所支援的程式設計語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

如需詳細資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="bindings"></a>繫結 

執行階段 2.x 使用新的[繫結擴充性模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，該模型提供下列優點：

* 支援第三方繫結延伸模組。
* 分開處理執行階段和繫結。 這可讓繫結延伸模組個別建立版本和發行。 舉例來說，您可以選擇升級至依賴較新版基礎 SDK 的延伸模組版本。
* 較輕便的執行環境，執行階段只會知道及載入使用中的繫結。

所有內建 Azure Functions 繫結均已採用此模型，除了計時器觸發程序和 HTTP 觸發程序以外，依預設不會再包含這些繫結。 當您透過 Visual Studio 等工具或透過入口網站建立函式時，這些延伸模組會自動安裝。

下表說明每個執行階段版本所支援的繫結。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>2.x 中的已知問題

如需 2.x 中的繫結支援和其他功能差異 的詳細資訊，請參閱[執行階段 2.0 的已知問題](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [撰寫 Azure Functions 並在本機進行測試](functions-run-local.md)
* [如何設定 Azure Functions 執行階段目標版本](set-runtime-version.md)
* [版本資訊](https://github.com/Azure/azure-functions-host/releases)