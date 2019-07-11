---
title: 語音裝置 SDK Roobo 智慧音訊開發套件 v1-語音服務
titleSuffix: Azure Cognitive Services
description: 先決條件與指示開始使用語音裝置 SDK，Roobo 智慧音訊開發套件 v1。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: e4aba26238a0d87c8e708ae27c7b2dbdb73f16ab
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604841"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>裝置：Roobo 智慧音訊的開發人員套件

本文章提供 Roobo 智慧音訊開發套件的裝置特定資訊。

## <a name="set-up-the-development-kit"></a>設定開發套件

1. 開發套件有兩個 micro USB 連接器。 左側的連接器用來對開發套件供電，在下圖中會以 Power 來醒目提示。 右邊的連接器則用來控制開發套件，在圖中會標示為 Debug。

    ![連接開發套件](media/speech-devices-sdk/qsg-1.png)

1. 使用 micro USB 纜線將電源連接埠連接至電腦或電源配接器，以對開發套件供電。 上方面板底下會亮起綠色電源指示燈。

1. 若要控制開發套件，連線到電腦的偵錯連接埠，使用第二個 micro USB 纜線。 請務必使用高品質的纜線，以確保可靠的通訊。

1. 將您的開發套件依圓形或線性設定調整方向。

    |開發套件設定|方向|
    |-----------------------------|------------|
    |圓形|直立，麥克風朝向天花板|
    |線性|側邊，麥克風朝向您 (如下圖所示)|

    ![線性開發套件方向](media/speech-devices-sdk/qsg-2.png)

1. 安裝憑證，並設定音效裝置的權限。 在命令提示字元視窗中輸入下列命令：

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > 這些命令會使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安裝的一部分。 此工具位於 C:\Users\[使用者名稱]\AppData\Local\Android\Sdk\platform-tools。 您可以將此目錄新增至您的路徑，以便更方便地叫用 `adb`。 否則，您必須在叫用 `adb` 的每個命令中指定 adb.exe 的完整安裝路徑。
    >
    > 如果您看到錯誤`no devices/emulators found`則請確認您的 USB 纜線已連接且高品質纜線。 您可以使用 `adb devices` 來確認電腦是否可以與開發套件通訊，以便其可傳回裝置清單。
    >
    > [!TIP]
    > 請將您電腦的麥克風和喇叭靜音，以確定您使用的是開發套件的麥克風。 如此一來，您就不會不小心讓來自電腦的音訊觸發該裝置。

1. 如果您想要將喇叭連結到開發套件，您可以將它連接到音訊線路輸出。您應該選擇與 3.5 公釐類比隨插即用良好品質說話者。

    ![Vysor 音訊](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>開發資訊

如需開發資訊，請參閱[Roobo 開發指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio"></a>音訊

Roobo 提供工具，可擷取所有的音訊到 flash 記憶體。 它可以協助您針對音訊問題進行疑難排解。 針對每個開發套件設定提供了工具版本。 在  [Roobo 站台](https://ddk.roobo.com/)，選取您的裝置，然後選取**Roobo 工具**在頁面底部的連結。

## <a name="next-steps"></a>後續步驟

* [執行 Android 範例應用程式](speech-devices-sdk-android-quickstart.md)
