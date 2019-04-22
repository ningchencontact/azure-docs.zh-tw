---
title: 如需 IoT 必要條件預覽的 azure 資訊安全中心 |Microsoft Docs
description: 若要開始使用 Azure 資訊安全中心的 IoT 服務必要條件所需的一切細節。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: f09d768c0c6c1d351f737b053da9fd3282867099
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861684"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>如需 IoT 必要條件的 azure 資訊安全中心

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文提供不同的建置組塊的 Azure 資訊安全中心 (ASC) IoT 服務，您需要開始和基本概念來協助您了解服務的說明。 

## <a name="minimum-requirements"></a>最低需求

- IoT 中樞標準層
    - RBAC 角色**擁有者**層級權限 
- [Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure 資訊安全中心 （建議選項）
    - Azure 資訊安全中心使用時僅供建議並不是需求，否則，您將無法檢視 IoT 中樞內其他 Azure 資源。 
 
## <a name="working-with-asc-for-iot-service"></a>使用 ASC IoT 服務

提供使用 Azure IoT 中樞和 Azure 資訊安全中心的 IoT 深入解析和報告的 ASC。 若要啟用您的 Azure IoT 中樞，具有的帳戶上的 IoT ASC**擁有者**須有層級權限。 啟用之後 ASC 在 IoT 中樞的 iot，IoT 深入解析的 ASC 會顯示為**安全性**功能，在 Azure IoT 中樞並為**IoT** Azure 資訊安全中心。 

## <a name="supported-service-regions"></a>支援的服務區域 

Iot 的 ASC 目前支援下列的 Azure 區域中的 IoT 中樞：
  - 美國中部
  - 北歐
  - 東南亞

## <a name="wheres-my-iot-hub"></a>其中是我的 IoT 中樞？

請檢查您的 IoT 中樞位置，在您開始前，請確認服務可用性。 

1. 開啟 IoT 中樞。 
2. 按一下 [概觀] 。 
3. 確認所列的位置符合其中一個[支援的服務區域](#supported-service-regions)。 


## <a name="supported-platforms-for-agents"></a>支援的平台代理程式 

ASC IoT 代理程式支援持續增加的裝置與平台清單。 請參閱[支援的平台清單](how-to-deploy-agent.md)來檢查您的現有或計劃裝置程式庫。  

## <a name="next-steps"></a>後續步驟
- [概觀](overview.md)
- [啟用服務](quickstart-onboard-iot-hub.md)
- [ASC iot 常見問題集](resources-frequently-asked-questions.md)
- [了解 IoT 警示 ASC](concept-security-alerts.md)
