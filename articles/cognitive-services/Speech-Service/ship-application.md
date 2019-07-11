---
title: 使用語音 SDK 開發應用程式 - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 建立應用程式。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 7c698abb133c14f32b60b22acbbccc37a191a02e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604852"
---
# <a name="ship-an-application"></a>交付應用程式

在散發 Azure 認知服務語音 SDK 時，請查看[語音 SDK 授權](https://aka.ms/csspeech/license201809)及[第三方軟體聲明](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)。 此外，請檢閱 [Microsoft 隱私權聲明](https://aka.ms/csspeech/privacy)。

視平台而定，有不同的相依性存在以執行您的應用程式。

## <a name="windows"></a>視窗

測試認知語音 SDK 會在 Windows 10 和 Windows Server 2016 上進行測試。

認知服務語音 SDK 需要[Microsoft VisualC++可轉散發套件的 Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)系統上。 您可以在此下載最新版 `Microsoft Visual C++ Redistributable for Visual Studio 2019` 的安裝程式：

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

如果您的應用程式使用受控程式碼，則目標電腦上需要 `.NET Framework 4.6.1` 或更新版本。

對於麥克風輸入，必須安裝媒體基礎程式庫。 這些程式庫是 Windows 10 和 Windows Server 2016 的一部分。 沒有這些程式庫也可能使用語音 SDK，只要麥克風並未作為音訊輸入裝置即可。

在與您的應用程式相同的目錄中，可以部署必要的語音 SDK 檔案。 如此一來，您的應用程式就可以直接存取程式庫。 確定您選取與您的應用程式相符的正確版本 (Win32/x64)。

| 名稱 | 功能
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | 核心 SDK (原生和受控部署所需)
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 受控部署所需

>[!NOTE]
> 從版本 1.3.0 開始檔案`Microsoft.CognitiveServices.Speech.csharp.bindings.dll`（隨附於之前的版本），則不需要再。 功能現已整合在 SDK 的核心。

>[!NOTE]
> Windows Forms 應用程式 (.NET Framework)C#專案中，確定程式庫加入您專案的部署設定中。 您可以檢查下`Properties -> Publish Section`。 按一下 `Application Files`按鈕，然後尋找對應的程式庫，從清單中向下捲動。 請確定值設定為`Included`。 Visual Studio 專案發佈/部署時，會包含檔案。

## <a name="linux"></a>Linux

語音 SDK 目前支援的 Ubuntu 16.04，Ubuntu 18.04 Debian 9 的散發套件。
對於原生應用程式，您需要提供語音 SDK 程式庫 `libMicrosoft.CognitiveServices.Speech.core.so`。
確定您選取與您的應用程式相符的版本 (x86、x64)。 視 Linux 版本而定，您可能也需要包含下列相依性：

* GNU C 程式庫的共用程式庫 (包括 POSIX 執行緒程式設計程式庫 `libpthreads`)
* OpenSSL 程式庫 (`libssl.so.1.0.0`或`libssl.so.1.0.2`)
* ALSA 應用程式的共用程式庫 (`libasound.so.2`)

在 Ubuntu 上，預設應該已安裝 GNU C 程式庫。 使用下列命令可以安裝最後三項：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian 9 上安裝這些套件：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
