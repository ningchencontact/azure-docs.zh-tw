---
title: 瞭解 IoT 成本的 Azure 資訊安全中心 |Microsoft Docs
description: 瞭解與 IoT Azure 資訊安全中心相關聯的成本, 以及如何控制它們。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 603df1def011232ad2120c37ad1ba256f2a30526
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596509"
---
# <a name="pricing-and-associated-costs"></a>定價和相關成本

本文說明 IoT 定價模式的 Azure 資訊安全中心、摘要所有相關成本, 並說明如何管理它們。

## <a name="pricing"></a>定價

IoT 計價模式的 Azure 資訊安全中心由兩個部分組成, 一旦在 IoT 的 Azure 資訊安全中心中[啟用](quickstart-onboard-iot-hub.md)IoT 中樞, 就會計費:

- 根據 IoT 中樞記錄分析的裝置內建安全性功能所產生的成本。

- 根據來自 IoT Edge 或分葉裝置的安全性訊息, 以訊息增強的安全性功能來計算成本。

  >[!Note]
  > 安全性訊息也會產生 IoT 中樞的配額耗用量。

如需詳細資訊, 請參閱[資訊安全中心定價](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>相關成本

IoT 的 Azure 資訊安全中心有兩種相關聯的成本, 而不是直接定價的一部分:

- IoT 中樞配額耗用量

- Log Analytics 儲存體成本

藉由變更您的設定, 您可以藉由退出宣告特定功能來降低相關成本。

若要變更您的設定:

1. 開啟 IoT 中樞。

2. 在 [**安全性**] 底下, 按一下 **[總覽**]。

3. 按一下 [設定]。

下表提供每個選項的相關費用和含意的摘要。

|     | 使用量 | 註解 |
| --- | --- | --- |
| **IoT 中樞配額耗用量** |  |
| [匯出裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices)作業 (對應項匯出) | 一天 1 次 | 停用對應項_元資料集合_ |
| **Log Analytics 儲存體** |  |
| 裝置建議和警示| 服務產生的安全性建議和警示 | 非選擇性 |
| 原始的安全性資料| 來自 IoT 裝置的原始安全性資料, 由安全性代理程式收集 | 停用_儲存原始裝置安全性事件_ |

>[!Important]
> 退出會對可用的安全性功能造成嚴重的影響。
  
| 退出 | 產生 |
| --- | --- |
| _對應項元資料集合_ | 停用[自訂警示](quickstart-create-custom-alerts.md) |
| | 停用 IoT Edge 資訊清單建議 |
| | 停用裝置身分識別型建議和警示 |
| _儲存原始裝置安全性事件_ | 裝置作業系統基準建議的詳細資料無法使用 |
| | [警示](concept-security-alerts.md)和[建議](concept-recommendations.md)調查的詳細資料無法使用 |
|


## <a name="see-also"></a>另請參閱

- 存取未經處理的[安全性資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 瞭解及探索[安全性建議](concept-recommendations.md)
- 瞭解及探索[安全性警示](concept-security-alerts.md)
