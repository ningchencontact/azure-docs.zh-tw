---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248933"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

[取用者群組](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)提供獨立的檢視事件資料流可讓應用程式與 Azure 服務來單獨使用相同的事件中樞端點的資料。 在本節中，您可以新增取用者群組到您的 IoT 中樞內建端點端點用於本教學課程稍後提取資料。

若要將取用者群組新增至 IoT 中樞，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 IoT 中樞。

2. 在左窗格中，選取**內建端點**，選取**事件**在右窗格中，輸入的名稱**取用者群組**。 選取 [ **儲存**]。

   ![在 IoT 中樞中建立取用者群組](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
