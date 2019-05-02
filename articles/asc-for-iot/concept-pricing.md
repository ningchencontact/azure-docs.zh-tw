---
title: 了解 IoT 的 Azure 資訊安全中心的成本預覽 |Microsoft Docs
description: 深入了解 IoT 和控制它們與 Azure 資訊安全中心相關聯的成本。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 00666e27c9251248aa6ecff75d88908baabf71f3
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919720"
---
# <a name="pricing-and-associated-costs"></a>定價和相關成本

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章說明 Azure 資訊安全中心 (ASC) IoT 定價模型、 摘要說明所有相關聯的成本，並說明如何管理它們。

## <a name="pricing"></a>價格

ASC iot 定價模型會包含兩個組件和 IoT 中樞後計費[啟用](quickstart-onboard-iot-hub.md)iot ASC 中：

- 依裝置-分析 IoT 中樞的記錄檔為基礎的內建安全性功能的成本。

- 依訊息-從 IoT Edge 或分葉裝置的安全性訊息為基礎的增強式的安全性功能的成本。

  >[!Note]
  > 安全性訊息也會產生在 IoT 中樞上的配額耗用量。

如需詳細資訊，請參閱 <<c0> [ 資訊安全中心價格](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="associated-costs"></a>相關聯的成本

Iot 的 ASC 有兩種類型的相關聯的成本，並不屬於直接定價：

- IoT 中樞配額耗用量

- 記錄分析儲存體成本

您可以退出特定功能，將設定變更，以降低相關的成本。

若要變更您的設定：

1. 開啟 IoT 中樞。

2. 底下**安全性**，按一下**概觀**。

3. 按一下 [設定] 。

下表提供相關聯成本的摘要，以及每個選項的影響。

|     | 使用量 | 註解 |
| --- | --- | --- |
| **IoT 中樞配額耗用量** |  |
| [匯出裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices)作業 （對應項匯出） | 一天 1 次 | 停用_對應項中繼資料集合_ |
| **記錄分析儲存體** |  |
| 裝置的建議和警示| 安全性建議及服務所產生的警示 | 不是選擇性 |
| 未經處理的安全性資料| 從 IoT 裝置，安全性代理程式所收集的未經處理的安全性資料 | 停用_儲存未經處理的裝置安全性事件_ |

>[!Important]
> 退出有可用的安全性功能的嚴重影響。
  
| 退出 | 影響 |
| --- | --- |
| _對應項中繼資料集合_ | 停用[自訂警示](quickstart-create-custom-alerts.md) |
| | 停用 IoT Edge 資訊清單的建議 |
| | 停用裝置身分識別為基礎的建議和警示 |
| _存放區未經處理的裝置安全性事件_ | 沒有裝置的 OS 基準建議的詳細資料 |
| | 在詳細資料[警示](concept-security-alerts.md)並[建議](concept-recommendations.md)調查未提供 |


## <a name="see-also"></a>請參閱

- 存取您[未經處理的安全性資料](how-to-security-data-access.md)
- [調查裝置](how-to-investigate-device.md)
- 了解和探索[安全性建議](concept-recommendations.md)
- 了解和探索[安全性警示](concept-security-alerts.md)
