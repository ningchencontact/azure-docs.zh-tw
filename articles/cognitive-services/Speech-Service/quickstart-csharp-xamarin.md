---
title: 快速入門：辨識語音，C# (Xamarin) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會使用認知服務語音 SDK 建立適用於 Windows UWP、Android 和 iOS 的跨平台 C# Xamarin 應用程式。 您會將來自裝置或模擬器麥克風的語音即時轉譯為文字。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2019 所建置的。
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: 02031e8e2800329134eaf34f5f99c93a20b99aac
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803878"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在跨平台的 Xamarin 應用程式中辨識語音

另備有[語音轉文字](quickstart-csharp-uwp.md)、[文字轉語音](quickstart-text-to-speech-csharp-uwp.md)和[語音翻譯](quickstart-translate-speech-uwp.md)的快速入門。

在本文中，您會使用[語音 SDK](speech-sdk.md)，開發通用 Windows 平台 (UWP)、Android 和 iOS 的 Xamarin 所適用的跨平台 C# 應用程式。 該程式會從您裝置的麥克風將語音即時轉換成文字。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget) \(英文\) 與 Microsoft Visual Studio 2019 (任何版本) 所建置的。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 具有 Windows 10 Fall Creators Update (10.0，組建 16299) 或更新版本以及可用麥克風的 Windows 電腦。
* [將 Xamarin 安裝至 Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)。
* [在 Windows 上安裝 Xamarin Android](https://docs.microsoft.com/xamarin/android/get-started/installation/windows)。
* [在 Windows 上安裝 Xamarin iOS](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows)。
* 若要以 Android 為目標： 
   * Android 裝置 (ARM32/64，x86；API 23：Android 6.0 Marshmallow 或更新版本)，且[可供開發之用](https://developer.android.com/studio/debug/dev-options)，具備可用的麥克風。
* 若要以 iOS 為目標：[可供開發之用](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)且具備可用麥克風的 iOS 裝置 (ARM64) 或 iOS 模擬器 (x64)。
* 如需 Windows ARM64 組建支援，請安裝[適用於 ARM/ARM64 的選擇性建置工具和 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>新增一般 `helloworld` 專案的範例程式碼

一般 `helloworld` 專案包含跨平台應用程式的平台通用實作。
現在，新增可定義應用程式使用者介面的 XAML 程式碼，並新增程式 C# 程式碼後置實作。

1. 在 [方案總管]  中的一般 `helloworld` 專案下方，開啟 `MainPage.xaml`。

1. 在設計工具的 XAML 檢視中，將下列 XAML 程式碼片段插入**格線**標記 (在 `<StackLayout>` 和 `</StackLayout>` 之間)：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. 在 [方案總管]  中，開啟程式碼後置原始檔 `MainPage.xaml.cs`。 (它分組在 `MainPage.xaml` 底下。)

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. 在來源檔案的 `OnRecognitionButtonClicked` 處理常式中，尋找字串 `YourSubscriptionKey`，並將其取代為與您的訂用帳戶金鑰相關聯的區域。

1. 在 `OnRecognitionButtonClicked` 處理常式中，尋找字串 `YourServiceRegion`，並將它取代為與您的訂用帳戶相關聯的[區域](regions.md)。 (例如，針對免費試用訂用帳戶使用 `westus`。)

1. 接下來，您必須建立 [Xamarin 服務](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)，用來查詢不同平台專案 (UWP、Android 和 iOS) 的麥克風權限。 若要這麼做，請在 `helloworld` 專案下新增資料夾 `Services`，並在其下建立新的 C# 原始程式檔 (以滑鼠右鍵按一下 `Services` 資料夾，然後選取 [新增]   > [新增項目]   > [程式碼檔案]  )，並將它重新命名為 `IMicrophoneService.cs`，然後將下列程式碼片段中的所有程式碼放入該檔案中：[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>新增 `helloworld.Android` 專案的範例程式碼

現在，新增 C# 程式碼以定義應用程式的 Android 特定部分。

1. 在 [方案總管]  中的 `helloworld.Android` 專案下方，開啟 `MainActivity.cs`。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. 接著，在 `helloworld.Android` 專案下建立新的資料夾 `Services`，以新增 `MicrophoneService` 的 Android 特定實作。 然後，在其下建立新的 C# 原始程式檔，並將它重新命名為 `MicrophoneService.cs`，然後將下列程式碼片段複製並貼到該檔案中。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. 接下來，在 `Properties` 資料夾下開啟 `AndroidManifest.xml`，然後在 `<manifest>` 與 `</manifest>` 之間新增麥克風的下列使用權限設定。
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>新增 `helloworld.iOS` 專案的範例程式碼

現在，新增 C# 程式碼以定義應用程式的 iOS 特定部分，同時建立 helloworld ios 專案的 Apple 裝置特定組態。

1. 在 [方案總管]  中的 `helloworld.iOS` 專案下方，開啟 `AppDelegate.cs`。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. 接著，在 `helloworld.iOS` 專案下建立新的資料夾 `Services`，以新增 `MicrophoneService` 的 iOS 特定實作。 然後，在其下建立新的 C# 原始程式檔，並將它重新命名為 `MicrophoneService.cs`，然後將下列程式碼片段複製並貼到該檔案中。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. 在文字編輯器中開啟 `helloworld.iOS` 專案下的 Info.plist，然後在 dict 區段下新增下列索引鍵值組：<key>NSMicrophoneUsageDescription</key>
   <string>此範例應用程式需要麥克風存取權</string>
   > 注意：如果您的目標為 iPhone 裝置的組建，請確定 `Bundle Identifier` 符合裝置的佈建設定檔應用程式識別碼，否則建置將會失敗。 對於 iPhoneSimulator，您可以將它保持原狀。

1. 如果您要在 Windows 電腦上建置，則必須建立 Mac 裝置的連線，以透過 [工具]   > [iOS]   > [與 Mac 配對]  進行建置。 請依照 Visual Studio 提供的指示精靈操作，以啟用 Mac 裝置的連線。

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>新增 `helloworld.UWP` 專案的範例程式碼

現在，新增 C# 程式碼以定義應用程式的 UWP 特定部分。

1. 在 [方案總管]  中的 `helloworld.UWP` 專案下方，開啟 `MainPage.xaml.cs`。

1. 將其中的所有程式碼取代為下列程式碼片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. 接著，在 `helloworld.UWP` 專案下建立新的資料夾 `Services`，以新增 `MicrophoneService` 的 UWP 特定實作。 然後，在其下建立新的 C# 原始程式檔，並將它重新命名為 `MicrophoneService.cs`，然後將下列程式碼片段複製並貼到該檔案中。

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. 接下來，在 Visual Studio 中按兩下 `helloworld.UWP` 專案下的 `Package.appxmanifest` 檔案，並在勾選 [功能]   > [麥克風]  後儲存檔案。
   > 注意：如果您看到警告：憑證檔案不存在：helloworld.UWP_TemporaryKey.pfx，請檢查 [語音轉文字](quickstart-csharp-uwp.md)範例以取得詳細資訊。

1. 從功能表列中，選擇 [檔案]   > [全部儲存]  來儲存變更。

## <a name="build-and-run-the-uwp-application"></a>建置並執行 UWP 應用程式

1. 將 `helloworld.UWP` 設定為起始專案，並以滑鼠右鍵按一下 `helloworld.UWP` 專案，然後選擇 [建置]  以建置應用程式。 

1. 選擇 [偵錯]   > [開始偵錯]  (或按 **F5**) 以啟動應用程式。 **helloworld** 視窗會出現。

   ![C# 範例 UWP 語音辨識應用程式 - 快速入門](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. 選取 [啟用麥克風]  ，當存取權限要求出現時，選取 [是]  。

   ![麥克風存取權限要求](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. 選取 [啟動語音辨識]  ，然後對裝置的麥克風說出英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在視窗中。

   ![語音辨識使用者介面](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>建置並執行 Android 和 iOS 應用程式

在裝置或模擬器中建置並執行 Android 和 iOS 應用程式的方式，與 UWP 類似。 重要的是，請確定在本文件的 `Prerequisites` 一節中所需的所有 SDK 均已正確安裝。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)
