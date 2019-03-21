---
title: 快速入門：翻譯語音，Java (Windows、Linux) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您將會建立簡單的 Java 應用程式來擷取使用者語音，將它翻譯為另一個語言，然後將文字輸出到命令列。 此指南是專為 Windows 和 Linux 使用者所設計。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 36eaaeabcf888aac10bcf9b8a27e3590d21079ec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897092"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>快速入門：使用適用於 Java 的語音 SDK 來轉譯語音

在此快速入門中，您將會建立簡單的 Java 應用程式以從電腦的麥克風即時擷取使用者語音，翻譯該語音，然後將翻譯的文字轉譯到命令列。 此應用程式是設計來在 64 位元的 Windows 或 64 位元的 Ubuntu Linux 16.04/18.04 上執行，並使用語音 SDK Maven 套件與 Eclipse Java IDE 所建置。

如需可供進行語音翻譯的完整語言清單，請參閱[語言支援](language-support.md)。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* 作業系統：64 位元的 Windows 或 64 位元的 Ubuntu Linux 16.04/18.04
* [Eclipse Java IDE](https://www.eclipse.org/downloads/) \(英文\)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) \(英文\) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) \(英文\)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

如果您執行 Ubuntu 16.04/18.04，請先確定已安裝下列相依性，再啟動 Eclipse。

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

> [!NOTE]
> 針對語音裝置 SDK 和 Roobo 裝置，請參閱[語音裝置 SDK](speech-devices-sdk.md)。

## <a name="create-and-configure-project"></a>建立及設定專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 若要將新的空白類別新增到您的 Java 專案，請選取 [檔案] > [新增] > [類別]。

1. 在 [新增 Java 類別] 視窗中，於 [套件] 欄位中輸入 **speechsdk.quickstart**，並在 [名稱] 欄位中輸入 **Main**。

   ![[新增 Java 類別] 視窗的螢幕擷取畫面](media/sdk/qs-java-jre-06-create-main-java.png)

1. 使用下列程式碼片段來取代 `Main.java` 中的所有程式碼：

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

按 F11 鍵，或選取 [執行] > [偵錯]。

系統將會辨識來自您麥克風的語音輸入，並將它轉譯為德文，然後記錄在主控台視窗中。 按 "Enter" 來停止擷取語音。

![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音，以及將翻譯的文字輸出為合成語音。

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Java 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [快速入門：辨識語音，Java (Windows、Linux)](quickstart-java-jre.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
