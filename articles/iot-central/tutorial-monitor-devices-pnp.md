---
title: 在 Azure IoT Central 中監視您的裝置 | Microsoft Docs
description: 身為操作員，您可以使用 Azure IoT Central 應用程式監視您的裝置。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878779"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>教學課程：使用 Azure IoT Central 監視您的裝置 (預覽功能)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本教學課程將為操作員說明如何使用 Microsoft Azure IoT Central 應用程式監視您的裝置和變更設定。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 接收通知
> * 調查問題
> * 補救問題

## <a name="prerequisites"></a>必要條件

在開始之前，建置者應先完成三個建置者教學課程，以建立 Azure IoT Central 應用程式：

* [定義新的裝置類型](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [新增裝置](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [為您的裝置設定規則和動作](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>接收通知

Azure IoT Central 會以電子郵件訊息傳送關於裝置的通知。 建置者已新增會在連線的環境感應器裝置溫度超出臨界值時傳送通知的規則。 請查看傳送至建置者選擇要接收通知之帳戶的電子郵件。

開啟您在[為您的裝置設定規則和動作](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)教學課程末尾收到的電子郵件訊息。 在電子郵件中，選取裝置的連結：

![警示通知電子郵件](media/tutorial-monitor-devices-pnp/email.png)

您在先前的教學課程中建立的環境感應器模擬裝置的 [儀表板]  檢視會在您的瀏覽器中開啟：

![觸發通知電子郵件訊息的裝置](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>調查問題

身為操作員，您可以在 [儀表板]  、[環境感應器屬性]  和 [命令]  頁面上檢視裝置的相關資訊。 建置者自訂了 [儀表板]  和 [環境感應器屬性]  頁面，以顯示與已連線的環境感應器裝置有關的重要資訊。

選擇 [儀表板]  檢視以查看裝置的相關資訊。

儀表板上的圖表會顯示裝置溫度的繪圖。 您也可以在 [裝置屬性]  圖格中檢視裝置目前的目標溫度。 您判定目標溫度過高。

## <a name="remediate-an-issue"></a>補救問題

若要對裝置進行變更，請使用 [環境感應器屬性]  頁面：

1. 選擇 [環境感應器屬性]  。 將 [亮度層級]  變更為 10。 選擇 [儲存]  以更新裝置。 在裝置確認設定變更後，屬性的狀態會變更為 [已同步]  ：

    ![更新設定](media/tutorial-monitor-devices-pnp/change-settings.png)

2. 選擇 [儀表板]  ，並確認新的屬性值：

    ![已更新的裝置儀表板](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

* 接收通知
* 調查問題
* 補救問題

現在您已了解如何監視您的裝置，建議的下一個步驟是：

> [!div class="nextstepaction"]
> [為您的裝置設定規則和動作](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。