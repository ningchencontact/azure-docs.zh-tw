---
title: Azure Functions 中支援的語言
description: 了解支援哪些語言 (GA) 以及哪些語言仍在實驗性或預覽版階段。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148804"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions 中支援的語言

本文會說明針對可搭配 Azure Functions 使用之語言所提供的支援等級。

## <a name="levels-of-support"></a>支援等級

支援等級有三個：

* **正式推出 (GA)** - 完整支援且已核准用於生產環境。
* **預覽** - 尚未支援，但預期未來會正式推出。
* **實驗性** - 不支援，且未來可能會放棄，不保證最後會提供預覽或正式推出。

## <a name="languages-in-runtime-1x-and-2x"></a>執行階段 1.x 和 2.x 中的語言

提供[兩種版本的 Azure Functions 執行階段](functions-versions.md)。 以下表格說明每個執行階段版本支援哪些語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>實驗性語言

1.x 版的實驗性語言並未妥善調整，因此不支援所有繫結。

請勿針對您所依賴的任何項目使用實驗性功能，因為那些功能並未受到正式支援。 請勿針對實驗性語言相關的問題建立支援案例。 

2.x 版執行階段不支援實驗性語言。 只有在生產環境可支援新語言時，才會新增該語言的支援。 

### <a name="language-extensibility"></a>語言擴充性

2.x 執行階段是針對提供[語言擴充性](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) \(英文\) 所設計。 2.x 執行階段中的 JavaScript 和 Java 語言是使用此擴充性來建置。

## <a name="next-steps"></a>後續步驟

若要深入了解如何在 Azure Functions 中使用其中一種 GA 或預覽版語言，請參閱下列資源：

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
