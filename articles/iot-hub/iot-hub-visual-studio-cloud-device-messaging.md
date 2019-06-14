---
title: 使用適用於 Visual Studio 的 Cloud Explorer 管理 Azure IoT 中樞雲端服務傳訊 | Microsoft Docs
description: 了解如何使用適用於 Visual Studio 的 Cloud Explorer，在 Azure IoT 中樞監視裝置到雲端訊息和雲端到裝置訊息。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440586"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>使用適用於 Visual Studio 的 Cloud Explorer，在您的裝置和 IoT 中樞之間傳送及接收訊息

![端對端圖表](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是非常有用的 Visual Studio 延伸模組，可讓您在 Visual Studio 內檢視您的 Azure 資源、檢查其屬性，以及執行重要的開發人員動作。 本文著重於如何使用 Cloud Explorer，在裝置與 IoT 中樞之間傳送及接收訊息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>學習目標

了解如何使用適用於 Visual Studio 的 Cloud Explorer，監視裝置到雲端訊息和雲端到裝置訊息。 裝置到雲端的訊息可能是您的裝置所收集，然後傳送到 IoT 中樞的感應器資料。 雲端到裝置訊息可能是您的 IoT 中樞傳送到裝置的命令。 例如，閃爍連接到您裝置的 LED。

## <a name="what-you-will-do"></a>將執行的作業

- 使用適用於 Visual Studio 的 Cloud Explorer 來監視裝置到雲端訊息。
- 使用適用於 Visual Studio 的 Cloud Explorer 來傳送雲端到裝置訊息。

## <a name="what-you-need"></a>您需要什麼

- 有效的 Azure 訂用帳戶。
- 位於您訂用帳戶中的 Azure IoT 中樞。
- Microsoft Visual Studio 2017 Update 8 或更新版本
- 來自 Visual Studio 安裝程式的 Cloud Explorer 元件 (使用 Azure 工作負載的預設選項)

## <a name="update-cloud-explorer-to-latest-version"></a>將 Cloud Explorer 更新至最新版本

來自 Visual Studio 安裝程式的 Cloud Explorer 元件僅支援監視裝置到雲端和雲端到裝置訊息。 為了將訊息傳送到裝置或雲端，您需要下載並安裝最新 [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) \(英文\)。

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

1. 在 Visual Studio [Cloud Explorer]  視窗中，按一下帳戶管理圖示。 您可以從 [檢視]   > [Cloud Explorer]  功能表開啟 Cloud Explorer 視窗。

    ![按一下 [帳戶管理]](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. 在 Cloud Explorer 中按一下 [管理帳戶]  。

3. 若為第一次登入 Azure，請在新視窗中按一下 [新增帳戶]  。

4. 登入之後，系統會顯示您的 Azure 訂用帳戶清單。 選取您想要檢視的 Azure 訂用帳戶並按一下 [套用]  。

5. 展開**您的訂用帳戶** > [IoT 中樞]   > **您的 IoT 中樞**，裝置清單會顯示在您的 IoT 中樞節點下。

    ![裝置清單](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>監視裝置到雲端的訊息

若要監視從您的裝置傳送到 IoT 中樞的訊息，請遵循下列步驟：

1. 在裝置或 IoT 中樞上按滑鼠右鍵，然後選取 [Start Monitoring D2C Message]  \(開始監視 D2C 訊息\)。

    ![開始監視 D2C 訊息](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. 監視的訊息會顯示在 [IoT 中樞]  輸出窗格。

    ![監視 D2C 訊息結果](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. 若要停止監視，請以滑鼠右鍵按一下任何 IoT 中樞或裝置，並選取 [Stop Monitoring D2C Message]  \(停止監視 D2C 訊息\)。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循這些步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message]  \(傳送 C2D 訊息\)。

    ![傳送 C2D 訊息](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. 在輸入方塊中輸入訊息。

3. 結果會顯示在 [IoT 中樞]  輸出窗格。

    ![傳送 C2D 訊息結果](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>後續步驟

您已了解如何監視裝置到雲端的訊息，以及在 IoT 裝置和 Azure IoT 中樞之間傳送雲端到裝置的訊息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]