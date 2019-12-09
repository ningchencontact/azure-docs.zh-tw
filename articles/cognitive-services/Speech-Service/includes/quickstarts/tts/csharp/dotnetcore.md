---
title: 快速入門：合成語音，C# (.NET Core) - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 在 Windows 上以 .NET Core 下的 C# 合成語音
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 6e732237b22bec6e2a66d44f12ac25e5f9adca3f
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818249"
---
> [!NOTE]
> .NET Core 是實作[.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 規格的開放原始碼、跨平台 .NET 平台。

## <a name="prerequisites"></a>必要條件

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=dotnetcore)
## <a name="add-sample-code"></a>新增範例程式碼

1. 開啟 `Program.cs` 並將其中的所有程式碼取代為下列程式碼。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. 在相同檔案中，以您的訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 同時，將 `YourServiceRegion` 字串替換成您訂用帳戶的關聯[區域](~/articles/cognitive-services/Speech-Service/regions.md) (例如，免費試用訂用帳戶的區域是 `westus`)。

1. 儲存專案的變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從功能表列中，選擇 [建置]   > [建置解決方案]  。 這應該會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置方案] 選項已添加醒目提示](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "建置成功")

1. 啟動應用程式。 從功能表列中，選擇 [偵錯]   > [開始偵錯]  ，或按 **F5**。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已添加醒目提示](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "啟動應用程式進入偵錯模式")

1. 主控台視窗隨即顯示，提示您輸入某些文字。 請輸入幾個字或句子。 您輸入的文字會傳送至語音服務並合成為語音，然後以喇叭播放。

    ![此螢幕擷取畫面顯示合成成功後的主控台輸出](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "合成成功後的主控台輸出")

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另請參閱

- [建立自訂語音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [錄製自訂語音樣本](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
