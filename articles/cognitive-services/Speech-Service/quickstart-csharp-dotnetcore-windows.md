---
title: 快速入門：使用認知服務語音 SDK 在 Windows 上根據 .NET Core 以 C# 辨識語音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用認知服務語音 SDK 在 Windows 上根據 .NET Core 以 C# 辨識語音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 74fb62ffa707188779bcf827d427c61a0b9a4347
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576333"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Windows 上根據 .NET Core 以 C# 辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將學習到如何透過使用認知服務語音 SDK 將語音轉譯成文字，在 Windows 上的 .NET Core 建立 C# 主控台應用程式。
應用程式是利用 [Microsoft 認知服務 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 所建置的。

> [!NOTE]
> .NET Core 是實作[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 規格的開放原始碼、跨平台 .NET 平台。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* Windows 電腦以及運作正常的麥克風。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/)，Community Edition 以上版本。
* Visual Studio 中的 **.NET 跨平台開發**工作負載。 您可以在 [工具]  \> [取得工具與功能] 中啟用該工作負載。

  ![啟用 .NET Core 跨平台開發](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 2017 中，建立新的 Visual C# .NET Core 主控台應用程式。 在 [新增專案] 對話方塊中，展開左窗格中的 [已安裝] \> [Visual C#] \> [.NET Core]，然後選取 [主控台應用程式 (.NET Core)]。 針對專案名稱，請輸入 *helloworld*。

    ![建立 Visual C# 主控台應用程式 (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "建立 Visual C# 主控台應用程式 (.NET Core)")

1. 安裝並參考 [Speech SDK NuGet 封裝](https://aka.ms/csspeech/nuget)。 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後選取 [管理解決方案的 NuGet 套件]。

    ![以滑鼠右鍵按一下解決方案的管理 NuGet 封裝](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "管理解決方案的 NuGet 封裝")

1. 在右上角的 [套件來源] 欄位中，選取 [Nuget.org]。搜尋並安裝 `Microsoft.CognitiveServices.Speech` 套件，然後將它安裝到 **helloworld** 專案。

    ![安裝 Microsoft.CognitiveServices.Speech NuGet 封裝](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "安裝 Nuget 封裝")

1. 接受顯示的授權。

    ![接受授權](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "接受授權")

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 開啟 `Program.cs` 並將其中的所有程式碼取代為下列程式碼。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 建置應用程式。 從功能表列中，選擇 [建置] > [建置解決方案]。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![成功建置](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選擇 [偵錯] > [開始偵錯]，或按 **F5**。

    ![讓應用程式進入偵錯工作](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "讓應用程式進入偵錯工作")

1. 一個主控台視窗隨即顯示，提示您說話 (請用英文)。 辨識出的文字隨即顯示在相同的視窗中。

    ![成功辨識後的主控台輸出](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "成功辨識後的主控台輸出")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-dotnetcore-windows` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

- [轉譯語音](how-to-translate-speech-csharp.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
