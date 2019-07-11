---
title: 快速入門：辨識語音，Java (Android) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Android 上以 Java 辨識語音
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 22970eb58dc244405e522d46fcaa380151076062
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603105"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Android 上以 Java 辨識語音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將了解如何使用認知服務語音 SDK 將語音轉譯成文字，以開發適用於 Android 的 Java 應用程式。
應用程式以語音 SDK Maven 套件 1.6.0 版和 Android Studio 3.3 為基礎。
語音 SDK 目前與使用 32/64 位元 ARM 和 Intel x86/x64 相容處理器的 Android 裝置相容。

> [!NOTE]
> 針對語音裝置 SDK 和 Roobo 裝置，請參閱[語音裝置 SDK](speech-devices-sdk.md)。

## <a name="prerequisites"></a>必要條件

您需要語音服務訂用帳戶金鑰，才能完成本快速入門。 您可以免費取得一個金鑰。 如需詳細資訊，請參閱[免費試用語音服務](get-started.md)。

## <a name="create-and-configure-a-project"></a>建立和設定專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>建立使用者介面

我們將建立應用程式的基本使用者介面。 編輯您主要活動的版面配置 `activity_main.xml`。 最初，版面配置會包含具有您應用程式名稱的標題列，以及包含 "Hello World!" 文字的 TextView。

* 按一下 TextView 元素。 在 `hello` 的右上角，變更其 ID 屬性。

* 從 `activity_main.xml` 視窗左上方的 [調色盤] 中，將按鈕拖曳至文字上方的空白處。

* 在右側按鈕的屬性中，針對 `onClick` 屬性的值，輸入 `onSpeechButtonClicked`。 我們將以此名稱撰寫用來處理按鈕事件的方法。  在 `button` 的右上角，變更其 ID 屬性。

* 使用設計工具頂端的魔術棒圖示，推斷版面配置條件約束。

  ![魔術棒圖示的螢幕擷取畫面](media/sdk/qs-java-android-10-infer-layout-constraints.png)

UI 的文字和圖形化表示法現在應會顯示如下：

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟來源檔案 `MainActivity.java`。 以下列程式碼取代此檔案中的所有程式碼。

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 方法包含要求麥克風和網際網路權限，以及初始化原生平台繫結的程式碼。 原生平台繫結只需要設定一次。 此設定應該在應用程式初始化期間即已完成。

   * 如先前所述，方法 `onSpeechButtonClicked` 是按鈕點擊處理常式。 按下按鈕就會觸發語音轉換文字的轉譯。

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 同時以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 將 Android 裝置連接到開發電腦。 請確定您已在裝置上啟用[開發模式和 USB 偵錯](https://developer.android.com/studio/debug/dev-options)。

1. 若要建置應用程式，請按 Ctrl+F9，或從功能表列中選擇 [建置]   > [建立專案]  。

1. 若要啟動應用程式，請按 Shift+F10，或選擇 [執行]   > [執行應用程式]  。

1. 在出現的 [部署目標] 視窗中，選擇您的 Android 裝置。

   ![選取部署目標視窗的螢幕擷取畫面](media/sdk/qs-java-android-12-deploy.png)

按下應用程式中的按鈕，開始使用 [語音辨識] 區段。 接下來 15 秒的英文語音會傳送到語音服務，並進行轉譯。 結果會出現在 Android 應用程式中，以及 Android Studio 的 Logcat 視窗中。

![Android 應用程式的螢幕擷取畫面](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 Java 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
