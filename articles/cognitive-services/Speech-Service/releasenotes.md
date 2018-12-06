---
title: 語音服務 SDK 文件
titlesuffix: Azure Cognitive Services
description: 版本資訊 - 最近版本中的變更
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: wolfma
ms.openlocfilehash: ff09926d61d64294a4f12ccf959f6685910539f1
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317886"
---
# <a name="release-notes"></a>版本資訊

## <a name="speech-service-sdk-110"></a>語音服務 SDK 1.1.0

**新功能**

* 支援 Android x86/x64。
* Proxy 支援：您現在可以在 SpeechConfig 物件中呼叫函式來設定 Proxy 資訊 (主機名稱、連接埠、使用者名稱和密碼)。 此功能尚無法在 iOS 上取得。
* 改善的錯誤碼和訊息。 如果辨識傳回錯誤，系統現在會將 `Reason` (在取消的事件中) 或 `CancellationDetails` (在辨識結果中) 設為 `Error`。 取消的事件現在包含兩個額外的成員，`ErrorCode` 和 `ErrorDetails`。 如果伺服器隨報告的錯誤傳回其他錯誤資訊，現在將可以透過新成員取得這些資訊。

**改進**

* 已在辨識器設定中新增其他驗證，及新增其他錯誤訊息。
* 已改善對音訊檔案中長時間無聲之部分的處理。
* NuGet 套件：針對 .NET Framework 專案，其可防止使用 AnyCPU 設定來建置。

**錯誤修正**

* 已修正在辨識器中找到的幾個例外狀況。 此外，系統會攔截例外狀況，並轉換成已取消事件。
* 修正屬性管理中的記憶體流失。
* 已修正音訊輸入可能會損毀辨識器的錯誤。
* 已修正工作階段將事件停止之後，仍然可收到事件的錯誤。
* 已修正執行緒中的一些競爭條件。
* 已修正可能會導致當機的 iOS 相容性問題。
* 已改善對 Android 麥克風支援的穩定性。
* 已修正 JavaScript 中的辨識器會忽略辨識語言的錯誤。
* 已修正 JavaScript 中 (在某些情況下) 會防止設定 EndpointId 的錯誤。
* 變更 JavaScript 中 AddIntent 內參數的順序，和新增遺失的 AddIntent JavaScript 簽章。

**範例**

* 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增可供提取和推送資料流使用方式的 C++ 和 C# 範例。

## <a name="speech-service-sdk-101"></a>語音服務 SDK 1.0.1

穩定性改進及 Bug 修正：

* 已修正處置辨識器時因競爭條件而可能導致的嚴重錯誤
* 已修正未設定屬性時可能導致的嚴重錯誤。
* 已新增額外的錯誤和參數檢查。
* Objective-C：已修正因 NSString 中的名稱覆寫而可能導致的嚴重錯誤。
* Objective-C：已調整 API 的可見性
* JavaScript：已修正有關事件及其承載的問題。
* 文件改進。

在我們的[範例存放庫](https://aka.ms/csspeech/samples)中，已新增新的 JavaScript 範例。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>認知服務語音 SDK 1.0.0：2018 年 9 月版

**新功能**

* iOS 上對 Objective-C 的支援。 請參閱我們的[適用於 iOS 的 Objective-C 快速入門](quickstart-objectivec-ios.md)。
* 瀏覽器中對 JavaScript 的支援。 請參閱我們的 [JavaScript 快速入門](quickstart-js-browser.md)。

**重大變更**

* 此版本引進了幾個重大變更。
  如需詳細資訊，請參閱[此頁面](https://aka.ms/csspeech/breakingchanges_1_0_0)。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>認知服務語音 SDK 0.6.0：2018 年 8 月版

**新功能**

* 使用語音 SDK 所建置的 UWP 應用程式現在可以通過 Windows 應用程式認證套件 (WACK) 的認證。
  請參閱 [UWP 快速入門](quickstart-csharp-uwp.md)。
* 在 Linux (Ubuntu 16.04 x64) 上支援 .NET Standard 2.0。
* 實驗性：在 Windows (64 位元) 和 Linux (Ubuntu 16.04 x64) 上支援 Java 8。
  請參閱 [Java 執行階段環境快速入門](quickstart-java-jre.md)。

**功能性變更**

* 公開關於連線錯誤的其他錯誤詳細資訊。

**重大變更**

* 在 Java (Android) 上，`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 函式不再需要路徑參數。 系統現在會自動在所有支援平台上偵測路徑。
* Java 和 C# 中 `EndpointUrl` 屬性的 get-accessor 已移除。

**錯誤修正**

* 在 Java 中，現已實作翻譯辨識器上的音訊合成結果。
* 已修正錯誤，此錯誤可能導致非作用中的執行緒，並增加已開啟且未使用的通訊端數目。
* 已修正問題，長時間執行的辨識可能會在傳輸途中終止。
* 已修正辨識器關機中的競爭條件。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>認知服務語音 SDK 0.5.0：2018 年 7 月版

**新功能**

* 支援 Android 平台 (API 23: Android 6.0 Marshmallow 或更新版本)。 請參閱 [Android 快速入門](quickstart-java-android.md)。
* 在 Windows 上支援 .NET Standard 2.0。 請參閱 [.NET Core 快速入門](quickstart-csharp-dotnetcore-windows.md)。
* 實驗性：在 Windows 上支援 UWP (1709 版或更新版本)。
  * 請參閱 [UWP 快速入門](quickstart-csharp-uwp.md)。
  * 注意：使用語音 SDK 建置的 UWP 應用程式尚未通過 Windows 應用程式認證套件 (WACK) 的認證。
* 使用自動重新連線支援長時間執行辨識。

**功能性變更**

* `StartContinuousRecognitionAsync()` 支援長時間執行辨識。
* 辨識結果包含更多欄位。 它們從已辨識文字的音訊開頭和持續時間 (兩者都以刻度為單位)，以及表示辨識狀態的其他值位移，例如 `InitialSilenceTimeout` 和 `InitialBabbleTimeout`。
* 支援 AuthorizationToken 以建立處理站執行個體。

**重大變更**

* 辨識事件：NoMatch 事件類型已合併到錯誤事件中。
* C# 中的 SpeechOutputFormat 已重新命名為 OutputFormat，以便與 C++ 保持一致。
* `AudioInputStream` 介面部分方法的傳回型別已稍微變更：
   * 在 Java 中，`read` 方法現在會傳回 `long` 而不是 `int`。
   * 在 C# 中，`Read` 方法現在會傳回 `uint` 而不是 `int`。
   * 在 C++ 中，`Read` 和 `GetFormat` 方法現在會傳回 `size_t` 而不是 `int`。
* C++：音訊輸入串流的執行個體現在只能當作 `shared_ptr` 傳遞。

**錯誤修正**

* 修正 `RecognizeAsync()` 逾時的時候，結果中不正確的傳回值。
* 已移除 Windows 上媒體基礎程式庫的相依性。 SDK 現在使用核心音訊 API。
* 文件修正：已新增[區域](regions.md)頁面來描述支援的區域。

**已知問題**

* 適用於 Android 的語音 SDK 不會回報用於翻譯的語音合成結果。 此問題將在下一版中修正。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>認知服務語音 SDK 0.4.0：2018 年 6 月版本

**功能性變更**

- AudioInputStream

  辨識器現在可以取用資料流作為音訊來源。 如需詳細資訊，請參閱相關的[操作說明指南](how-to-use-audio-input-streams.md)。

- 詳細的輸出格式

  建立 `SpeechRecognizer` 時，您可以要求 `Detailed` 或 `Simple` 輸出格式。 `DetailedSpeechRecognitionResult` 包含信賴分數、已辨識的文字、未經處理的語彙形式，以及標準化形式和加上遮罩的粗話。

**重大變更**

- 在 C# 中已從 `SpeechRecognitionResult.RecognizedText` 變更為 `SpeechRecognitionResult.Text`。

**錯誤修正**

- 已修正在關閉期間 USP 層中可能發生的回呼問題。

- 如果辨識器取用音訊輸入檔，其保留檔案控制代碼超過所需的時間。

- 已移除訊息幫浦與辨識器之間的數個死結。

- 如果來自服務的回應逾時，則引發 `NoMatch`。

- Windows 上的媒體基礎程式庫延遲載入。 只有麥克風輸入需要此程式庫。

- 音訊資料的上傳速度受限於大約兩倍的原始音訊速度。

- 在 Windows 上，C# .NET 組件現在使用強式名稱。

- 文件修正：`Region` 是建立辨識器時所需的資訊。

已新增更多範例，且持續更新。 如需最新的範例集，請參閱[語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) \(英文\)。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>認知服務語音 0.2.12733：2018 年 5 月版本

此版本為認知服務語音 SDK 的第一個公開預覽版本。
