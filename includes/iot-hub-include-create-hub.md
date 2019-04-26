---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d373bc188a4d93c122cdf2cffbc4323aca16b4a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346123"
---
本節將說明如何使用 [Azure 入口網站](https://portal.azure.com)建立 IoT 中樞。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 選擇 [+建立資源]，然後選擇 [物聯網]。

3. 按一下右側清單中的 [IoT 中樞]。 您會看到用於建立 IoT 中樞的第一個畫面。

   ![在 Azure 入口網站中建立中樞](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   填寫欄位。

   訂用帳戶：選取要為您的 IoT 中樞使用的訂用帳戶。

   **資源群組**：您可以建立新的資源群組，或使用現有的資源群組。 若要建立新的資源群組，請按一下 [新建] 並填入您要使用的名稱。 若要使用現有的資源群組，請按一下 [使用現有的] 並從下拉式清單中選取資源群組。 如需詳細資訊，請參閱[管理 Azure Resource Manager 資源群組](../articles/azure-resource-manager/manage-resource-groups-portal.md)。

   **區域**：這是您要放置中樞的區域。 從下拉式清單中選取最接近您的位置。

   **IoT 中樞名稱**：填入您的 IoT 中樞名稱。 此名稱必須是全域唯一的。 如果您輸入的名稱可用，則會出現綠色核取記號。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. 按一下 **[下一步大小與級別]** 繼續建立 IoT 中樞。

   ![使用 Azure 入口網站設定新 IoT 中樞的大小與級別](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   在此畫面上，您可以接受預設值，只需按一下底部的 [檢閱 + 建立] 即可。 

   **定價與級別層**：您可以依據所需的功能多寡，以及每天透過解決方案傳送的訊息多寡，從數個層級中做選擇。 免費層適用於測試和評估。 它可允許 500 個裝置連接到 IoT 中樞，每天最多可允許 8,000 則訊息。 每個 Azure 訂用帳戶可以在免費層建立一個「IoT 中樞」。 

   **IoT 中樞單位**：每天每單位允許的訊息數目取決於您的中樞定價層。 例如，如果您想要 IoT 中樞支援 700,000 封訊息的輸入，您可以選擇 2 個 S1 層單位。

   如需有關其他層級選項的詳細資料，請參閱[選擇適合的 IoT 中樞層](../articles/iot-hub/iot-hub-scaling.md)。

   **進階/裝置到雲端的分割區**：此屬性會將裝置到雲端的訊息數與同時閱讀訊息的讀者數產生關聯。 大部分的 IoT 中樞只需要四個分割區。 

5. 按一下 [檢閱 + 建立] 以檢閱您的選擇。 您會看到類似這個畫面的內容。

   ![檢閱有關建立新 IoT 中樞的資訊](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. 按一下 [建立] 以建立新的 IoT 中樞。 建立中樞需要幾分鐘的時間。
