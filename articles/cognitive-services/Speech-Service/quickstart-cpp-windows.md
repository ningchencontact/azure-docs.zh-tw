---
title: 適用於 C++ 和 Windows 的語音 SDK 快速入門 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 取得資訊和程式碼範例，以協助您搭配 Windows 和 C++ 快速開始使用認知服務中的語音 SDK。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049225"
---
# <a name="quickstart-for-c-and-windows"></a>適用於 C++ 和 Windows 的快速入門

認知服務語音 SDK 目前的版本為 `0.4.0`。

我們會說明如何建立適用於 Windows 桌面的 C++ 型主控台應用程式，它會使用語音 SDK。
應用程式是根據 [Microsoft 認知服務 SDK NuGet 套件](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech)和 Microsoft Visual Studio 2017。

> [!NOTE]
> 如果您是要尋找適用於 C++ 和 Linux 的快速入門，請移至[這裡](quickstart-cpp-linux.md)。<br>
> 如果您是要尋找適用於 C# 和 Windows 的快速入門，請移至[這裡](quickstart-csharp-windows.md)。

> [!NOTE]
> 本快速入門需要具有運作正常麥克風的電腦。<br>
> 如需會從指定音訊輸入檔案辨識語音的範例，請參閱[範例](speech-to-text-sample.md#speech-recognition-from-a-file)。

> [!NOTE]
> 請確定您的 Visual Studio 安裝包含**使用 C++ 的桌面開發**工作負載。
> 如果您不確定，請使用下列步驟來檢查並修正：在 Visual Studio 2017 中，選取 [工具] \> [取得工具和功能]，並且藉由選擇 [是] 來確認「使用者帳戶控制」提示。
> 在 [工作負載] 索引標籤中，如果 [使用 C++ 的桌面開發] 旁邊沒有設定核取方塊，請加以設定，然後按一下 [修改] 以儲存變更。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>建立空的主控台應用程式專案

在 Visual Studio 2017 中，建立名稱為 "CppHelloSpeech" 的新 Visual C++ Windows 桌面 Windows 主控台應用程式：

![建立 Visual C++ Windows 桌面 Windows 主控台應用程式](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

如果您是在 64 位元 Windows 安裝上執行，選擇性地將您的建置平台切換至 `x64`：

![將建置平台切換至 x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>安裝並參考語音 SDK NuGet 套件

> [!NOTE]
> 請確定已針對您的 Visual Studio 2017 安裝啟用 NuGet 套件管理員。
> 在 Visual Studio 2017 中，選取 [工具] \> [取得工具和功能]，並且藉由選擇 [是] 來確認「使用者帳戶控制」提示。 然後選取 [個別元件] 索引標籤，在 [程式碼工具] 底下尋找 [NuGet 套件管理員]。
> 如果未設定其左邊的核取方塊，請確定將其設定，然後按一下 [修改] 以儲存變更。
>
> ![在 Visual Studio 中啟用 NuGet 套件管理員 ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

在 [方案總管] 中，以滑鼠右鍵按一下解決方案，然後按一下 [管理解決方案的 NuGet 套件]。

![以滑鼠右鍵按一下 [管理解決方案的 NuGet 套件]](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

在右上角的 [套件來源] 欄位中，選擇 [Nuget.org]。
從 [瀏覽] 索引標籤中，搜尋 "Microsoft.CognitiveServices.Speech" 套件，選取該套件並且勾選右側的 [專案] 和 [CppHelloSpeech] 方塊，然後選取 [安裝] 以將其安裝到 CppHelloSpeech 專案中。

![安裝 Microsoft.CognitiveServices.Speech NuGet 套件](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

在快顯的授權畫面中，接受授權：

![接受授權](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>新增範例程式碼

使用下列程式碼取代您的預設起始程式碼：

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> 使用您取得的金鑰取代訂用帳戶金鑰。 <br>
> 從[語音服務 REST API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis) 以您的區域取代原有區域，例如，取代為 'westus'。

![新增訂用帳戶金鑰](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>建置並執行範例

現在程式碼應該可以編譯，沒有錯誤：

![建置成功](media/sdk/speechsdk-16-vs-cpp-build.png)

使用 [啟動] 按鈕或使用 F5 鍵盤快速鍵，啟動偵錯工具底下的程式：

![啟動應用程式進入偵錯模式](media/sdk/speechsdk-17-vs-cpp-f5.png)

主控台視窗應該會快顯，提示您說出任何內容 (英文)。
辨識的結果會顯示在畫面上。

![成功辨識後的主控台輸出](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>下載範例

如需最新的範例集，請參閱[認知服務語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples)。

## <a name="next-steps"></a>後續步驟

* 造訪[範例頁面](samples.md)以取得其他範例。
