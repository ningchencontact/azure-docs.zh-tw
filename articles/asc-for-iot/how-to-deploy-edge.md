---
title: 部署 Azure 資訊安全中心的 IoT Edge 模組 |Microsoft Docs
description: 深入了解如何部署 Azure 資訊安全中心的 IoT Edge 上的 IoT 安全性代理程式。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 85e342f08e5402e50e5b0dfd1fe2df90337f29ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254300"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>部署您的 IoT Edge 裝置上的安全性模組

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

**Iot 的 azure 資訊安全中心 (ASC)** 模組提供您的 IoT Edge 裝置的全方位的安全性解決方案。
安全性模組會收集、 彙總，並會從您的作業系統與容器系統的未經處理的安全性資料分析成可操作的安全性建議和警示。
若要進一步了解，請參閱[IoT Edge 的安全性模組](security-edge-architecture.md)。

本指南中，您將了解如何部署您的 IoT Edge 裝置上的安全性模組。

## <a name="deploy-security-module"></a>部署安全性模組

使用下列步驟來部署 IoT Edge 適用的 IoT 安全性模組 ASC。

### <a name="prerequisites"></a>必要條件

- 在您的 IoT 中樞，請確定您的裝置[註冊為 IoT Edge 裝置](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

- ASC IoT Edge 模組需要[AuditD framework](https://linux.die.net/man/8/auditd) IoT Edge 裝置上已安裝。

    - IoT Edge 裝置上執行下列命令來安裝 framework:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - 確認 AuditD 為作用中執行下列命令：
   
      `sudo systemctl status auditd`
      
        預期的回應是`active (running)`。 

### <a name="deployment-using-azure-portal"></a>使用 Azure 入口網站的部署

1. 從 Azure 入口網站中，開啟**Marketplace**。

1. 選取 **物聯網**，然後搜尋**IoT 的 Azure 資訊安全中心**並加以選取。

   ![選取 Azure 資訊安全中心的 IoT](media/howto/edge-onboarding-8.png)

1. 按一下 **建立**來設定的部署。 

1. 選擇 Azure**訂用帳戶**的 IoT 中樞，然後選取您**IoT 中樞**。<br>選取 **部署到裝置**設為目標的單一裝置或選取**大規模部署**目標多個裝置，然後按一下**建立**。 如需大規模部署的詳細資訊，請參閱[如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。 

    >[!Note] 
    >如果您選取**大規模部署**，新增的裝置名稱和詳細資料，然後再繼續**新增模組** 索引標籤中的下列指示。     

有三個步驟來建立 IoT Edge 部署 Azure 資訊安全中心的 iot。 下列各節將逐步解說每一個步驟。 

#### <a name="step-1-add-modules"></a>步驟 1：新增模組

1. 從**新增模組**索引標籤上，**部署模組**區域中，按一下  **AzureSecurityCenterforIoT**。 
   
1. 變更**名稱**要**azureiotsecurity**。
1. 變更**映像 URI**要**mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**。
1. 請確認**容器建立選項**值設定為：      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. 確認**組模組對應項的所需屬性**已選取，然後變更的組態物件：
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. 按一下 [檔案]  。
1. 捲動至底部的索引標籤，然後選取**進行進階 Edge 執行階段設定**。
   
   >[!Note]
   > 請勿**不**停用 IoT Edge 中樞的 AMQP 通訊。
   > Azure 資訊安全中心的 IoT 模組需要使用 IoT Edge 中樞的 AMQP 通訊。
   
1. 變更**映像**下方**Edge 中樞**來**mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**。

   >[!Note]
   > IoT 模組的 azure 資訊安全中心需要分支的版本的 SDK 版本 1.20 為基礎的 IoT Edge 中樞。
   > 藉由變更 IoT Edge 中樞映像，您會指示您的 IoT Edge 裝置的 IoT Edge 中樞，IoT Edge 服務未正式支援的分支版本中取代最新穩定版本。

1. 請確認**建立選項**設為： 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. 按一下 [檔案]  。
   
1. 单击“下一步”  。

#### <a name="step-2-specify-routes"></a>步驟 2：指定路由 

1. 在**指定路由**索引標籤上，設定**ASCForIoTToIoTHub**路由傳送至 **"/ 訊息/模組/azureiotsecurity/從\*到 $upstream 」** ，然後按一下**下一步**。

   ![指定路由](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>步驟 3：檢閱部署

1. 在 **檢閱部署**索引標籤上，檢閱您的部署資訊，然後選取**送出**來完成部署。

## <a name="diagnostic-steps"></a>診斷步驟

如果您遇到問題，容器記錄檔是最佳方式，若要了解 IoT Edge 安全性模組裝置的狀態。 可使用這一節中的命令與工具來收集資訊。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>確認必要的容器已安裝且如預期般正常

1. IoT Edge 裝置上執行下列命令：
    
     `sudo docker ps`
   
1. 確認下列容器正在執行：
   
   | 名稱 | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   如果容器不存在所需的最小，請檢查您的 IoT Edge 部署資訊清單的建議的設定會配合。 如需詳細資訊，請參閱 <<c0> [ 部署 IoT Edge 模組](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>檢查有錯誤的模組記錄檔
   
1. IoT Edge 裝置上執行下列命令：

   `sudo docker logs azureiotsecurity`
   
1. 為更詳細的記錄檔，新增下列環境變數**azureiotsecurity**模組的部署： `logLevel=Debug`。

## <a name="next-steps"></a>後續步驟

若要深入了解組態選項，繼續模組設定的使用說明指南。 
> [!div class="nextstepaction"]
> [模組設定的使用說明指南](./how-to-agent-configuration.md)
