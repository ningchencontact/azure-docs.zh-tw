---
title: 快速入門：辨識語音 (Unity) - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南，可透過 Unity 和適用於 Unity 的語音 SDK (搶鮮版 (Beta)) 建立語音轉文字的應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 06831fa933c04827c966e8f6e12aa817f5008b88
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554152"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>快速入門：使用適用於 Unity 的語音 SDK (搶鮮版 (Beta)) 來辨識語音

另備有[文字轉換語音](quickstart-text-to-speech-csharp-unity.md)的快速入門。

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

使用本指南，可透過 [Unity](https://unity3d.com/) 和適用於 Unity 的語音 SDK (搶鮮版 (Beta)) 建立語音轉文字的應用程式。
完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
如果您不熟悉 Unity，建議您先詳讀 [Unity 使用者手冊](https://docs.unity3d.com/Manual/UnityManual.html)，再開始進行應用程式開發。

> [!NOTE]
> 適用於 Unity 的語音 SDK 目前為搶鮮版 (Beta)。
> 它支援 Windows Desktop (x86 和 x64) 或通用 Windows 平台 (x86、x64、ARM/ARM64)，以及 Android (x86、ARM32/64)。

## <a name="prerequisites"></a>必要條件

若要完成此專案，您需要：

* [Unity 2018.3 或更新版本](https://store.unity.com/)與[新增 UWP ARM64 支援的 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * 如需 ARM64 支援，安裝[適用於 ARM64 的選擇性建置工具和適用於 ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)
* 適用於語音服務的訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 能夠存取電腦的麥克風。

## <a name="create-a-unity-project"></a>建立 Unity 專案

* 啟動 Unity，然後在 [專案]  索引標籤下方選取 [新增]  。
* 指定 **csharp-unity** 作為 [專案名稱]  ，並指定 **3D** 作為 [範本]  ，然後選擇位置。
  接著，選取 [建立專案]  。
* 不久後應該就會跳出 [Unity 編輯器] 視窗。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* 適用於 Unity 的語音 SDK (搶鮮版 (Beta)) 會封裝為 Unity 資產套件 (.unitypackage)。
  請從[這裡](https://aka.ms/csspeech/unitypackage)下載。
* 選取 [資產]   > [匯入套件]   > [自訂套件]  ，以匯入語音 SDK。
  如需詳細資訊，請查看 [Unity 文件](https://docs.unity3d.com/Manual/AssetPackages.html)。
* 在檔案選擇器中，選取您先前下載的語音 SDK .unitypackage 檔案。
* 確定所有檔案皆已選取，然後按一下 [匯入]  ：

  ![匯入語音 SDK Unity 資產套件時的 Unity 編輯器螢幕擷取畫面](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>新增 UI

我們僅在場景中新增最低限度的 UI，其中包含觸發語音辨識的按鈕，和顯示結果的文字欄位。

* 在[階層視窗](https://docs.unity3d.com/Manual/Hierarchy.html) (依預設位於左側) 中，會顯示 Unity 建立的範例場景，內含新專案。
* 按一下 [階層視窗] 頂端的 [建立]  按鈕，然後選取 [UI]   > [按鈕]  。
* 這會建立三個可在 [階層視窗] 中檢視的遊戲物件：內嵌在**畫布**物件中的**按鈕**物件，和**事件系統**物件。
* [瀏覽場景檢視](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以清楚檢視畫布和[場景檢視](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的按鈕。
* 按一下 [階層視窗] 中的 [按鈕]  物件，以在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中顯示其設定。
* 將 [位置 X]  和 [位置 Y]  屬性設為 **0**，使按鈕置於畫布中央。
* 再按一下 [階層視窗] 頂端的 [建立]  按鈕，然後選取 [UI]   > [文字]  以建立文字欄位。
* 按一下 [階層視窗] 中的 [文字]  物件，以在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中顯示其設定。
* 將 [位置 X]  和 [位置 Y]  屬性分別設為 **0** 和 **120**，並將 [寬度]  和 [高度]  屬性分別設為 **240** 和 **120**，以確保文字欄位和按鈕不會重疊。

完成之後，UI 應該會如下列螢幕擷取畫面所示：

[![快速入門使用者介面在 Unity 編輯器中的螢幕擷取畫面](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 在[專案視窗](https://docs.unity3d.com/Manual/ProjectView.html) (依預設位於左下方) 中按一下 [建立]  按鈕，然後選取 [C# 指令碼]  。 將指令碼命名為 `HelloWorld`。

1. 按兩下該指令碼加以編輯。

   > [!NOTE]
   > 您可以在 [編輯]   > [喜好設定]  下方設定所將啟動的程式碼編輯器，詳情請參閱 [Unity 使用者手冊](https://docs.unity3d.com/Manual/Preferences.html)。

1. 將所有程式碼取代為下列內容：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. 找出字串 `YourSubscriptionKey` 並將其取代為您的語音服務訂用帳戶金鑰。

1. 找出字串 `YourServiceRegion` 並將其取代為與您的訂用帳戶相關聯的[區域](regions.md)。 例如，如果您使用的是免費試用版，區域將是 `westus`。

1. 儲存指令碼的變更。

1. 回到 Unity 編輯器，您必須將指令碼新增為其中一個遊戲物件的元件。

   * 在階層視窗中按一下 [畫布]  物件。 這會在[偵測器視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中開啟設定。
   * 在偵測器視窗中按一下 [新增元件]  按鈕，然後搜尋我們先前建立的 HelloWorld 指令碼，並加以新增。
   * 請注意，Hello World 元件具有 [輸出文字]  和 [開始辨識按鈕]  這兩個未初始化的屬性，與 `HelloWorld` 類別的公用屬性相符。
     若要加以連接，請按一下 [物件選擇器] (屬性右側的小圓圈圖示)，然後選擇您先前建立的文字和按鈕物件。

     > [!NOTE]
     > 按鈕中還有內嵌的文字物件。 請確定您並未不慎選擇該物件作為文字輸出 (或者，請使用 [偵測器視窗] 中的 [名稱] 欄位將其中一個文字物件重新命名，以避免混淆)。

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 編輯器中執行應用程式

* 在 [Unity 編輯器] 工具列 (在功能表列下方) 按下 [播放]  按鈕。

* 在應用程式啟動後按一下按鈕，然後對電腦的麥克風說出英文片語或句子。 您的語音會傳送到語音服務，並且轉譯為文字，出現在視窗中。

  [![在 Unity 遊戲視窗中執行快速入門的螢幕擷取畫面](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-expanded.png#lightbox)

* 查看[主控台視窗](https://docs.unity3d.com/Manual/Console.html)中偵錯訊息。

* 完成語音辨識後，請按一下 [Unity 編輯器] 工具列中的 [播放]  按鈕以停止應用程式。

## <a name="additional-options-to-run-this-application"></a>執行此應用程式的其他選項

此應用程式也可部署至 Android，作為 Windows 的獨立應用程式或 UWP 應用程式。
請參閱 quickstart/csharp-unity 資料夾中的[範例存放庫](https://aka.ms/csspeech/samples)，其中包含這些其他目標的設定說明。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
