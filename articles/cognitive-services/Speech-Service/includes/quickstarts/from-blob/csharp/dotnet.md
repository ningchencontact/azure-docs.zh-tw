---
title: 快速入門：辨識儲存在 Blob 儲存體中的語音，C# - 語音服務
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 96062057a139e4ab2c91792b5c451e093f7f4c96
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828751"
---
## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [將原始程式檔上傳至 Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>在 Visual Studio 中開啟您的專案

第一個步驟是確定您已在 Visual Studio 中開啟專案。

1. 啟動 Visual Studio 2019。
2. 載入您的專案，並開啟 `Program.cs`。

## <a name="add-a-reference-to-newtonsoftjson"></a>新增對 NewtonSoftJSon 的參考

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **helloworld** 專案，然後選取 [管理 NuGet 套件]  以顯示 NuGet 套件管理員。

1. 在右上角找到 [套件來源]  下拉式方塊，並確定已選取 **`nuget.org`** 。

1. 選取左上角的 [瀏覽]  。

1. 在搜尋方塊中，輸入 *newtonsoft.json*，然後選取 **Enter**。

1. 從搜尋結果中，選取 [Newtonsoft.Json]  套件，然後選取 [安裝]  以安裝最新的穩定版本。

1. 接受所有合約和授權，即可開始安裝。

   安裝套件之後，[套件管理員主控台]  視窗中會出現確認訊息。

## <a name="start-with-some-boilerplate-code"></a>從重複使用程式碼開始著手

我們將新增程式碼，作為專案的基本架構。

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]
(您必須以您自己的值來取代 `YourSubscriptionKey`、`YourServiceRegion` 和 `YourFileUrl` 的值。)
## <a name="json-wrappers"></a>JSON 包裝函式

由於 REST 的 API 會取得 JSON 格式的要求，而且也會以 JSON 格式傳回結果，因此我們只能使用字串與它們互動，但並不建議這麼做。
為了能更容易管理要求和回應，我們將宣告幾個類別以用來序列化/還原序列化 JSON。

請繼續進行，並將其宣告放在 `TranscribeAsync` 之後。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>建立與設定 HTTP 用戶端
我們需要的第一件事是具有正確基底 URL 和驗證集的 HTTP 用戶端。
在 `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)] 中插入此程式碼

## <a name="generate-a-transcription-request"></a>產生轉譯要求
接下來，我們將產生轉譯要求。 將此程式碼新增至 `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>傳送要求並檢查其狀態
現在，我們會將要求張貼到語音服務，並檢查初始的回應碼。 此回應碼只會指出服務是否已收到要求。 服務將會在回應標頭中傳回 URL，這是儲存轉譯狀態的位置。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>請等候轉譯完成
因為服務會以非同步方式處理轉譯，所以我們每隔一段時間就需要輪詢其狀態。 我們會每隔 5 秒檢查一次。

我們可以藉由在張貼要求時，從我們所取得的 URL 處擷取內容來檢查狀態。 當我們取回內容時，會將它還原序列化為我們其中一個協助程式類別，讓互動變得更容易。

以下為輪詢程式碼，其顯示除了成功完成之外所有一切的狀態，這是我們接下來將執行的作業。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>顯示轉譯結果
一旦服務成功完成轉譯，系統會將轉譯結果儲存在另一個我們可以從狀態回應取得的 URL 中。

在此，我們會先要求將這些結果下載至暫存檔，然後再讀取結果並將其還原序列化。
結果載入之後，我們就可以將它們列印到主控台。
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>檢查您的程式碼
此時，您的程式碼應會如下所示：(我們已在此版本中新增一些註解) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>建置並執行您的應用程式

現在您已準備好使用語音服務來建立應用程式，並測試我們的語音辨識。

1. **編譯程式碼** - 從 Visual Studio 的功能表列中，選擇 [建置]   > [建置解決方案]  。
2. **啟動應用程式** - 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。
3. **開始辨識** - 它會提示您說英文片語。 您的語音會傳送至語音服務、轉譯為文字，並在主控台中轉譯。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
