---
title: 開始使用裝置模擬解決方案 - Azure | Microsoft Docs
description: IoT 套件模擬方案是一種工具，可用來協助開發和測試 IoT 解決方案。 模擬服務是一個獨立的供應項目，可以與其他預先設定的解決方案一起使用，或搭配您自己的自訂解決方案使用。
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1452508822f08d4554f419a72f7e9e6018a52469
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="device-simulation-walkthrough"></a>裝置模擬逐步解說

Azure IoT 裝置模擬是一種工具，可用來協助開發和測試 IoT 解決方案。 裝置模擬是一個獨立的供應項目，可以與其他預先設定的解決方案一起使用，或搭配您自己的自訂解決方案使用。

本教學課程將引導您完成部分裝置模擬功能。 它會向您示範其運作方式，並可讓您用於測試您自己的 IoT 解決方案。

在本教學課程中，您了解如何：

>[!div class="checklist"]
> * 設定模擬
> * 開始和停止模擬
> * 檢視遙測計量

## <a name="prerequisites"></a>先決條件

若要完成本教學課程，您需要在 Azure 訂用帳戶中有一個已部署的 Azure IoT 裝置模擬執行個體。

如果您尚未部署裝置模擬，您應該先完成[部署 Azure IoT 裝置模擬](iot-suite-device-simulation-deploy.md)教學課程。

## <a name="configuring-device-simulation"></a>設定裝置模擬

您可以從儀表板內完整設定並執行裝置模擬。 從 IoT 套件的 [已佈建的解決方案](https://www.azureiotsuite.com/) 頁面開啟儀表板。 按一下新「裝置模擬」部署底下的 [啟動]。

### <a name="target-iot-hub"></a>目標 IoT 中樞

您可以使用「裝置模擬」搭配預先佈建的 IoT 中樞或其他任何 IoT 中樞：

![目標 IoT 中樞](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> 只有當您在部署裝置模擬時選擇建立新 IoT 中樞的情況下，才能使用預先佈建的 IoT 中樞的選項。 如果您沒有 IoT 中樞，一律可以從 [Azure 入口網站](https://portal.azure.com)建立一個新的 IoT 中樞。

若要以特定 IoT 中樞為目標，請提供 **iothubowner** 連接字串。 您可以從 [Azure 入口網站](https://portal.azure.com)取得此連接字串：

1. 在 Azure 入口網站中的 [IoT 中樞設定] 頁面上，按一下 [共用存取原則]。
1. 按一下 [iothubowner]。
1. 複製主要金鑰或次要金鑰。
1. 將此值貼到 [目標 IoT 中樞] 底下的文字方塊中。

![目標 IoT 中樞](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>裝置型號

裝置型號可讓您選擇要模擬的裝置類型。 您可以選擇其中一個預先設定的裝置型號，或針對自訂裝置型號定義一個感應器清單：

![裝置型號](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>預先設定的裝置型號

「裝置模擬」提供三種預先設定的裝置型號。 提供適用於 Chillers、電梯和卡車的裝置型號。

預先設定的裝置模型包含多個感應器與 JavaScript 檔案中定義的進階行為。 Web UI 中不支援這些自訂行為。 

下表針對每個預先設定的裝置型號，顯示設定清單：

| 裝置型號 | 感應器 | 單位 | 
| -------------| ------ | -----| 
| Chiller | 溼度 | % |
| | pressure | psig | 
| | 溫度 | F | 
| 電梯 | 樓層 | 
| | 震動 | mm | 
| | 溫度 | F | 
| 卡車 | 緯度 | |
| | 經度 | | 
| | 速度 | 英哩/小時 | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>自訂裝置型號

自訂裝置型號可讓您製作更能代表您自己裝置的感應器型號。 自訂裝置最多可以擁有 10 個自訂感應器。

當您選取自訂裝置型號類型時，可以按一下 [+ 新增感應器] 來加入新的感應器。

![新增感應器](media/iot-suite-device-simulation-explore/customsensors.png)

自訂感應器具有下列屬性：

| 欄位 | 說明 |
| ----- | ----------- |
| 感應器名稱 | 易記的感應器名稱，例如**溫度**或**速度**。 |
| 行為 | 行為可讓遙測資料根據不同的訊息而變化，以模擬真實世界的資料。 **遞增**可在傳送的每個訊息中將其值加 1 (從最小值開始)。 一旦符合最大值，就會再從最小值開始。 **遞減**的運作方式與**遞增**相同，但是為倒數。 **隨機**行為會產生一個介於最小值到最大值之間的隨機值。 |
| 最小值 | 代表可接受範圍的最小數字。 |
| 最大值 | 代表可接受範圍的最大數字。 |
| 單位 | 感應器的度量單位，例如 °F 或英哩/小時。 |

### <a name="number-of-devices"></a>裝置數目

「裝置模擬」目前可讓您模擬最多 20,000 部裝置。

![裝置數目](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>遙測頻率

遙測頻率可讓您指定模擬的裝置將資料傳送到 IoT 中樞的頻率。 您可以每 10 秒傳送資料一次，或者每 99 小時 59 分鐘 59 秒傳送一次。

![遙測頻率](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> 如果您使用的是 IoT 中樞而非預先佈建的 IoT 中樞，則應該針對您的目標 IoT 中樞考慮訊息限制。 例如，如果您有 1,000 部模擬的裝置每 10 秒將遙測資料傳送到 S1 中樞一次，則只需要一個多小時就可以達到中樞的遙測限制。

### <a name="simulation-duration"></a>模擬持續時間

您可以選擇在特定的時間長度執行模擬，或者執行到您明確停止為止。 當您選擇特定的時間長度時，可以選擇任何持續時間，從 10 分鐘到最多 99 小時 59 分鐘 59 秒。

![模擬持續時間](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>開始和停止模擬

當您已經將所有必要的設定資料新增至表單時，[開始模擬] 按鈕就會啟用。 若要開始模擬，請按一下此按鈕。

![開始模擬](media/iot-suite-device-simulation-explore/start.png)

如果您為您的模擬指定特定的持續時間，則它會在到達該時間時自動停止。 您一律可以按一下 [停止模擬]，以提早停止模擬。

如果您選擇要無限期地執行您的模擬，則它會執行到您按一下 [停止模擬] 為止。 您可以隨時關閉瀏覽器並回到 [裝置模擬] 頁面，以停止模擬。

![停止模擬](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> 一次只能執行一個模擬。 您必須停止目前正在執行的模擬，才能開始新的模擬。
