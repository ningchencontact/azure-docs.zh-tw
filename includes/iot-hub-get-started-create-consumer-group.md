---
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 242f601ced4838a1b4e559774c25d05de04ddb77
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2019
ms.locfileid: "57016373"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>將取用者群組新增至 IoT 中樞

應用程式會使用取用者群組從 Azure IoT 中樞提取資料。 在本教學課程中，您會建立即將推出的 Azure 服務要使用的取用者群組，以便從 IoT 中樞讀取資料。

若要將取用者群組新增至 IoT 中樞，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 IoT 中樞。

2. 在左窗格中，按一下 [內建端點]，選取頂端窗格的 [事件]，然後在右窗格的 [取用者群組] 之下輸入名稱。 在變更 [預設 TTL] 值之後按一下 [儲存]，然後將該值恢復為原始值。

   ![在 IoT 中樞中建立取用者群組](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
