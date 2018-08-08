---
title: 快速入門：使用認知服務語音 SDK 在 Android 上以 Java 辨識語音
titleSuffix: Microsoft Cognitive Services
description: 了解如何使用認知服務語音 SDK 在 Android 上以 Java 辨識語音
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: ad1bf15f198529487dbed92d8f8639704b0c5fc7
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325120"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>快速入門：使用語音 SDK 在 Android 上以 Java 辨識語音

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，您將了解如何使用認知服務語音 SDK 將語音轉譯成文字，建立適用於 Android 的 Java 應用程式。
應用程式以 Microsoft 認知服務語音 SDK Maven 套件 0.5.0 版和 Android Studio 3.1 為基礎。

> [!NOTE]
> 針對語音裝置 SDK 和 Roobo 裝置，請瀏覽[語音裝置 SDK](speech-devices-sdk.md) 頁面。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* 能夠執行 Android Studio 的電腦 (Windows、Linux、Mac)。
* [Android Studio](https://developer.android.com/studio/) 3.1 版。
* [已啟用](https://developer.android.com/studio/debug/dev-options)使用運作正常的麥克風進行開發的 ARM 型 Android 裝置 (API 23: Android 6.0 Marshmallow 或更新版本)。

## <a name="create-an-android-studio-project"></a>建立 Android Studio 專案

啟動 Android Studio，然後選取 [開始新的 Android Studio 專案]。

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

在出現的 [建立新專案] 精靈中，選取下列選項：

1. 在 [建立 Android 專案] 畫面上，輸入 **Quickstart** 作為**應用程式名稱**，並輸入 **samples.speech.cognitiveservices.microsoft.com** 作為**公司網域**，然後選擇一個專案位置。 將核取方塊維持在未核取狀態，然後按一下 [下一步]。

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. 在 [目標 Android 裝置] 畫面上，核取 [手機和平板電腦] 作為唯一的選項、從下方的下拉式清單中選擇 [API 23: Android 6.0 (Marshmallow)]，然後按一下 [下一步]。

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. 在 [將活動加入行動裝置] 畫面中，選取 [空白活動]，然後按一下 [下一步]。

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. 在 [設定活動] 畫面中，使用 **MainActivity** 作為 [活動名稱]，並使用 **activity\_main** 作為 [版面配置名稱]。 選取這兩個核取方塊，然後按一下 [完成]。

   ![](media/sdk/qs-java-android-05-configure-activity.png)

執行一段時間之後，應該出現新建立的 Android Studio 專案。

## <a name="configure-your-project-for-the-speech-sdk"></a>設定適用於語音 SDK 的專案

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

認知服務語音 SDK 目前的版本為 `0.5.0`。

適用於 Android 的語音 SDK 封裝成 [AAR (Android 程式庫)](https://developer.android.com/studio/projects/android-library)，其中包含必要的程式庫，以及使用它的必要 Android 權限。
它會裝載在位於 https://csspeechstorage.blob.core.windows.net/maven/ 的 Maven 存放庫中。

我們會說明如何將您的專案設定為使用語音 SDK 如下。

在 [檔案] \> [專案結構] 底下，開啟 [專案結構] 視窗。
在出現的視窗中，進行下列變更 (只有在您完成所有步驟之後，才能按一下 [確定])：

1. 選取 [專案]，然後在單引號中附加逗號和我們的 Maven 存放庫 URL `'https://csspeechstorage.blob.core.windows.net/maven/'`，以編輯 [預設程式庫存放庫]：

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. 仍在同一個畫面中的左側，選取 [應用程式] 模組，然後選取頂端的 [相依性] 索引標籤。接著，按一下右上角的綠色加號，然後選取 [程式庫相依性]。

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. 在出現的視窗中，輸入適用於 Android 之語音 SDK 的名稱和版本 `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`，然後按一下 [確定]。
   語音 SDK 現在應該會加入至相依性的清單，如下所示：

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. 選取頂端的 [屬性] 索引標籤。針對 [來源相容性] 和 [目標相容性] 兩者，選取 [1.8]。

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. 最後，按一下 [確定]，關閉 [專案結構] 視窗，並套用所有更新。

## <a name="create-a-minimal-ui"></a>建立最低限度的 UI

編輯您主要活動的版面配置 `activity_main.xml`。
根據預設，應該會出現具有您的應用程式名稱的標題列，以及顯示 'Hello World!' 的 TextView。

* 按一下 [TextView]。 在 `hello` 的右上角，變更其 ID 屬性。

* 從 `activity_main.xml` 視窗左上方的 [調色盤] 中，將 [按鈕] 拖曳至文字上方的空白空間。

* 在右側按鈕的屬性中，針對 `onClick` 屬性的值，輸入 `onSpeechButtonClicked`，這將是我們按鈕處理常式的名稱。
  在 `button` 的右上角，變更其 ID 屬性。

* 如果您想要為自己推斷版面配置條件約束，請使用設計工具頂端的魔術棒圖示。

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

UI 的文字和圖形化版本現在看起來應該像下面這樣：

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 編輯 `MainActivity.java` 原始程式檔，並將其程式碼取代為下面這樣 (在您的 package 陳述式下方)：

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 方法包含要求麥克風和網際網路權限，以及初始化原生平台繫結的程式碼。 設定原生平台繫結只需要一次，也就是說，應該要在應用程式初始化期間及早完成。
   
   * 方法 `onSpeechButtonClicked` 先前已當作按鈕 click 處理常式連接。 按下按鈕就會觸發實際的語音辨識。

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

## <a name="build-and-run-the-sample"></a>建置並執行範例

* 若要建置，按下 Ctrl+F9，或選取 [建置]\> [建立專案]。

* 將 Android 裝置連接到開發電腦。 請確定您已[啟用開發模式和 USB 偵錯](https://developer.android.com/studio/debug/dev-options) \(英文\)。

* 若要啟動應用程式，按下 Shift+F10，或選取 [執行] \>[執行應用程式]。

* 在出現的 [部署目標] 視窗中，挑選您的 Android 裝置。

  ![啟動應用程式進入偵錯模式](media/sdk/qs-java-android-12-deploy.png)

* 應用程式應該會在您的裝置上啟動。
  一旦您按下按鈕之後，將會辨識接下來的 15 秒，並顯示在 UI 中 (您也應該能夠在 Android Studio 的 logcat 視窗中看到回應)：

  ![成功辨識後的 UI](media/sdk/qs-java-android-13-gui-on-device.png)

此螢幕擷取畫面總結 Android 快速入門。 完整的專案範例程式碼可以從範例存放庫下載。

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/java-android` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

* [取得我們的範例](speech-sdk.md#get-the-samples)
