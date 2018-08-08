---
title: 快速入門：使用認知服務語音 SDK 在 Windows 桌面上以 C++ 辨識語音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用認知服務語音 SDK 在 Windows 桌面上以 C++ 辨識語音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 7c93f9f450cc2d244a0d716401171cde02abf70b
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324566"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Windows 桌面上以 C++ 辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

我們會說明如何建立適用於 Windows 桌面的 C++ 型主控台應用程式，它會使用語音 SDK。
應用程式是以 [Microsoft 認知服務語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 和 Microsoft Visual Studio 2017 為基礎。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* Windows 電腦以及運作正常的麥克風。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/)，Community Edition 或更新版本。
* Visual Studio 中**使用 C++ 進行桌面開發**的工作負載，以及 Visual Studio 中的 **NuGet 套件管理員**元件。
  您可以分別在 [工具] \> [取得工具和功能] 的 [工作負載] 和 [個別元件] 索引標籤底下啟用兩者：

  ![使用 C++ 工作負載啟用桌面開發](media/sdk/vs-enable-cpp-workload.png)

  ![在 Visual Studio 中啟用 NuGet 套件管理員 ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

在 Visual Studio 2017 中，建立一個新的 Visual C++ Windows 桌面 Windows 主控台應用程式。 在 [新增專案] 對話方塊中，從左窗格展開 [已安裝] \> [Visual C++] \> [Windows 桌面]，然後選取 [Windows 主控台應用程式]。 針對專案名稱，請輸入 *helloworld*。

![建立 Visual C++ Windows 桌面 Windows 主控台應用程式](media/sdk/qs-cpp-windows-01-new-console-app.png)

如果您是在 64 位元 Windows 安裝上執行，選擇性地將您的建置平台切換至 `x64`：

![將建置平台切換至 x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>安裝並參考語音 SDK NuGet 套件

在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後按一下 [管理解決方案的 NuGet 套件]。

![以滑鼠右鍵按一下 [管理解決方案的 NuGet 套件]](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

在右上角的 [套件來源] 欄位中，選擇 [Nuget.org]。
從 [瀏覽] 索引標籤中，搜尋 "Microsoft.CognitiveServices.Speech" 套件，選取該套件並且核取右側的 [專案] 和 [helloworld] 方塊，然後選取 [安裝] 以將其安裝到 helloworld 專案中。

> [!NOTE]
> 認知服務語音 SDK 目前的版本為 `0.5.0`。

![安裝 Microsoft.CognitiveServices.Speech NuGet 套件](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

在快顯的授權畫面中，接受授權：

![接受授權](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 使用下列程式碼取代您的預設起始程式碼：

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 建置應用程式。 從功能表列中，選取 [建置]  >  [建置解決方案]。 現在編譯程式碼應不會再出現錯誤訊息：

   ![建置成功](media/sdk/qs-cpp-windows-06-build.png)

1. 啟動應用程式。 從功能表列中，選取 [偵錯]  >  [開始偵錯]，或按 **F5**。

   ![啟動應用程式進入偵錯模式](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. 一個主控台視窗快顯，提示您說話 (請用英文)。
   辨識的結果會顯示在畫面上。

   ![成功辨識後的主控台輸出](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/cpp-windows` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

* [取得我們的範例](speech-sdk.md#get-the-samples)
