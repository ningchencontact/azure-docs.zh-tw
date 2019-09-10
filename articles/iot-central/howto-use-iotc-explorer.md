---
title: 使用 Azure IoT Central Explorer 監視裝置連線
description: 透過 IoT Central Explorer CLI 監視裝置訊息，並觀察裝置對應項變更。
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 38cbe43e9038a47c4e222fd4744f0b844f9ddb4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845678"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>使用 Azure IoT Central Explorer 監視裝置連線

本主題適用於建置人員和系統管理員。

使用 IoT Central Explorer CLI 查看您裝置傳送至 IoT Central 的訊息，並觀察 IoT 中樞對應項中的變更。 您可以使用這項開放原始碼工具來取得裝置連線狀態的更深入見解，並診斷裝置訊息未到達雲端或裝置未回應對應項變更的問題。

[流覽 GitHub 中的 iotc-explorer-explorer 存放庫。](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>必要條件

+ Node.js 8.x 版或更新版本 - https://nodejs.org
+ 應用程式的系統管理員必須產生存取權杖，以供您在 iotc-explorer-explorer 中使用

## <a name="install-iotc-explorer"></a>安裝 iotc-explorer-explorer

從您的命令列執行下列命令以安裝：

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 您通常需要在類似 Unix 的環境中`sudo`使用來執行 install 命令。

## <a name="run-iotc-explorer"></a>執行 iotc-explorer-explorer

下列各節說明您可以在執行`iotc-explorer`時使用的常見命令和選項。 若要查看完整的命令和選項組，請`--help`傳遞`iotc-explorer`至或其任何子命令。

### <a name="login"></a>登入

開始進行之前，您需要讓 IoT Central 應用程式的系統管理員取得存取權杖以供您使用。 系統管理員會採取下列步驟：

1. 流覽至 [**管理**]，然後按 [**存取權杖**]。
1. 選取 [**產生權杖**]。
    ![存取權杖頁面螢幕擷取畫面](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 輸入權杖名稱，選取 **[下一步]** ，然後按一下 [**複製**]。
    > [!NOTE]
    > Token 值只會顯示一次，因此必須在關閉對話方塊之前複製。 關閉對話方塊之後，它就不會再次顯示。

    ![複製存取權杖對話方塊螢幕擷取畫面](media/howto-use-iotc-explorer/copyaccesstoken.png)

您可以使用權杖來登入 CLI，如下所示：

```cmd/sh
iotc-explorer login "<Token value>"
```

如果您不想要在 shell 歷程記錄中保存權杖，您可以離開權杖，並改為在出現提示時提供：

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>監視裝置訊息

您可以使用 `monitor-messages` 命令，監看來自您應用程式中特定裝置或所有裝置的訊息。 此命令會啟動監看員，以在收到新訊息時持續加以輸出：

若要監看您應用程式中的所有裝置，請執行下列命令：

```cmd/sh
iotc-explorer monitor-messages
```

輸出：

![monitor-messages 命令輸出](media/howto-use-iotc-explorer/monitormessages.png)

若要監看特定裝置，只要將裝置識別碼新增至命令的結尾：

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

您也可以將`--raw`選項新增至命令，以輸出更容易電腦的格式：

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>取得裝置對應項

您可以使用 `get-twin` 命令取得 IoT Central 裝置的對應項內容。 若要這樣做，請執行下列命令：

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

輸出：

![get-twin 命令輸出](media/howto-use-iotc-explorer/getdevicetwin.png)

如同使用 `monitor-messages`，您可以透過傳遞 `--raw` 選項，來取得更方便電腦辨識的輸出：

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何使用 IoT Central Explorer，建議的下一個步驟是探索如何[IoT Central 管理裝置](howto-manage-devices.md)。
