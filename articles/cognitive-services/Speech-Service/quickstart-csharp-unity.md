---
title: 快速入門：辨識語音 (Unity) - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南，可透過 Unity 和適用於 Unity 的語音 SDK 建立語音轉文字的應用程式。 完成之後，您可以使用電腦的麥克風將語音即時轉譯為文字。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803477"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>快速入門：使用適用於 Unity 的語音 SDK 來辨識語音

另備有[文字轉換語音](quickstart-text-to-speech-csharp-unity.md)的快速入門。

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

使用本指南，可使用 [Unity](https://unity3d.com/) 和適用於 Unity 的語音 SDK 來建立語音轉文字的應用程式。
完成之後，您便可以命令裝置將語音即時轉譯為文字。
如果您不熟悉 Unity，建議您在開發應用程式之前，先研究 [Unity 使用者手冊](https://docs.unity3d.com/Manual/UnityManual.html)。

> [!NOTE]
> 適用於 Unity 的語音 SDK 支援 Windows Desktop (x86 和 x64) 或通用 Windows 平台 (x86、x64、ARM/ARM64)、Android (x86、ARM32/64) 以及 iOS (x64 模擬器、ARM32 和 ARM64)

## <a name="prerequisites"></a>必要條件

若要完成此專案，您需要：

- [Unity 2018.3 或更新版本](https://store.unity.com/)與[新增 UWP ARM64 支援的 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 15.9 版或更高版本的 Visual Studio 2017 也可以接受。
- 如需 Windows ARM64 支援，請安裝[適用於 ARM64 的選擇性建置工具和適用於 ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)。
- 適用於語音服務的訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
- 能夠存取電腦的麥克風。

## <a name="create-a-unity-project"></a>建立 Unity 專案

1. 開啟 Unity。 如果您首次使用 Unity，便會出現 [Unity 中樞]  *<version number>* 視窗。 (您也可以直接開啟 Unity 中樞來進入此視窗)。

   [![Unity 中樞視窗](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. 選取 [ **新增**]。 隨即會出現 [使用 Unity 建立新專案]  *<version number>* 視窗。

   [![在 Unity 中樞中建立新專案](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. 在 [專案名稱]  中，輸入 **csharp-unity**。
1. 在 [範本]  中，如果尚未選取 [3D]  ，請加以選取。
1. 在 [位置]  中，選取或建立用來儲存專案的資料夾。
1. 選取 [建立]  。

不久後就會出現 [Unity 編輯器] 視窗。

## <a name="install-the-speech-sdk"></a>安裝語音 SDK

若要安裝適用於 Unity 的語音 SDK，請遵循下列步驟：

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. 下載和開啟[適用於 Unity 的語音 SDK](https://aka.ms/csspeech/unitypackage)，其會封裝為 Unity 資產套件 (.unitypackage)。 當資產套件開啟時，[匯入 Unity 套件]  對話方塊隨即出現。

   [![Unity 編輯器中的 [匯入 Unity 套件] 對話方塊](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. 確定所有檔案皆已選取，然後選取 [匯入]  。 一會兒之後，Unity 資產套件就會匯入到您的專案中。

如需將資產套件匯入到 Unity 的詳細資訊，請參閱 [Unity 文件](https://docs.unity3d.com/Manual/AssetPackages.html)。

## <a name="add-ui"></a>新增 UI

現在，讓我們在場景中新增最小的 UI。 此 UI 會包含用來觸發語音辨識的按鈕，以及用來顯示結果的文字欄位。 在 [[階層]  視窗](https://docs.unity3d.com/Manual/Hierarchy.html)中，會顯示 Unity 使用新專案所建立的場景範例。

1. 在 [階層]  視窗頂端，選取 [建立]   > [UI]   > [按鈕]  。

   此動作會建立三個可在 [階層]  視窗中檢視的遊戲物件：**按鈕**物件、含有按鈕的**畫布**物件，以及 **EventSystem** 物件。

   [![Unity 編輯器環境](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [瀏覽 [場景]  檢視](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以清楚檢視 [[場景]  檢視](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的畫布和按鈕。

1. 在 [[偵測器]  視窗](https://docs.unity3d.com/Manual/UsingTheInspector.html) (依預設位於右側) 中，將 **Pos X** 和 **Pos Y** 屬性設定為 **0**，以便讓按鈕位於畫布中央。

1. 在 [階層]  視窗中，選取 [建立]   > [UI]   > [文字]  以建立**文字**物件。

1. 在 [偵測器]  視窗中，將 **Pos X** 和 **Pos Y** 屬性設定為 **0** 和 **120**，並將 [寬度]  和 [高度]  屬性設定為 **240** 和 **120**。 這些值可確保文字欄位和按鈕不會重疊。

完成之後，[場景]  檢視應該會如下列螢幕擷取畫面所示：

[![Unity 編輯器中的 [場景] 檢視](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>新增範例程式碼

若要新增 Unity 專案的指令碼程式碼範例，請遵循下列步驟：

1. 在 [[專案] 視窗](https://docs.unity3d.com/Manual/ProjectView.html)中，選取 [建立]   > [C# 指令碼]  以新增 C# 指令碼。

   [![Unity 編輯器中的 [專案] 視窗](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. 將指令碼命名為 `HelloWorld`。

1. 按兩下 `HelloWorld` 以編輯新建立的指令碼。

   > [!NOTE]
   > 若要設定供 Unity 用來編輯的程式碼編輯器，請選取[編輯]   > [喜好設定]  ，然後移至 [外部工具]  喜好設定。 如需詳細資訊，請參閱 [Unity 使用者手冊](https://docs.unity3d.com/Manual/Preferences.html)。

1. 將現有指令碼取代為下列程式碼：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. 尋找字串 `YourSubscriptionKey` 並將其取代為您的語音服務訂用帳戶金鑰。

1. 尋找字串 `YourServiceRegion` 並將其取代為與您的訂用帳戶相關聯的[區域](regions.md)。 例如，如果您使用的是免費試用版，區域將是 `westus`。

1. 儲存指令碼的變更。

現在，返回 Unity 編輯器，然後將指令碼新增為其中一個遊戲物件的元件：

1. 在 [階層]  視窗中，選取 [畫布]  物件。

1. 在 [偵測器]  視窗中，選取 [新增元件]  按鈕。

   [![Unity 編輯器中的 [偵測器] 視窗](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. 在下拉式清單中，搜尋我們前面建立的 `HelloWorld` 指令碼並加以新增。 [偵測器]  視窗中便會出現 [Hello World (指令碼)]  區段，並列出兩個未初始化的屬性，分別是 [輸出文字]  和 [開始辨識按鈕]  。 這些 Unity 元件屬性會符合 `HelloWorld` 類別的公用屬性。

1. 選取 [開始辨識按鈕]  屬性的物件選擇器 (屬性右邊的小圓圈圖示)，然後選擇您稍早建立的 [按鈕]  物件。

1. 選取 [輸出文字]  屬性的物件選擇器，然後選擇您稍早建立的 [文字]  物件。

   > [!NOTE]
   > 按鈕中還有內嵌的文字物件。 請確定您並未不慎選擇該物件作為文字輸出 (或者，請使用 [偵測器]  視窗中的 [名稱]  欄位將其中一個文字物件重新命名，以避免混淆)。

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 編輯器中執行應用程式

現在您已經準備好在 Unity 編輯器內執行應用程式。

1. 在 Unity 編輯器工具列 (位於功能表列下方) 中，選取 [播放]  按鈕 (指向右方的三角形)。

1. 移至 [[遊戲]  檢視](https://docs.unity3d.com/Manual/GameView.html)，並等候 [文字]  物件顯示 [按一下按鈕以辨識語音]  。 (當應用程式尚未啟動或尚未準備好回應時，則會顯示 [新增文字]  )。

1. 選取該按鈕，然後對電腦的麥克風說出英文片語或句子。 您的語音會傳送到語音服務並轉譯為文字，文字則會出現在 [遊戲]  檢視中。

   [![Unity 編輯器中的 [遊戲] 檢視](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. 查看 [[主控台]  視窗](https://docs.unity3d.com/Manual/Console.html)中的偵錯訊息。 如果 [主控台]  視窗並未出現，請移至功能表列，然後選取 [視窗]   > [一般]   > [主控台]  來顯示該視窗。

1. 完成語音辨識後，請選取 [Unity 編輯器] 工具列中的 [播放]  按鈕以停止應用程式。

## <a name="additional-options-to-run-this-application"></a>執行此應用程式的其他選項

此應用程式也可部署為 Android 應用程式、Windows 的獨立應用程式或 UWP 應用程式。
如需詳細資訊，請參閱[存放庫範例](https://aka.ms/csspeech/samples)。 `quickstart/csharp-unity` 資料夾會說明這些額外目標的設定。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)

## <a name="see-also"></a>另請參閱

- [讓自訂語音模型定型](how-to-custom-speech-train-model.md)
