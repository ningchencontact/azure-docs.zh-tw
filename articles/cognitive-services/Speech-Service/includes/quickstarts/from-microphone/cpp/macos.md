---
title: 快速入門：從麥克風辨識語音，C++ (macOS) - 語音服務
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
ms.openlocfilehash: 9b19cd1ff8340a22afb6713289bc3d7be42857ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500658"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=macos)

## <a name="add-sample-code"></a>新增範例程式碼

1. 建立名為 `helloworld.cpp` 的 C++ 原始程式檔，然後在其中貼上下列程式碼。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/macos/from-microphone/helloworld.cpp#code)]

1. 在這個新檔案中，以您的語音服務訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)取代 `YourServiceRegion` 字串。

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

[!INCLUDE [footer](./footer.md)]