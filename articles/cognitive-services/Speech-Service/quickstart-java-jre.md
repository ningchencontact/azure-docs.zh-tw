---
title: 快速入門：辨識語音，Java (Windows、Linux) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在此快速入門中，您會了解如何建立簡單的 Java 應用程式，以擷取及轉譯來自電腦麥克風的使用者語音。
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 87360d49892698458a021287d88240d98ba2ee19
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881500"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>快速入門：使用適用於 Java 的語音 SDK 來辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您會使用[語音服務 SDK](speech-sdk.md)，建立 Java 主控台應用程式。 您將來自電腦麥克風的語音即時轉譯為文字。 此應用程式是使用語音 SDK Maven 套件，以及 64 位元 Windows 或 64 位元 Ubuntu Linux 16.04 / 18.04 上的 Eclipse Java IDE (v4.8) 來建置。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

> [!NOTE]
> 針對語音裝置 SDK 和 Roobo 裝置，請參閱[語音裝置 SDK](speech-devices-sdk.md)。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* 作業系統：Windows (64 位元) 或 Ubuntu Linux 16.04/18.04 (64 位元)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) \(英文\) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) \(英文\)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。

如果您執行 Ubuntu 16.04/18.04，請先確定已安裝下列相依性，再啟動 Eclipse。

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

如果您執行 Windows (64 位元)，請確定您已為平台安裝 Microsoft Visual C++ 可轉散發套件。
* [下載適用於 Visual Studio 2017 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


## <a name="create-and-configure-project"></a>建立及設定專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 若要將新的空白類別新增到您的 Java 專案，請選取 [檔案] > [新增] > [類別]。

1. 在 [新增 Java 類別] 視窗中，於 [套件] 欄位中輸入 **speechsdk.quickstart**，並在 [名稱] 欄位中輸入 **Main**。

   ![[新增 Java 類別] 視窗的螢幕擷取畫面](media/sdk/qs-java-jre-06-create-main-java.png)

1. 使用下列程式碼片段來取代 `Main.java` 中的所有程式碼：

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

按 F11 鍵，或選取 [執行] > [偵錯]。
系統將會辨識接下來 15 秒來自您麥克風的語音輸入，並記錄在主控台視窗中。

![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Java 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [快速入門：翻譯語音，Java (Windows、Linux)](quickstart-translate-speech-java-jre.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
