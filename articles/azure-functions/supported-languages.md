---
title: Azure Functions 中支援的語言
description: 了解支援哪些語言 (GA) 以及哪些語言仍在實驗性或預覽版階段。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 2de2ebdea41ca35e853b37ab804e516eb7f4df9f
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494443"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions 中支援的語言

本文會說明針對可搭配 Azure Functions 使用之語言所提供的支援等級。

## <a name="levels-of-support"></a>支援等級

支援等級有三個：

* **正式推出 (GA)** - 完整支援且已核准用於生產環境。
* **預覽** - 尚未支援，但預期未來會正式推出。
* **實驗性** - 不支援，且未來可能會放棄，不保證最後會提供預覽或正式推出。

## <a name="languages-in-runtime-1x-and-2x"></a>執行階段 1.x 和 2.x 中的語言

提供[兩種版本的 Azure Functions 執行階段](functions-versions.md)。 1.x 為 GA 狀態。 它是唯一核准用於生產應用程式的執行階段。 2.x 執行階段目前處於預覽狀態，因此支援的語言也處於預覽版。 以下表格說明每個執行階段版本支援哪些語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>實驗性語言

1.x 版的實驗性語言並未妥善調整，因此不支援所有繫結。 例如，Python 速度緩慢，因為 Functions 執行階段會在每個函式引動過程中執行 *python.exe*。 而且雖然 Python 支援 HTTP 繫結，但它不能存取要求物件。

PowerShell 的實驗性支援限於 5.1 版，因為該版本是函式應用程式執行所在 VM 上所安裝的版本。 如果想要執行 PowerShell 指令碼，請考慮使用 [Azure 自動化](https://azure.microsoft.com/services/automation/)。

如果想要使用只有在 1.x 中才能使用的其中一種語言，請繼續維持使用 1.x 執行階段。 但是請勿針對您所依賴的任何項目使用實驗性語言，因為那些語言並未受到正式支援。 您可以透過[建立 GitHub 問題](https://github.com/Azure/azure-webjobs-sdk-script/issues) \(英文\) 要求協助，但不應針對實驗性語言相關問題建立支援案例。 

2.x 版執行階段不支援實驗性語言。 只有在生產環境可支援新語言時，才會新增該語言的支援。 

### <a name="language-extensibility"></a>語言擴充性

2.x 執行階段是針對提供[語言擴充性](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) \(英文\) 所設計。 Java 是以此擴充性模型為基礎的第一種語言，在 2.x 中為預覽版。

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
