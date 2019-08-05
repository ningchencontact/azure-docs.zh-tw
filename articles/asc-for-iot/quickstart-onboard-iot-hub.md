---
title: 在 IoT 中樞內啟用適用於 IoT 的 Azure 資訊安全中心 | Microsoft Docs
description: 了解如何在 IoT 中樞內啟用適用於 IoT 的 Azure 資訊安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: a794ccea13323f38b20906458e216f85652bfc3e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596956"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>快速入門：在 IoT 中樞內讓適用於 IoT 的 Azure 資訊安全中心上線

本文說明如何在現有的 IoT 中樞上啟用適用於 IoT 的 Azure 資訊安全中心服務。 如果您目前沒有 IoT 中樞，請參閱[使用 Azure 入口網站建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)來著手。 

> [!NOTE]
> 適用於 IoT 的 Azure 資訊安全中心目前只支援標準層的 IoT 中樞。
> 適用於 IoT 的 Azure 資訊安全中心是單一中樞的解決方案。 如果您需要多個中樞，則需要使用多個適用於 IoT 的 Azure 資訊安全中心解決方案。 

## <a name="prerequisites-for-enabling-the-service"></a>啟用服務的必要條件

- Log Analytics 工作區
  - 適用於 IoT 的 Azure 資訊安全中心預設會在 Log Analytics 工作區中儲存兩種類型 (**安全性警示**和**建議**) 的資訊。 
  - 您可以選擇新增其他資訊類型 (**未經處理的事件**) 的儲存體。 請注意，在 Log Analytics 中儲存**未經處理的事件**會帶來額外的儲存體成本。 
- IoT 中樞 (標準層)
- 符合所有[服務必要條件](service-prerequisites.md) 

|支援的 Azure 服務區域 | ||
|---|---|---|
| 美國中部 |美國東部 |美國東部 2 |
| 美國中西部 |美國西部 |美國西部 2 |
| 美國中南部|美國中北部 | 加拿大中部|
| 加拿大東部| 北歐|巴西南部|
| 法國中部| 英國西部|英國南部|
|西歐|北歐| 日本西部|
|日本東部 | 澳大利亞東南部|澳洲東部|
|東亞| 東南亞| 南韓中部|
|南韓南部| 印度中部| 印度南部|
|

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>在您的 IoT 中樞上啟用適用於 IoT 的 Azure 資訊安全中心 

若要在 IoT 中樞上啟用安全性，請執行下列步驟︰ 

1. 在 Azure 入口網站中開啟您的 **IoT 中樞**。 
1. 在 [安全性]  功能表之下，按一下 [保護您的 IoT 解決方案]  。
1. 讓 [啟用]  保持已選取狀態作為預設值。 
1. 選取您的記錄分析工作區。
1. 提供 Log Analytics 工作區詳細資料。 
   - 藉由讓 [對應項收集]  開關保持 [開啟]  ，來選擇啟用 [對應項收集]  。
   - 在 Log Analytics 中選取 [儲存未經處理的裝置安全性事件]  ，以選擇除了儲存預設的儲存體資訊類型外，還要儲存**未經處理的事件**。 將 [未經處理的事件]  切換為 [開啟]  。 
    
1. 按一下 [檔案]  。 

恭喜！ 您已在 IoT 中樞上完成適用於 IoT 的 Azure 資訊安全中心啟用。 

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以設定您的解決方案...

> [!div class="nextstepaction"]
> [設定您的解決方案](quickstart-configure-your-solution.md)


