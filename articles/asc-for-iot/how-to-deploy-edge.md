---
title: 部署 IoT Edge 模組 ASC |Microsoft Docs
description: 了解如何將 ASC 部署 IoT Edge 上的 IoT 安全性代理程式。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580489"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>部署您的 IoT Edge 裝置上的安全性模組

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Iot 的 ASC **azureiotsecurity**模組提供您的 IoT Edge 裝置的全方位的安全性解決方案。
安全性模組會收集、 彙總並分析未經處理的安全性資料，從您的作業系統與容器系統，到可操作的安全性建議和警示。
若要進一步了解，請參閱[IoT Edge 的安全性模組](security-edge-architecture.md)。

本指南中，您將了解如何部署您的 IoT Edge 裝置上的安全性模組。

## <a name="deploy-security-module"></a>部署安全性模組

使用下列步驟來部署 IoT Edge 適用的 IoT 安全性模組 ASC。

### <a name="prerequisites"></a>必要條件

1. 請確定您的裝置[註冊為 IoT Edge 裝置](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal)。

1. IoT Edge 模組 ASC 需要[AuditD framework](https://linux.die.net/man/8/auditd) Edge 裝置上安裝。

   在您的 Edge 裝置上執行下列命令來安裝 framework:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>使用 Azure 入口網站的部署

1. 開啟**Marketplace**在 Azure 入口網站中。

1. 搜尋**建立 azure iot 安全性**，然後按一下**Azure IoT 安全性**。

   ![](media/howto/edge_onboarding_7.png)

1. 按一下頁面底部的 [新增] 。

1. 選擇您**訂用帳戶**， **IoT 中樞**並**IoT Edge 裝置名稱**，然後按一下**建立**。

1. 按一下 **下一步**兩次到**檢閱部署**。

1. 請確定**edgeHub.settings.createOptions**設定，如下所示：

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. 按一下 **送出**來完成部署。


## <a name="next-steps"></a>後續步驟

若要深入了解組態選項，繼續模組設定的使用說明指南。 
> [!div class="nextstepaction"]
> [模組設定使用說明指南](./how-to-agent-configuration.md)