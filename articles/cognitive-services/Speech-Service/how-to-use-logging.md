---
title: 語音 SDK 記錄語音服務
titleSuffix: Azure Cognitive Services
description: 啟用語音 SDK 中的記錄。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 4573ff14c5326a47cf7b15ae2660614661d45d80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073076"
---
# <a name="enable-logging-in-the-speech-sdk"></a>啟用語音 SDK 中的記錄

記錄的檔案是語音 SDK 的選擇性功能。 在開發期間記錄會提供其他資訊和從語音 SDK 的核心元件診斷。 可藉由設定屬性來啟用`Speech_LogFilename`語音之組態物件的位置和記錄檔的名稱。 記錄從該組態建立的辨識器之後全域啟用，而不能在之後停用。 記錄工作階段執行期間，您無法變更記錄檔的名稱。

> [!NOTE]
> 因為語音 SDK 中所有 1.4.0 版支援的程式設計語言，除了 JavaScript 的語音 SDK 使用記錄。

## <a name="sample"></a>範例

組態物件上指定的記錄檔名稱。 採取`SpeechConfig`為例，假設您已建立執行個體呼叫`config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

您可以從組態物件來建立辨識器。 這可讓所有的辨識器的記錄。

> [!NOTE]
> 如果您建立`SpeechSynthesizer`從設定物件，它將不會啟用記錄。 如果透過啟用記錄，您也會收到來自診斷`SpeechSynthesizer`。

## <a name="create-a-log-file-on-different-platforms"></a>建立在不同的平台上的記錄檔

適用於 Windows 或 Linux，記錄檔可以是任何使用者具有寫入權限的路徑。 在其他作業系統中的檔案系統位置的寫入權限可能有限，或依預設限制。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 應用程式必須是位置記錄檔儲存在其中一個應用程式資料位置 （本機、 漫遊，或暫時）。 可以在本機應用程式資料夾中建立的記錄檔：

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

更多有關檔案存取 UWP 應用程式的權限可[此處](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)。

### <a name="android"></a>Android

您可以將記錄檔儲存至內部存放區、 外部儲存體或快取目錄。 在內部儲存體中建立檔案或快取目錄是私用應用程式。 最好是外部的儲存體中建立記錄檔。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

上述程式碼會將記錄檔案儲存到外部儲存體，在應用程式特定目錄的根目錄中。 使用者可以使用檔案管理員存取檔案 (通常在`Android/data/ApplicationName/logfile.txt`)。 解除安裝應用程式時，將會刪除檔案。

您也必須要求`WRITE_EXTERNAL_STORAGE`資訊清單檔中的權限：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

更多有關資料和檔案則儲存體 Android 應用程式便可用於[此處](https://developer.android.com/guide/topics/data/data-storage.html)。

#### <a name="ios"></a>iOS

只有在應用程式沙箱目錄都可以存取。 可以在文件、 程式庫和 temp 目錄中建立檔案。 文件 目錄中的檔案可提供給使用者。 下列程式碼片段會顯示應用程式的文件目錄中建立記錄檔：

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

若要存取建立的檔案，新增下列屬性至`Info.plist`應用程式的屬性清單：

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

更多有關 iOS 檔案系統可[此處](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 GitHub 上探索我們的範例](https://aka.ms/csspeech/samples)

