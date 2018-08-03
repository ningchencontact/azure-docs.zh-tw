---
title: 關於認知服務語音 SDK | Microsoft Docs
description: 提供語音服務的 SDK 概觀。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ed523493f456e65f7aa5d3ad33914e3e52cd7044
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113384"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>關於認知服務語音 SDK

認知服務語音軟體開發套件 (SDK) 提供您應用程式對語音服務功能的原生存取權，以更輕鬆地開發軟體。 目前，SDK 提供對**語音轉換文字**、**語音翻譯**和**意圖辨識**的存取權。

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-windows-sdk"></a>取得 Windows SDK

Windows 版本的語音 SDK 包括 32 位元和 64 位元 C/C++ 用戶端程式庫，以及受控 (.NET) 程式庫以與 C# 搭配使用。 使用 NuGet，可以在 Visual Studio 中安裝 SDK；只要搜尋 `Microsoft.CognitiveServices.Speech`。

## <a name="get-the-linux-sdk"></a>取得 Linux SDK

請執行下列殼層命令，確定您具有必要的編譯器和程式庫：

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> 這些指示假設您在電腦 (x86 或 x64) 上執行 Ubuntu 16.04。 在不同的 Ubuntu 版本或不同的 Linux 散發套件上，調整這些步驟以符合您的環境。

然後，[下載 SDK](https://aka.ms/csspeech/linuxbinary)，並將其中的檔案解壓縮至您選擇的目錄中。 本表顯示 SDK 資料夾結構。

|Path|說明|
|-|-|
|`license.md`|授權|
|`third-party-notices.md`|協力廠商通知|
|`include`|C 和 C++ 的標頭檔|
|`lib/x64`|與應用程式連結的原生 x64 程式庫|
|`lib/x86`|與應用程式連結的原生 x86 程式庫|

若要建立應用程式，請將所需的二進位檔 (和程式庫) 複製或移至您的開發環境，並將它們包含為建置程序的必要項目。

## <a name="get-the-java-sdk"></a>取得 Java SDK

適用於 Android 的 Java SDK 封裝成 [AAR (Android 程式庫)](https://developer.android.com/studio/projects/android-library)，其中包含必要的程式庫，以及使用它的必要 Android 權限。
它在 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存放庫中以套件 `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0` 的形式裝載。
從 Android Studio 專案取用套件會進行下列變更：

* 在專案層級的 `build.gradle` 檔案中，將下列內容加入至 `repository` 區段：

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* 在模組層級的 `build.gradle` 檔案中，將下列內容加入至 `dependencies` 區段：

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Java SDK 也是[語音裝置 SDK](speech-devices-sdk.md) 的一部分。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
