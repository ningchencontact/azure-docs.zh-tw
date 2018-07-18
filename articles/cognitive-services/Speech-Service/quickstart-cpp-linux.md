---
title: 適用於 C++ 和 Linux 的語音 SDK 快速入門 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 取得資訊和程式碼範例，以協助您搭配 Linux 和 C++ 快速開始使用認知服務中的語音 SDK。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753584"
---
# <a name="quickstart-for-c-and-linux"></a>適用於 C++ 和 Linux 的快速入門

認知服務語音 SDK 目前的版本為 `0.4.0`。

適用於 Linux 的認知服務語音 SDK 可用於建置 64 位元和 32 位元的應用程式。 必要的檔案可從 https://aka.ms/csspeech/linuxbinary 下載為 tar 檔案的格式。

> [!NOTE]
> 如果您是要尋找適用於 C++ 和 Windows 的快速入門，請移至[這裡](quickstart-cpp-windows.md)。
> 如果您是要尋找適用於 C# 和 Windows 的快速入門，請移至[這裡](quickstart-csharp-windows.md)。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 這些指示假設您是於電腦上的 Ubuntu 16.04 (x86 或 x64) 上執行。
> 如果您是在不同的 Ubuntu 版本，或是不同的 Linux 散發套件上執行，便必須對所需的步驟做出調整。

## <a name="prerequisites"></a>先決條件

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>取得二進位套件

[!include[License Notice](includes/license-notice.md)]

1. 選擇您想要放置語音 SDK 二進位檔和標頭的目錄 (絕對路徑)。
   例如，選擇位於主目錄下的 `speechsdk` 路徑：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 在該目錄不存在的情況下建立它：

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下載並解壓縮具有語音 SDK 二進位檔的 `.tar.gz` 封存：

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 驗證已壓縮套件之最上層目錄的內容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   它應該會顯示第三方聲明和授權檔案，與包含標頭的 `include` 目錄，以及包含程式庫的 `lib` 目錄。

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>範例程式碼

下列程式碼能透過您的麥克風識別英文語音。
將它置於名為 `quickstart-linux.cpp` 的檔案中：

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

將程式碼中的訂用帳戶金鑰取代為您所取得的金鑰。

## <a name="building"></a>建置

> [!NOTE]
> 請務必以_單行_的形式複製並貼上下面的建置命令。

* 執行下列命令以在 x64 電腦上建置應用程式：

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* 執行下列命令以在 x86 電腦上建置應用程式：

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>執行中

若要執行應用程式，您必須將載入器的程式庫路徑設定為指向語音 SDK 程式庫。

* 在 x64 電腦上，請執行：

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* 在 x86 電腦上，請執行：

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

以下列方式執行應用程式：

```sh
./quickstart-linux
```

如果順利執行，您應該會看到如下所示的輸出：

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>下載範例

如需最新的範例集，請參閱[認知服務語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) \(英文\)。

## <a name="next-steps"></a>後續步驟

* 造訪[範例頁面](samples.md)以取得其他範例。
