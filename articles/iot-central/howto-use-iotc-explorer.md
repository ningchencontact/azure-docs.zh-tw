---
title: 使用 Azure IoT Central Explorer 監視裝置連線
description: 透過 IoT Central Explorer CLI 監視裝置訊息，並觀察裝置對應項變更。
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779737"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>使用 Azure IoT Central Explorer 監視裝置連線

本主題適用於建置人員和系統管理員。

使用 IoT Central Explorer CLI 查看您裝置傳送至 IoT Central 的訊息，並觀察 IoT 中樞對應項中的變更。 您可以使用這項開放原始碼工具來取得裝置連線狀態的更深入見解，並診斷裝置訊息未到達雲端或裝置未回應對應項變更的問題。

[請瀏覽 iotc 總管 中的存放庫 GitHub。](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>必要條件

+ Node.js 8.x 版或更新版本 - https://nodejs.org
+ 您的應用程式的系統管理員必須先產生存取權杖以供您使用 iotc 總管 中

## <a name="install-iotc-explorer"></a>安裝 iotc explorer

從您的命令列執行下列命令以安裝：

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 您通常需要執行安裝命令並搭配`sudo`Unix 這類環境中。

## <a name="run-iotc-explorer"></a>執行 iotc 總管

下列各節描述常見的命令和選項，在執行時，您可以使用`iotc-explorer`。 若要檢視完整的命令和選項，請傳遞`--help`至`iotc-explorer`或任何它的子命令。

### <a name="login"></a>登入

開始進行之前，您需要讓 IoT Central 應用程式的系統管理員取得存取權杖以供您使用。 系統管理員會採取下列步驟：

1. 瀏覽至**Administration**再**存取權杖**。
1. 選取 **產生權杖**。
    ![存取權杖頁面螢幕擷取畫面](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 輸入的語彙基元名稱，並選取**下一步**，然後**複製**。
    > [!NOTE]
    > 語彙基元的值僅顯示一次，因此必須將它複製之前關閉對話方塊。 關閉對話方塊之後, 它會永遠不會再次顯示。

    ![複製存取權杖對話方塊螢幕擷取畫面](media/howto-use-iotc-explorer/copyaccesstoken.png)

您可以使用權杖來登入 cli，如下所示：

```cmd/sh
iotc-explorer login "<Token value>"
```

如果您不想保存在您的殼層歷程記錄中的權杖，您可以將語彙基元，並改為提供它出現提示時：

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>監視裝置訊息

您可以使用 `monitor-messages` 命令，監看來自您應用程式中特定裝置或所有裝置的訊息。 此命令會啟動持續輸出新郵件抵達的監看員：

若要監看您應用程式中的所有裝置，請執行下列命令：

```cmd/sh
iotc-explorer monitor-messages
```

輸出：

![monitor-messages 命令輸出](media/howto-use-iotc-explorer/monitormessages.png)

若要觀看特定的裝置，只要將裝置識別碼新增至命令的結尾：

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

您也可以藉由新增輸出更方便電腦理解格式`--raw`命令的選項：

```
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

既然您已了解如何使用 IoT Central 總管，建議的下一個步驟是瀏覽[管理裝置 IoT Central](howto-manage-devices.md)。
