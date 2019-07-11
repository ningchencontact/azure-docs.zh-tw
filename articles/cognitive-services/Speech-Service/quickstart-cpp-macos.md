---
title: 快速入門：辨識語音，C++ (macOS) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 macOS 上以 C++ 辨識語音
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: de170955ced45040541565800fc9853edea322f5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603072"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 macOS 上以 C++ 辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將建立適用於 macOS 10.13 或更新版本的 C++ 主控台應用程式。 您將使用認知服務[語音 SDK](speech-sdk.md)，即時從 Mac 的麥克風將語音轉譯為文字。 此應用程式是使用[適用於 macOS 的語音 SDK](https://aka.ms/csspeech/macosbinary) 和 Mac 的預設 C++ 編譯器 (例如 `g++`) 所建置。

## <a name="prerequisites"></a>必要條件

您需要語音服務訂用帳戶金鑰，才能完成本快速入門。 您可以免費取得一個金鑰。 如需詳細資訊，請參閱[免費試用語音服務](get-started.md)。

## <a name="install-speech-sdk"></a>安裝語音 SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

認知服務語音 SDK 目前的版本為 `1.6.0`。

適用於 macOS 的語音 SDK 可從 https://aka.ms/csspeech/macosbinary 以壓縮的架構組合形式下載。

下載並安裝 SDK，如下所示：

1. 選擇語音 SDK 檔案應解壓縮至的目錄，並將 `SPEECHSDK_ROOT` 環境變數設定為指向該目錄。 此變數方便在未來命令中參考此目錄。 例如，如果您想要在主目錄中使用目錄 `speechsdk`，請使用類似下列的命令：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 在該目錄不存在的情況下建立它。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下載並解壓縮包含語音 SDK 架構的 `.zip` 封存：

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 驗證已壓縮套件之最上層目錄的內容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   目錄清單應包含第三方聲明和授權檔案，以及 `MicrosoftCognitiveServicesSpeech.framework` 目錄。

## <a name="add-sample-code"></a>新增範例程式碼

1. 建立名為 `helloworld.cpp` 的 C++ 原始程式檔，然後在其中貼上下列程式碼。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. 在這個新檔案中，以您的語音服務訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

## <a name="build-the-app"></a>建置應用程式

> [!NOTE]
> 請務必以「單一命令行」  的形式輸入下面的命令。 若要這麼做，最簡單的方式就是使用每個命令旁邊的 [複製]  按鈕來複製命令，然後將它貼在殼層提示字元。

* 執行下列命令以建置應用程式。

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>執行應用程式

1. 將載入程式的程式庫路徑設定為指向語音 SDK 程式庫。

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. 執行應用程式。

   ```sh
   ./helloworld
   ```

1. 主控台視窗會出現提示，要求您說一些話。 請說英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，該文字會出現在相同視窗中。

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C++ 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
