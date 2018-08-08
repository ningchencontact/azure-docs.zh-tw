---
title: 快速入門：使用認知服務語音 SDK 在 Windows 上根據 .NET Framework 以 C# 辨識語音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用認知服務語音 SDK 在 Windows 上根據 .NET Framework 以 C# 辨識語音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 587ae9f6452f85dee867047e47ccc272ee508b81
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325185"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Windows 上根據 .NET Framework 以 C# 辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將學習到如何透過使用認知服務語音 SDK 將語音轉譯成文字，在 Windows 上的 .NET Framework 建立 C# 主控台應用程式。
應用程式是利用 [Microsoft 認知服務 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 所建置的。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* Windows 電腦以及運作正常的麥克風。
* Visual Studio 2017，Community Edition 以上版本。
* Visual Studio 中的 **.NET 桌面開發**工作負載。 您可以在 [工具]  \> [取得工具與功能] 中啟用該工作負載。

  ![啟用 .NET 桌面開發](media/sdk/vs-enable-net-desktop-workload.png)

  ![啟用 .NET Core 跨平台開發](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 2017 中，建立新的 Visual C# 主控台應用程式。 在 [新增專案] 對話方塊中，從左窗格展開 [已安裝] \> [Visual C++] \> [Windows 桌面]，然後選取 [主控台應用程式 (.NET Framework)]。 針對專案名稱，請輸入 *helloworld*。

    ![建立 Visual C# 主控台應用程式 (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "建立 Visual C# 主控台應用程式 (.NET Framework)")

1. 安裝並參考 [Speech SDK NuGet 封裝](https://aka.ms/csspeech/nuget)。 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後選取 [管理解決方案的 NuGet 套件]。

    ![以滑鼠右鍵按一下解決方案的管理 NuGet 封裝](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "管理解決方案的 NuGet 封裝")

1. 在右上角的 [套件來源] 欄位中，選取 [Nuget.org]。搜尋 `Microsoft.CognitiveServices.Speech` 套件，然後將它安裝到 **helloworld** 專案。

    ![安裝 Microsoft.CognitiveServices.Speech NuGet 封裝](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "安裝 Nuget 封裝")

1. 在出現的對話方塊中接受授權。

    ![接受授權](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "接受授權")

1. 下列輸出行會出現在套件管理器主控台中。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>建立符合您 PC 結構的平台設定

在本節中，您將把一個平台新增到符合您處理器結構的設定。

1. 啟動組態管理員。 選取 [建置] >  [組態管理員]。

    ![啟動組態管理員](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "啟動組態管理員")

1. 在 [組態管理員] 對話方塊中，新增平台。 從 [使用中的方案平台] 下拉式清單中，選取 [新增]。

    ![在 [組態管理員] 視窗下方新增平台](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "在 [組態管理員] 視窗下方新增平台")

1. 如果您執行的是 64 位元 Windows，請建立一個名為 `x64` 的新平台設定。 如果您執行的是32 位元 Windows，請建立一個名為 `x86` 的新平台設定。 在本文中，您將建立 `x64` 平台設定。

    ![在 64 位元 Windows 上新增名為「x64」的平台](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "新增 x64 平台")

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 開啟 Visual Studio 專案中的 `Program.cs`，然後以下列程式碼取代此檔案中的所有程式碼。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 建置應用程式。 從功能表列中，選取 [建置]  >  [建置解決方案]。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![成功建置](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選取 [偵錯]  >  [開始偵錯]，或按 **F5**。

    ![讓應用程式進入偵錯工作](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "讓應用程式進入偵錯工作")

1. 一個主控台視窗隨即顯示，提示您說話 (請用英文)。 辨識出的文字隨即顯示在相同的視窗中。

    ![成功辨識後的主控台輸出](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "成功辨識後的主控台輸出")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-dotnet-windows` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

- [轉譯語音](how-to-translate-speech-csharp.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
