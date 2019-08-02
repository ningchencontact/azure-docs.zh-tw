---
title: 適用于 IoT 必要條件的 Azure 資訊安全中心 |Microsoft Docs
description: 開始使用 IoT 服務必要條件 Azure 資訊安全中心所需的所有專案的詳細資料。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596824"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>適用于 IoT 必要條件的 Azure 資訊安全中心

本文說明適用于 IoT 服務的 Azure 資訊安全中心 (ASC) 的不同建立組塊、您需要開始的事項, 並說明可協助您瞭解服務的基本概念。 

## <a name="minimum-requirements"></a>最低需求

- IoT 中樞標準層
    - RBAC 角色**擁有**者層級許可權 
- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure 資訊安全中心 (建議)
    - Azure 資訊安全中心的使用是建議事項, 而不是必要條件。 若沒有 Azure 資訊安全中心, 您將無法在 IoT 中樞內查看其他 Azure 資源。 
 
## <a name="working-with-asc-for-iot-service"></a>使用適用于 IoT 服務的 ASC

您可以使用 Azure IoT 中樞和 Azure 資訊安全中心取得適用于 IoT 深入解析和報告的 ASC。 若要在您的 Azure IoT 中樞上啟用適用于 IoT 的 ASC, 則需要具有**擁有**者層級許可權的帳戶。 在您的 IoT 中樞中啟用適用于 IoT 的 ASC 之後, 適用于 IoT insights 的 ASC 會顯示為 Azure IoT 中樞中的**安全性**功能, 以及 Azure 資訊安全中心中的**iot** 。 

## <a name="supported-service-regions"></a>支援的服務區域 

下列 Azure 區域中的 IoT 中樞目前支援適用于 IoT 的 ASC:
  - 美國中部  
  - East US 
  - 美國東部 2
  - 美國中西部
  - 美國西部
  - 美國西部 2
  - 美國中部南部
  - 美國中北部
  - 加拿大中部
  - 加拿大東部 
  - 北歐    
  - 巴西南部
  - 法國中部  
  - 英國西部 
  - 英國南部
  - 西歐 
  - 北歐 
  - 日本西部  
  - 日本東部  
  - 澳大利亞東南部
  - 澳大利亞東部
  - 東亞   
  - 東南亞
  - 南韓中部
  - 南韓南部 
  - 印度中部
  - 印度南部
  
## <a name="wheres-my-iot-hub"></a>我的 IoT 中樞在哪裡？

開始之前, 請先檢查您的 IoT 中樞位置以確認服務可用性。 

1. 開啟 IoT 中樞。 
2. 按一下 [概觀]。 
3. 確認列出的位置符合其中一個[支援的服務區域](#supported-service-regions)。 


## <a name="supported-platforms-for-agents"></a>代理程式支援的平臺 

適用于 IoT 代理程式的 ASC 支援不斷增長的裝置和平臺清單。 請參閱[支援的平臺清單](how-to-deploy-agent.md), 以檢查您現有或已規劃的裝置程式庫。  

## <a name="next-steps"></a>後續步驟
- 閱讀 Azure IoT 安全性[總覽](overview.md)
- 瞭解如何[啟用服務](quickstart-onboard-iot-hub.md)
- 閱讀[適用于 IoT 的 AZURE 資訊安全中心常見問題](resources-frequently-asked-questions.md)
- 探索如何[瞭解 IoT 警示的 Azure 資訊安全中心](concept-security-alerts.md)
