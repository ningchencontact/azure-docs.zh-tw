---
title: 建置 Azure IoT Central 裝置橋接器 | Microsoft Docs
description: 建置用來將其他 IoT 雲端 (Sigfox、粒子、物聯網等) 連線至 IoT Central 應用程式的 IoT Central 裝置橋接器。
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/8/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 83f053a8815f31803f536920497fdc42e72d2a2d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628421"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>建置用來將其他 IoT 雲端連線至 IoT Central 的 IoT Central 裝置橋接器

*此主題適用於系統管理員。*

IoT Central 裝置橋接器是一項開放原始碼解決方案，可將您的 Sigfox、粒子、物聯網和其他雲端連線至 IoT Central 應用程式。 無論您使用連線至 Sigfox 低功率廣域網路的資產追蹤裝置，還是在粒子裝置雲端上使用空氣品質監視裝置，或是在 TTN 上使用土壤濕度監視裝置，您都可以透過 IoT Central 裝置橋接器直接利用 IoT Central 的強大功能。 裝置橋接器可藉由將您的裝置傳送到其他雲端的資料轉送至 IoT Central 應用程式，來連線其他 IoT 雲端與 IoT Central。 在您的 IoT Central 應用程式中，您可以建置規則並對該資料執行分析、在 Microsoft Flow 和 Azure 邏輯應用程式中建立工作流程、匯出該資料，以及其他工作。 從 Github取得 [IoT Central 裝置橋接器](https://aka.ms/iotcentralgithubdevicebridge)

## <a name="what-is-it-and-how-does-it-work"></a>這是什麼？如何運作？
IoT Central 裝置橋接器是 Github 中的開放原始碼解決方案。 您可以直接使用 [部署至 Azure] 按鈕，將具有數項 Azure 資源的自訂 Azure Resource Manager 範本部署到您的 Azure 訂用帳戶。 這些資源包括：
-   Azure 函式應用程式
-   Azure 儲存體帳戶
-   App Service 方案 (S1 層)
-   Azure Key Vault：此函式應用程式是裝置橋接器的重要部分。 它可透過簡單的 Webhook 整合，從其他 IoT 平台或任何自訂平台接收 HTTP POST 要求。 我們提供了範例來說明如何連線至 Sigfox、粒子和 TTN 雲端。 如果您的平台可將 HTTP POST 要求傳送至函式應用程式，您即可輕鬆地擴充此解決方案以連線至您的自訂 IoT 雲端。
函式應用程式可將資料轉換成 IoT Central 可接受的格式，並透過 DPS API 加以轉送。

![Azure 函式螢幕擷取畫面](media/howto-build-iotc-device-bridge/azfunctions.png)

如果您的 IoT Central 應用程式可透過轉送訊息中的裝置識別碼來辨識裝置，即會出現該裝置的新量測。 如果您的 IoT Central 應用程式無法辨識裝置識別碼，函式應用程式將會嘗試使用該裝置識別碼註冊新裝置，且該裝置在您的 IoT Central 應用程式中會顯示為「無關聯的裝置」。 

## <a name="how-do-i-set-it-up"></a>如何加以設定？
相關指示會詳列在 Github 存放庫內的 README 檔案中。 

## <a name="pricing"></a>價格
這全部裝載於您的 Azure 訂用帳戶中。 佈建資源的預估成本大多來自於[標準 App Service 方案的價格]( https://azure.microsoft.com/en-us/pricing/details/app-service/windows/)。 您可以在 README 檔案中深入了解其相關資訊，以及有可能降低成本的方式。

## <a name="next-steps"></a>後續步驟

您現在已了解如何建置 IoT Central 裝置橋接器，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的裝置](howto-manage-devices.md)