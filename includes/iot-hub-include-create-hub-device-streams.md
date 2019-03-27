---
title: 包含檔案 (裝置串流預覽版)
description: 包含檔案 (裝置串流預覽版)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: d1cfe3d998c08aef2b845315a16d881dea1cd1f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124349"
---
本節將說明如何使用 [Azure 入口網站](https://portal.azure.com)建立 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選擇 [+建立資源]，然後選擇 [物聯網]。

3. 按一下右側清單中的 [IoT 中樞]。 您會看到用於建立 IoT 中樞的第一個畫面。

   ![顯示在 Azure 入口網站中建立中樞的螢幕擷取畫面](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   填寫欄位：

   訂用帳戶：選取要為您的 IoT 中樞使用的訂用帳戶。

   **資源群組**：您可以建立新的資源群組，或使用現有的資源群組。 若要建立新的資源群組，請按一下 [新建] 並填入您要使用的名稱。 若要使用現有的資源群組，請按一下 [使用現有的] 並從下拉式清單中選取資源群組。 如需詳細資訊，請參閱[管理 Azure Resource Manager 資源群組](../articles/azure-resource-manager/manage-resource-groups-portal.md)。

   **區域**：這是您要放置中樞的區域。 選取支援 IoT 中樞裝置串流預覽的區域，可能是 [美國中部] 或 [美國中部 EUAP]。

   **IoT 中樞名稱**：填入您的 IoT 中樞名稱。 此名稱必須是全域唯一的。 如果您輸入的名稱可用，則會出現綠色核取記號。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. 按一下 **[下一步大小與級別] 繼續建立 IoT 中樞。

   ![顯示使用 Azure 入口網站之新 IoT 中樞的設定大小與級別的螢幕擷取畫面](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)

   在此畫面上，您可以接受預設值，只需按一下底部的 [檢閱 + 建立] 即可。

   **定價與級別層**：請確實選取其中一個標準層 (S1、S2、S3) 或免費層 (F1)。 這項選擇也可依據您的機群規模以及您預期中樞所將包含的非串流工作負載 (例如遙測訊息) 來決定。 例如，免費層適用於測試和評估。 它可允許 500 個裝置連接到 IoT 中樞，每天最多可允許 8,000 則訊息。 每個 Azure 訂用帳戶可以在免費層建立一個「IoT 中樞」。 

   **IoT 中樞單位**：這項選擇取決於您預期中樞所將包含的非串流工作負載 - 您可以暫且選取 1。

   如需有關其他層級選項的詳細資料，請參閱[選擇適合的 IoT 中樞層](../articles/iot-hub/iot-hub-scaling.md)。

5. 按一下 [檢閱 + 建立] 以檢閱您的選擇。 您會看到類似這個畫面的內容。

   ![檢閱有關建立新 IoT 中樞之資訊的螢幕擷取畫面](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. 按一下 [建立] 以建立新的 IoT 中樞。 建立中樞需要幾分鐘的時間。
