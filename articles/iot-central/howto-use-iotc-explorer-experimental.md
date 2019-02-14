---
title: 使用 Azure IoT Central Explorer 監視裝置連線
description: 透過 IoT Central Explorer CLI 監視裝置訊息，並觀察裝置對應項變更。
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e510cfbd89ab8dcd8dccd9a8b95a49a057c9b54f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824856"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>使用 Azure IoT Central Explorer 監視裝置連線

本主題適用於建置人員和系統管理員。

使用 IoT Central Explorer CLI 查看您裝置傳送至 IoT Central 的訊息，並觀察 IoT 中樞對應項中的變更。 您可以使用這項開放原始碼工具來取得裝置連線狀態的更深入見解，並診斷裝置訊息未到達雲端或裝置未回應對應項變更的問題。

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[瀏覽 GitHub 中的 iotc-explorer 存放庫](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>必要條件
+ Node.js 8.x 版或更新版本 - https://nodejs.org
+ 您需要讓應用程式的系統管理員產生存取權杖以供您用於 iotc-explorer

## <a name="installing-iotc-explorer"></a>安裝 iotc-explorer

從您的命令列執行下列命令以安裝：

```
npm install -g iotc-explorer
```

> [!NOTE]
> 您通常需要在 Unix 類環境中使用 `sudo` 來執行 install 命令。

## <a name="running-iotc-explorer"></a>執行 iotc-explorer

以下是使用 `iotc-explorer` 時可執行的一些命令和常見選項。 若要檢視完整的命令和選項集，您可以將 `--help` 傳遞至 `iotc-explorer` 或其任何子命令。

### <a name="login"></a>登入

開始進行之前，您需要讓 IoT Central 應用程式的系統管理員取得存取權杖以供您使用。 系統管理員會採取下列步驟：
1. 移至 [管理]/[存取權杖]。 
1. 按一下 [產生]。
    ![存取權杖頁面螢幕擷取畫面](media/howto-use-iotc-explorer-experimental/accesstokenspage.png)

1. 輸入權杖名稱，按一下 [下一步]，然後**複製權杖值**。
    > [!NOTE]
    > 權杖值只會顯示一次，因此必須先加以複製，再關閉對話方塊。 關閉對話方塊之後，永遠不會再次顯示該資訊。

    ![複製存取權杖對話方塊螢幕擷取畫面](media/howto-use-iotc-explorer-experimental/copyaccesstoken.png)

您接著可以執行下列命令，來使用該權杖登入 CLI：

```sh
iotc-explorer login "<Token value>"
```

如果您不想要在殼層歷程記錄中保存權杖，您可以移除權杖，並改為在出現提示時提供：

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>監視裝置訊息

您可以使用 `monitor-messages` 命令，監看來自您應用程式中特定裝置或所有裝置的訊息。 這會啟動監看員，以在新的訊息抵達時持續將它輸出。

若要監看您應用程式中的所有裝置，請執行下列命令：

```
iotc-explorer monitor-messages
```

輸出：

![monitor-messages 命令輸出](media/howto-use-iotc-explorer-experimental/monitormessages.png)

若要監看特定裝置，只要將裝置識別碼新增至命令結尾即可：

```
iotc-explorer monitor-messages <your-device-id>
```

您也可以透過將 `--raw` 選項新增至命令，來讓命令輸出更方便電腦辨識的格式：

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>取得裝置對應項

您可以使用 `get-twin` 命令取得 IoT Central 裝置的對應項內容。 若要這樣做，請執行下列命令：

```
iotc-explorer get-twin <your-device-id>
```

輸出：

![get-twin 命令輸出](media/howto-use-iotc-explorer-experimental/getdevicetwin.png)

如同使用 `monitor-messages`，您可以透過傳遞 `--raw` 選項，來取得更方便電腦辨識的輸出：

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>後續步驟
您現在已了解如何使用 IoT Central Explorer，建議執行下一個步驟，以探索如何[在 IoT Central 中管理裝置](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。
