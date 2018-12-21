---
title: 包含檔案
description: 包含檔案
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108017"
---
Azure IoT Edge 的主要功能之一，是能夠從雲端將模組部署到您的 IoT Edge 裝置。 IoT Edge 模組是實作為容器的可執行檔套件。 在本節中，我們會從 [Azure Marketplace 的 IoT Edge 模組區段](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署預先建置的模組。 這個模組會產生模擬裝置的遙測。

1. 在 Azure 入口網站的搜尋中，輸入 `Simulated Temperature Sensor` 並開啟 Marketplace 結果。

   ![Azure 入口網站搜尋中的模擬溫度感應器](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. 在 [訂用帳戶] 欄位中，選取具有您所用 IoT 中樞的訂用帳戶 (如果還沒有的話)。

3. 在 [IoT 中樞] 欄位中，選取您所用 IoT 中樞的名稱 (如果還沒有的話)。

4. 按一下 [尋找裝置]，選取您的 IoT Edge 裝置 (名為 `myEdgeDevice`)，然後選取 [建立]。

5. 在精靈的 [新增模組] 步驟中，按一下 **SimulatedTemperatureSensor** 模組來確認其組態設定，按一下 [儲存] 並選取 [下一步]。

6. 在精靈的 [指定路由] 步驟中，透過可將所有模組的所有訊息傳送至 IoT 中樞的預設路由 (`$upstream`)，確認路由設定正確。 如果沒有，請新增下列程式碼，然後選取 [下一步]。

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. 在精靈的 [檢閱部署] 步驟中，選取 [提交]。

8. 返回裝置的詳細資料頁面，選取 [重新整理]。 除了第一次啟動服務時所建立的 edgeAgent 模組以外，您應該還會看到名為 **edgeHub** 的另一個執行階段模組和 **SimulatedTemperatureSensor** 模組均列出。

   新模組可能需要幾分鐘才會出現。 IoT Edge 裝置必須從雲端擷取其新的部署資訊、啟動容器，然後向 IoT 中樞回報其新的狀態。 

   ![在已部署的模組清單中檢視 SimulatedTemperatureSensor](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
