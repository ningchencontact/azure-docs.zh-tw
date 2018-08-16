---
title: 快速入門：使用認知服務語音 SDK 在 UWP 應用程式中以 C# 辨識語音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用認知服務語音 SDK 在 UWP 應用程式中辨識語音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: d1245b07ac0de680c13542b9af86b25bdf517c21
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576129"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 UWP 應用程式中辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將學習到如何透過使用認知服務語音 SDK 將語音轉譯成文字，以建立通用 Windows 平台 (UWP) 應用程式。
應用程式是利用 [Microsoft 認知服務 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 所建置的。

> [!NOTE]
> 通用 Windows 平台可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。 使用語音 SDK 的應用程式尚未通過 Windows 應用程式認證套件 (WACK) 的驗證。 目前可以側載您的應用程式，但無法提交至 Microsoft Store。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* Windows 電腦 (Windows 10 Fall Creators Update 或更新版本) 與可正常運作的麥克風。
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/)，Community Edition 以上版本。
* Visual Studio 中的**通用 Windows 平台開發**工作負載。您可以在 [工具] \> [取得工具與功能] 中啟用該工作負載。

  ![啟用通用 Windows 平台開發](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 2017 中，建立新的 Visual C# Windows 通用空白應用程式。 在 [新增專案] 對話方塊中，從左窗格展開 [已安裝] \> [Visual C#] \> [Windows 通用]，然後選取 [空白應用程式 (通用 Windows)]。 針對專案名稱，請輸入 *helloworld*。

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. 在彈出的 [新增通用 Windows 平台專案] 視窗中，在 [最低版本] 中選擇 [Windows 10 Fall Creators Update (10.0；組建 16299)]，並在 [目標版本] 中選擇此版本或任何更新版本，然後按一下 [確定]。

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. 如果您是在 64 位元 Windows 安裝上執行，您可以將建置平台切換至 `x64`。

   ![將建置平台切換至 x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > 目前語音 SDK 支援 Intel 相容處理器，不支援 ARM。

1. 安裝並參考 [Speech SDK NuGet 封裝](https://aka.ms/csspeech/nuget)。 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後選取 [管理解決方案的 NuGet 套件]。

    ![以滑鼠右鍵按一下 [管理解決方案的 NuGet 套件]](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. 在右上角的 [套件來源] 欄位中，選取 [Nuget.org]。搜尋並安裝 `Microsoft.CognitiveServices.Speech` 套件，然後將它安裝到 **helloworld** 專案。

    ![安裝 Microsoft.CognitiveServices.Speech NuGet 封裝](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "安裝 Nuget 封裝")

1. 接受顯示的授權。

    ![接受授權](media/sdk/qs-csharp-uwp-06-nuget-license.png "接受授權")

1. 下列輸出行會出現在套件管理器主控台中。

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 在 [方案總管] 中，按兩下 [Package.appxmanifest] 以編輯您的應用程式資訊清單。
   選取 [功能] 索引標籤，選取 [麥克風] 功能的核取方塊，然後儲存您的變更。

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. 在 [方案總管] 中，按兩下 `MainPage.xaml` 以編輯應用程式的使用者介面。 

    在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入格線標記 (在 `<Grid>` 和 `</Grid>` 之間)。

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 在 [方案總管] 中，按兩下 `MainPage.xaml.cs` (它是在 `MainPage.xaml` 項目下分組) 以編輯 XAML 程式碼後置。
   以下列程式碼取代此檔案中的所有程式碼。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 處理常式中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 在 `SpeechRecognitionFromMicrophone_ButtonClicked` 處理常式中，以和您的訂用帳戶 相關聯的[區域](regions.md) (例如，免費試用訂用帳戶的 `westus`) 取代 `YourServiceRegion` 字串。

1. 儲存專案的所有變更。

## <a name="build-and-run-the-sample"></a>建置並執行範例

1. 建置應用程式。 從功能表列中，選取 [建置]  >  [建置解決方案]。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![成功建置](media/sdk/qs-csharp-uwp-08-build.png "成功建置")

1. 啟動應用程式。 從功能表列中，選取 [偵錯]  >  [開始偵錯]，或按 **F5**。

    ![讓應用程式進入偵錯工作](media/sdk/qs-csharp-uwp-09-start-debugging.png "讓應用程式進入偵錯工作")

1. GUI 視窗隨即出現。 請先按一下 [啟用麥克風] 按鈕，並確認彈出的權限要求。

    ![讓應用程式進入偵錯工作](media/sdk/qs-csharp-uwp-10-access-prompt.png "讓應用程式進入偵錯工作")

1. 按一下 [使用麥克風輸入的語音辨識]，然後對裝置的麥克風說出簡短片語。 辨識出的文字會顯示在視窗中。

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/csharp-uwp` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

- [轉譯語音](how-to-translate-speech-csharp.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
