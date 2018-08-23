---
title: 快速入門：使用認知服務語音 SDK 在 Linux 上以 C++ 辨識語音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用認知服務語音 SDK 在 Linux 上以 C++ 辨識語音
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: b8bc24123fe75e876f607b07e37423ae68d92ee4
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2018
ms.locfileid: "42093880"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Linux 上以 C++ 辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將了解如何使用認知服務語音 SDK 將語音轉譯成文字，以在 Linux (Ubuntu 16.04) 上建立 C++ 主控台應用程式。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* Ubuntu 16.04 電腦以及運作正常的麥克風。
* 若要安裝建置並執行此範例所需的套件，請執行下列命令：

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>取得語音 SDK

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

認知服務語音 SDK 目前的版本為 `0.6.0`。

適用於 Linux 的認知服務語音 SDK 可用於建置 64 位元和 32 位元的應用程式。
必要的檔案可從 https://aka.ms/csspeech/linuxbinary 下載為 tar 檔案的格式。
下載並安裝 SDK，如下所示：

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

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 將下列程式碼新增到名為 `helloworld.cpp` 的檔案中：

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

## <a name="building"></a>建置

> [!NOTE]
> 請務必以_單行_的形式複製並貼上下面的建置命令。

* 在 **x64** 電腦上，執行下列命令以建置應用程式：

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* 在 **x86** 電腦上，執行下列命令以建置應用程式：

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>執行範例

1. 將載入程式的程式庫路徑設定為指向語音 SDK 程式庫。

   * 在 **x64** 電腦上，執行：

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * 在 **x86** 電腦上，執行：

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. 以下列方式執行應用程式：

   ```sh
   ./helloworld
   ```

1. 您應該會看到如下所示的輸出：

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/cpp-linux` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

* [取得我們的範例](speech-sdk.md#get-the-samples)
