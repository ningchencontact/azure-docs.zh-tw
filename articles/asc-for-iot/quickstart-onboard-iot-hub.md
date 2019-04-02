---
title: 在 IoT 中樞內啟用適用於 IoT 的 ASC 服務 (預覽) | Microsoft Docs
description: 了解如何在 IoT 中樞內啟用適用於 IoT 的 ASC 服務。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4fd7fb68fd2eb8be0c1c109d76cff341faba9391
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541708"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>快速入門：在 IoT 中樞內啟用服務

> [!IMPORTANT]
> 適用於 IoT 的 ASC 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何在 IoT 中樞上啟用適用於 IoT 的 ASC 預覽服務。  

> [!NOTE]
> 適用於 IoT 的 ASC 目前只支援標準層和更高層的 IoT 中樞。
>適用於 IoT 的 ASC 是單一中樞的解決方案。 如果您需要多個中樞，則需要使用多個解決方案。 

## <a name="prerequisites-for-enabling-the-service"></a>啟用服務的必要條件

- Log Analytics 工作區
  - 適用於 IoT 的 ASC 預設會在 Log Analytics 工作區中儲存兩種類型 (**安全性警示**和**建議**) 的資訊。 
  - 您可以選擇新增其他資訊類型 (**未經處理的事件**) 的儲存體。 請注意，在 Log Analytics 中儲存**未經處理的事件**會帶來額外的儲存體成本。 
- IoT 中樞 (標準層或更高層)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>在 IoT 中樞上啟用適用於 IoT 的 ASC 

若要在 IoT 中樞上啟用安全性，請執行下列步驟︰ 

1. 在 Azure 入口網站中開啟您的 **IoT 中樞**。 
2. 從左側功能表中選取並開啟 [安全性]。 
3. 選擇 [啟用 IoT 安全性]。 
4. 提供 Log Analytics 工作區詳細資料。 
   - 藉由讓 [未經處理的事件] 開關保持 [開啟]，來選擇除了儲存預設的儲存體資訊類型外，還要儲存**未經處理的事件**。 
   - 藉由讓 [對應項收集] 開關保持 [開啟]，來選擇啟用 [對應項收集]。 
5. 按一下 [確定]。 
6. 按一下 [檔案] 。 

恭喜！ 您已完成在 IoT 中樞上啟用 ASC 的工作。 

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何設定您的解決方案...

> [!div class="nextstepaction"]
> [設定您的解決方案](quickstart-configure-your-solution.md)