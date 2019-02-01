---
title: 關於語音服務 SDK - 語音服務
titleSuffix: Azure Cognitive Services
description: 「語音服務軟體開發套件」(SDK) 可讓您的應用程式以原生方式存取「語音服務」的功能，使其能夠更輕鬆地開發軟體。 本文提供關於適用於 Windows、Linux 和 Android 的 SDK 其他詳細資料。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 1b58ef5f8254ef81eceefa277e5f146531fa2947
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221788"
---
# <a name="about-the-speech-service-sdk"></a>關於語音服務 SDK

「語音服務軟體開發套件」(SDK) 可讓您的應用程式以原生方式存取「語音服務」的功能，使其能夠更輕鬆地開發軟體。 目前，SDK 提供對**語音轉換文字**、**語音翻譯**和**意圖辨識**的存取權。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>取得 SDK

### <a name="windows"></a> Windows

針對 Windows，我們支援下列語言：

* C# (UWP 與 .NET)，C++：您可以參考並使用語音 SDK NuGet 套件的最新版本。 此套件包括 32 位元和 64 位元的用戶端程式庫，以及受控 (.NET) 程式庫。 使用 NuGet 即可在 Visual Studio 中安裝此 SDK。 請搜尋 **Microsoft.CognitiveServices.Speech**。

* Java：您可以參考並使用我們的最新版「語音 SDK」Maven 套件，此版本只支援 Windows x64。 在您的 Maven 專案中，新增 `https://csspeechstorage.blob.core.windows.net/maven/` 作為額外的存放庫，並參考 `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` 作為相依性。

### <a name="linux"></a> Linux

> [!NOTE]
> 目前，我們只支援 PC 上的 Ubuntu 16.04 和 18.04 (x86 或 x64 適用於 C++ 開發，x64 適用於 .NET Core 和 Java)。

請執行下列殼層命令，確定您已安裝必要的編譯器和程式庫：

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#：您可以參考並使用語音 SDK NuGet 套件的最新版本。 若要參考 SDK，請在專案中新增下列套件參考：

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.2.0" />
  ```

* Java：您可以參考並使用語音 SDK Maven 套件的最新版本。 在您的 Maven 專案中，新增 `https://csspeechstorage.blob.core.windows.net/maven/` 作為額外的存放庫，並參考 `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` 作為相依性。

* C++：將 SDK 下載為 [.tar 套件](https://aka.ms/csspeech/linuxbinary)，然後將檔案解壓縮至您選擇的目錄。 下表顯示 SDK 資料夾結構：

  |Path|說明|
  |-|-|
  |`license.md`|授權|
  |`ThirdPartyNotices.md`|協力廠商通知|
  |`include`|C 和 C++ 的標頭檔|
  |`lib/x64`|與應用程式連結的原生 x64 程式庫|
  |`lib/x86`|與應用程式連結的原生 x86 程式庫|

  若要建立應用程式，請將所需的二進位檔 (和程式庫) 複製或移至您的開發環境。 視需要將它們包含在您的建置程序中。

### <a name="android"></a>Android

適用於 Android 的 Java SDK 會封裝成 [AAR (Android 程式庫)](https://developer.android.com/studio/projects/android-library) \(英文\)，其中包含必要的程式庫及所需的 Android 權限。 它會以 `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0` 套件的形式裝載在位於 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存放庫中。

若要從 Android Studio 專案取用套件，請進行下列變更：

* 在專案層級的 build.gradle 檔案中，將下列內容新增至 `repository` 區段：

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* 在模組層級的 build.gradle 檔案中，將下列內容新增至 `dependencies` 區段：

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.2.0'
  ```

Java SDK 也是[語音裝置 SDK](speech-devices-sdk.md) 的一部分。

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
