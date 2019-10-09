---
title: 部署 IoT Edge 模組的 Azure 資訊安全中心 |Microsoft Docs
description: 瞭解如何在 IoT Edge 上部署 IoT 安全性代理程式的 Azure 資訊安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2019
ms.author: mlottner
ms.openlocfilehash: 128265cd3e69cd27bab6538c9eb376410439824d
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176668"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在您的 IoT Edge 裝置上部署安全性模組


適用于 IoT 模組的**Azure 資訊安全中心**可為您的 IoT Edge 裝置提供完整的安全性解決方案。
安全性模組會收集、匯總和分析原始的安全性資料，從您的作業系統和容器系統，到可操作的安全性建議和警示。
若要深入瞭解，請參閱[IoT Edge 的安全性模組](security-edge-architecture.md)。

在本文中，您將瞭解如何在 IoT Edge 裝置上部署安全性模組。

## <a name="deploy-security-module"></a>部署安全性模組

使用下列步驟來部署適用于 IoT Edge 的 IoT 安全性模組 Azure 資訊安全中心。

### <a name="prerequisites"></a>必要條件

1. 在您的 IoT 中樞中，請確定您的裝置已[註冊為 IoT Edge 裝置](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

1. IoT Edge 模組的 Azure 資訊安全中心需要在 IoT Edge 裝置上安裝[AuditD framework](https://linux.die.net/man/8/auditd) 。

    - 在您的 IoT Edge 裝置上執行下列命令來安裝架構：
   
    `sudo apt-get install auditd audispd-plugins`

    - 執行下列命令，確認 AuditD 為作用中： 
   
    `sudo systemctl status auditd`<br>
    - 預期的回應為： `active (running)` 
        

### <a name="deployment-using-azure-portal"></a>使用 Azure 入口網站進行部署

1. 從 [Azure 入口網站] 中，開啟 [ **Marketplace**]。

1. 選取 [**物聯網**]，然後搜尋**IoT 的 Azure 資訊安全中心**並加以選取。

   ![選取 IoT 的 Azure 資訊安全中心](media/howto/edge-onboarding-8.png)

1. 按一下 [**建立**] 以設定部署。 

1. 選擇您 IoT 中樞的 Azure**訂**用帳戶，然後選取您的**IoT 中樞**。<br>選取 [**部署至裝置**以單一裝置為目標]，或選取 [**大規模部署**] 以將多個裝置設為目標，然後按一下 [**建立**]。 如需大規模部署的詳細資訊，請參閱[如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。 

    >[!Note] 
    >如果您選取 [**大規模部署**]，請先新增裝置名稱和詳細資料，再繼續進行下列指示中的 [**新增模組**] 索引標籤。     

有三個步驟可為適用于 IoT 的 Azure 資訊安全中心建立 IoT Edge 部署。 下列各節將逐步解說每一個步驟。 

#### <a name="step-1-add-modules"></a>步驟 1:新增模組

1. 從 [**新增模組**] 索引標籤的 [**部署模組**] 區域中，按一下 [ **AzureSecurityCenterforIoT**] 的 [**設定**] 選項。 
   
1. 將**名稱**變更為**azureiotsecurity**。
1. 將**映射 URI**變更為**mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0**。
1. 確認 [**容器建立選項**] 值設定為：      
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
1. 確認已選取 [**設定模組對應項的所需屬性**]，並將 configuration 物件變更為：
      
    ``` json
    { 
       "properties.desired":{ 
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{ 

          }
       }
    }
    ```

1. 按一下 [儲存]。
1. 前往索引標籤的底部，然後選取 [**設定 Advanced Edge Runtime 設定**]。 
   
1. 將 [ **Edge 中樞**] 底下的**影像**變更為 [ **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2**]。

1. 確認 [**建立選項**] 設定為： 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
1. 按一下 [儲存]。
   
1. 按一下 [下一步]。

#### <a name="step-2-specify-routes"></a>步驟 2:指定路由 

1. 在 [**指定路由**] 索引標籤中，確定您有路由（明確或隱含），它會根據下列範例，將訊息從**azureiotsecurity**模組轉送至 **$Upstream** ，然後按 **[下一步]** 。 

~~~Default implicit route
"route": "FROM /messages/* INTO $upstream" 
~~~

~~~Explicit route
"ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
~~~

#### <a name="step-3-review-deployment"></a>步驟 3：檢閱部署

- 在 [**審查部署**] 索引標籤中，檢查您的部署資訊，然後選取 [**提交**] 以完成部署。

## <a name="diagnostic-steps"></a>診斷步驟

如果您遇到問題，容器記錄是瞭解 IoT Edge 安全性模組裝置狀態的最佳方式。 可使用這一節中的命令與工具來收集資訊。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>確認所需的容器已安裝並如預期般運作

1. 在您的 IoT Edge 裝置上執行下列命令：
    
    `sudo docker ps`
   
1. 確認下列容器正在執行：
   
   | Name | 包 |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   如果沒有所需的最小容器，請檢查您的 IoT Edge 部署資訊清單是否與建議的設定一致。 如需詳細資訊，請參閱[部署 IoT Edge 模組](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>檢查模組記錄檔中的錯誤
   
1. 在您的 IoT Edge 裝置上執行下列命令：

   `sudo docker logs azureiotsecurity`
   
1. 如需更詳細的記錄，請將下列環境變數新增至**azureiotsecurity**模組部署： `logLevel=Debug`。

## <a name="next-steps"></a>後續步驟

若要深入瞭解設定選項，請繼續進行模組設定的操作指南。 
> [!div class="nextstepaction"]
> [模組設定操作指南](./how-to-agent-configuration.md)
