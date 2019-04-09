---
title: 使用適用於 Visual Studio 的 Cloud Explorer 來管理 Azure IoT 裝置 | Microsoft Docs
description: 使用適用於 Visual Studio 的 Cloud Explorer 來管理 Azure IoT 中樞裝置，並採用直接方法和對應項所需的屬性管理選項。
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: xshi
ms.openlocfilehash: 87a0847f5d42e014f3b2691c96446892176b481b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269566"
---
# <a name="use-cloud-explorer-for-visual-studio-for-azure-iot-hub-device-management"></a>使用適用於 Visual Studio 的 Cloud Explorer 來管理 Azure IoT 中樞裝置

![端對端圖表](media/iot-hub-device-management-visual-studio/iot-e2e-simple.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 是非常有用的 Visual Studio 延伸模組，可讓您在 Visual Studio 內檢視您的 Azure 資源、檢查其屬性，以及執行重要的開發人員動作。 它隨附的管理選項可供您用來執行各種工作。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理選項          | Task                    |
|----------------------------|--------------------------------|
| 直接方法             | 使裝置執行動作，例如啟動或停止傳送訊息，或是將裝置重新開機。                                        |
| 讀取裝置對應項           | 取得裝置的報告狀態。 例如，裝置會回報 LED 現在正閃爍不停。                                    |
| 更新裝置對應項         | 讓裝置進入特定狀態，例如將 LED 設定為綠色，或將遙測傳送間隔設定為 30 分鐘。         |
| 雲端到裝置的訊息   | 將通知傳送至裝置。 例如，「今天很可能會下雨。 別忘了帶傘。」              |

如需差異的詳細說明和使用這些選項的相關指引，請參閱[裝置對雲端通訊指引](iot-hub-devguide-d2c-guidance.md)和[雲端對裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

「裝置對應項」是存放裝置狀態資訊 (中繼資料、組態和條件) 的 JSON 文件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。 如需裝置對應項的詳細資訊，請參閱[開始使用裝置對應項](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>您學到什麼

您了解如何使用適用於 Visual Studio 的 [雲端總管] 中，搭配您的開發電腦上的各種管理選項。

## <a name="what-you-do"></a>您要做什麼

使用各種管理選項來執行適用於 Visual Studio 的 Cloud Explorer。

## <a name="what-you-need"></a>您需要什麼

- 作用中的 Azure 訂用帳戶
- Azure IoT 中樞訂用帳戶
- Microsoft Visual Studio 2017 Update 8 或更新版本
- 來自 Visual Studio 安裝程式的 Cloud Explorer 元件 (使用 Azure 工作負載的預設選項)

## <a name="update-cloud-explorer-to-latest-version"></a>將 Cloud Explorer 更新至最新版本

來自 Visual Studio 安裝程式的 Cloud Explorer 元件僅支援監視裝置到雲端和雲端到裝置訊息。 您必須下載並安裝最新的 [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) 才能存取管理選項。

## <a name="sign-in-to-access-your-iot-hub"></a>登入以存取 IoT 中樞

1. 在 Visual Studio [Cloud Explorer] 視窗中，按一下帳戶管理圖示。 您可以從 [檢視] > [Cloud Explorer] 功能表開啟 Cloud Explorer 視窗。

    ![按一下 [帳戶管理]](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. 在 Cloud Explorer 中按一下 [管理帳戶]。
1. 若為第一次登入 Azure，請在新視窗中按一下 [新增帳戶]。
1. 登入之後，系統會顯示您的 Azure 訂用帳戶清單。 選取您想要檢視的 Azure 訂用帳戶並按一下 [套用]。
1. 展開**您的訂用帳戶** > [IoT 中樞] > **您的 IoT 中樞**，裝置清單會顯示在您的 IoT 中樞節點下。 以滑鼠右鍵按一下某個裝置，以存取管理選項。

    ![管理選項](media/iot-hub-device-management-visual-studio/management-options.png)

## <a name="direct-methods"></a>直接方法

1. 以滑鼠右鍵按一下您的裝置，然後選取 [叫用裝置直接方法]。
1. 在輸入方塊中輸入方法名稱和承載。
1. 結果會顯示在 [IoT 中樞] 輸出窗格。

## <a name="read-device-twin"></a>讀取裝置對應項

1. 以滑鼠右鍵按一下您的裝置，然後選取 [編輯裝置對應項]。
1. **azure-iot-device-twin.json** 檔案與裝置對應項的內容一起開啟。

## <a name="update-device-twin"></a>更新裝置對應項

1. 對 **azure-iot-device-twin.json** 檔案適度編輯 **tags** 或 **properties.desired** 欄位。
1. 按 **Ctrl+S** 來更新裝置對應項。
1. 結果會顯示在 [IoT 中樞] 輸出窗格。

## <a name="send-cloud-to-device-messages"></a>傳送雲端到裝置訊息

若要從您的 IoT 中樞將訊息傳送到裝置，請遵循這些步驟：

1. 以滑鼠右鍵按一下您的裝置，然後選取 [Send C2D Message] \(傳送 C2D 訊息\)。
1. 在輸入方塊中輸入訊息。
1. 結果會顯示在 [IoT 中樞] 輸出窗格。

## <a name="next-steps"></a>後續步驟

您已了解如何搭配各種管理選項來使用適用於 Visual Studio 的 Cloud Explorer。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]