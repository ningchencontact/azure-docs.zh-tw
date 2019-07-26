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
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404025"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

若要建立共用存取原則, 以授與**服務**連線和登錄**讀取**許可權, 並取得此原則的連接字串, 請遵循下列步驟:

1. 在[Azure 入口網站](https://portal.azure.com)中開啟您的 IoT 中樞。 若要進入 IoT 中樞, 最簡單的方法是選取 [**資源群組**], 選取您的 iot 中樞所在的資源群組, 然後從資源清單中選取您的 iot 中樞。

2. 在 IoT 中樞的左側窗格中, 選取 [**共用存取原則**]。

3. 從原則清單上方的頂端功能表中, 選取 [**新增**]。

4. 在 [**新增共用存取原則**] 窗格中, 輸入原則的描述性名稱;例如: *serviceAndRegistryRead*。 在 [**許可權**] 底下, 選取 [登錄] [**讀取**和**服務連線]** , 然後選取 [**建立**]。

    ![示範如何新增共用存取原則](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. 回到 [**共用存取原則**] 窗格, 從原則清單中選取您的新原則。

6. 在 [**共用存取金鑰**] 底下, 選取 [**連接字串-主要金鑰**] 的複製圖示, 然後儲存值。

    ![顯示如何擷取連接字串](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

如需 IoT 中樞共用存取原則和許可權的詳細資訊, 請參閱[存取控制和許可權](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
