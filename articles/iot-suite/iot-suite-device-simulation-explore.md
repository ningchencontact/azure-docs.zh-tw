---
title: "開始使用 Azure 裝置模擬方案 |Microsoft 文件"
description: "IoT 套件模擬方案是一種工具，可用來協助您在開發和測試的 IoT 解決方案。 模擬服務是獨立的供應項目，可以用於搭配其他預先設定的解決方案中或使用您自己自訂的解決方案。"
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
ms.openlocfilehash: 25b9d1c7debe0d98a87b7d0b47dea6ab6f36c8af
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="device-simulation-walkthrough"></a>裝置模擬逐步解說

Azure IoT 裝置模擬是一種工具，可用來協助您在開發和測試的 IoT 解決方案。 裝置模擬是獨立的供應項目，您可以使用連同其他預先設定的解決方案，或您自己自訂的解決方案。

本教學課程將引導您完成某些裝置模擬功能。 它會顯示它如何運作，並可讓您使用它來測試您自己的 IoT 解決方案。

在本教學課程中，您了解如何：

>[!div class="checklist"]
> * 設定模擬
> * 啟動和停止模擬
> * 檢視遙測指標

## <a name="prerequisites"></a>必要條件

若要完成本教學課程中，您需要部署的執行個體的 Azure IoT 裝置模擬您 Azure 訂用帳戶中。

如果您尚未部署裝置模擬，但是您應該先完成[部署 Azure IoT 裝置模擬](iot-suite-device-simulation-explore.md)教學課程。

## <a name="configuring-device-simulation"></a>設定裝置模擬

您可以設定及執行裝置模擬完全從儀表板中。 開啟儀表板從 IoT 套件[佈建解決方案](https://www.azureiotsuite.com/)頁面。 按一下**啟動**下新的裝置模擬部署。

### <a name="target-iot-hub"></a>目標 IoT 中樞

預先佈建的 IoT 中樞或任何其他的 IoT 中樞，您可以使用裝置模擬：

![目標 IoT 中樞](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> 使用 預先佈建的 IoT 中樞的選項才可用，如果您選擇要部署裝置模擬時，建立新的 IoT 中樞。 如果您沒有 IoT 中樞，您一律可以建立一個新從[Azure 入口網站](https://portal.azure.com)。

若要以特定的 IoT 中樞為目標，提供**iothubowner**連接字串。 您可以取得此連接字串從[Azure 入口網站](https://portal.azure.com):

1. 在 Azure 入口網站中 IoT 中樞設定頁面上，按一下 **共用存取原則**。
1. 按一下 [iothubowner]。
1. 將複製是主要或次要金鑰。
1. 將此值貼到文字方塊中，在目標 IoT 中樞。

![目標 IoT 中樞](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>裝置型號

裝置型號，可讓您選擇要模擬的裝置類型。 您可以選擇其中一個預先設定的裝置模型，或定義自訂的裝置型號的感應器的清單：

![裝置型號](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>預先設定的裝置型號

裝置模擬提供三種預先設定的裝置模型。 裝置型號 Chillers、 電梯，和卡車可用。

預先設定的裝置模型包含多個預先決定的遙測頻率的感應器。 您無法自訂這些裝置的遙測頻率。

下表顯示每個預先設定的裝置型號的組態清單：

| 裝置型號 | 感應器 | 單位 | 遙測頻率
| -------------| ------ | -----| --------------------|
| Chiller | 溼度 | % | 5 秒 |
| | pressure | psig | 5 秒 |
| | 溫度 | F | 5 秒 |
| 電梯 | floor | | 5 秒 |
| | 震動 | mm | 5 秒 |
| | 溫度 | F | 5 秒 |
| 卡車 | 緯度 | | 3 秒 |
| | 經度 | | 3 秒 |
| | 速度 | 哩 | 5 秒 |
| | cargotemperature | F | 5 秒 |

#### <a name="custom-device-model"></a>自訂的裝置型號

自訂裝置模型可讓您更密切地呈現您自己的裝置的模型感應器。 自訂的裝置皆可擁有最多 10 個自訂的感應器。

當您選取自訂的裝置模型類型時，您可以加入新的感應器按一下**+ 新增感應器**。

![新增感應器](media/iot-suite-device-simulation-explore/customsensors.png)

自訂的感應器具有下列屬性：

| 欄位 | 說明 |
| ----- | ----------- |
| 感應器名稱 | 例如感應器的易記名稱**溫度**或**速度**。 |
| 行為 | 行為讓您遙測資料會從一個訊息到下一個来模擬真實世界的資料。 **遞增**增加一個傳送的最小值處開始每個訊息中的值。 一旦符合最大值，然後它會開始透過再次最小值。 **遞減**行為方式與**遞增**但向下計數。 **隨機**行為會產生最小值和最大值之間的隨機值。 |
| 最小值 | 代表可接受的範圍的最低數字。 |
| 最大值 | 代表可接受的範圍最大數目。 |
| 單位 | 例如 ° F 或哩感應器的度量單位。 |

### <a name="number-of-devices"></a>裝置數目

裝置模擬目前可讓您模擬 1,000 個裝置。

![裝置數目](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>遙測頻率

遙測頻率可讓您指定模擬的裝置到 IoT 中樞傳送資料的頻率。 您可以將資料傳送為每隔 10 秒鐘而經常當做或不常為每個為 99 個小時，59 分鐘的時間和 59 秒。

![遙測頻率](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> 如果您使用 IoT 中樞預先佈建的 IoT 中樞以外，您應該針對您目標的 IoT 中樞考慮訊息限制。 比方說，如果您有 1000 模擬的裝置傳送遙測每隔 10 秒到 S1 中心您達到遙測中中樞只要一小時。

### <a name="simulation-duration"></a>模擬持續時間

您可以選擇執行您的模擬特定的時間長度，或執行，直到明確停止為止。 當您選擇的特定長度的時間時，您可以選擇任何持續時間從 10 分鐘，最多 99 的小時、 59 分鐘的時間和 59 的秒數。

![模擬持續時間](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>啟動和停止模擬

當所有必要的組態資料加入表單，**開始模擬**按鈕啟用。 若要模擬，請按一下此按鈕。

![開始模擬](media/iot-suite-device-simulation-explore/start.png)

如果您指定特定的持續期間內，為您的模擬，則它會停止自動時間到達時。 您可以一律停駐模擬早期按一下**停止模擬。**

如果您選擇要無限期地執行您的模擬，則它會執行，直到您按一下**停止模擬**。 您可以關閉您的瀏覽器，然後返回裝置模擬頁面，即可停止您在任何時間的模擬。

![停止模擬](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> 只有一個模擬可以執行一次。 您必須停止目前正在執行模擬，才能開始新的模擬。
