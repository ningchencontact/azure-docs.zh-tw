---
title: IoT 預覽的 Azure 資訊安全中心的驗證方法 |Microsoft Docs
description: 了解可用的不同驗證方法時使用 Azure 資訊安全中心的 IoT 服務。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d1f9d77d1f87e8d201e2cb034401bb3cae14f41b
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862908"
---
# <a name="security-agent-authentication-methods"></a>安全性代理程式驗證方法 

> [!IMPORTANT]
> IoT 的 azure 資訊安全中心目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章說明您可以使用與 AzureIoTSecurity 代理程式向 IoT 中樞的不同驗證方法。

針對每個裝置上架到 Azure 資訊安全中心 (ASC) 在 IoT 中樞的 iot，安全性模組則是必要項目。 若要驗證的裝置，iot ASC，也可以使用兩種方法之一。 選擇最適合用於您現有的 IoT 解決方案的方法。 

> [!div class="checklist"]
> * 安全性 [模組] 選項
> * 裝置選項

## <a name="authentication-methods"></a>驗證方法

AzureIoTSecurity 代理程式來執行驗證之兩種方法：

 - **模組**驗證模式<br>
   模組是獨立的裝置對應項進行驗證。
   如果您想要使用專用的驗證方法，透過安全性模組 （只有對稱金鑰） 的安全性代理程式，請使用這個驗證類型。
        
 - **裝置**驗證模式<br>
    在此方法中，安全性代理程式第一次驗證的裝置身分識別。 初始驗證之後，執行 IoT 代理程式 ASC **REST**到 IoT 中樞與裝置的驗證資料使用 REST API 呼叫。 IoT 代理程式 ASC 接著要求的安全性模組驗證方法和資料，從 IoT 中樞。 在最後一個步驟中，ASC IoT 代理程式會執行 IoT 模組驗證 ASC。
    
    如果您想要重複使用現有的裝置驗證方法 （已自我簽署憑證或對稱金鑰） 的安全性代理程式，請使用這個驗證類型。 

請參閱[安全性代理程式的安裝參數](#security-agent-installation-parameters)以了解如何設定。
                                
## <a name="authentication-methods-known-limitations"></a>已知限制的驗證方法

- **模組**驗證模式僅支援對稱金鑰驗證。
- CA 簽署的憑證不受**裝置**驗證模式。  

## <a name="security-agent-installation-parameters"></a>安全性代理程式的安裝參數

當[部署安全性代理程式](how-to-deploy-agent.md)，必須提供驗證詳細資料，做為引數。
下表會記錄這些引數。


|參數|描述|選項|
|---------|---------------|---------------|
|**身分識別**|驗證模式| **模組**或**裝置**|
|**type**|驗證類型|**SymmetricKey**或**SelfSignedCertificate**|
|**filePath**|包含憑證或對稱金鑰之檔案的絕對完整路徑| |
|**gatewayHostname**|IoT 中樞的 FQDN|範例：ContosoIotHub.azure-devices.net|
|**deviceId**|裝置識別碼|範例：MyDevice1|
|**certificateLocationKind**|憑證存放區位置|**LocalFile**或**存放區**|


使用時安裝安全性代理程式指令碼，下列組態會自動執行。

若要手動編輯安全性代理程式驗證，編輯組態檔。 

## <a name="change-authentication-method-after-deployment"></a>在部署後變更驗證方法

當部署安全性代理程式安裝指令碼，會自動建立的組態檔。

若要變更驗證方法，在部署之後，手動編輯組態檔是必要的。


### <a name="c-based-security-agent"></a>C#-基礎安全性代理程式

編輯_Authentication.config_具備下列參數：

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C 為基礎的安全性代理程式

編輯_LocalConfiguration.json_具備下列參數：

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>請參閱
- [安全性代理程式概觀](security-agent-architecture.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
- [存取未經處理的安全性資料](how-to-security-data-access.md)
