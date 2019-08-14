---
title: Azure Service Fabric-使用系統指派的受控識別來部署 Azure Service Fabric 應用程式 |Microsoft Docs
description: 本文說明如何將系統指派的受控識別指派給 Azure Service Fabric 應用程式
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: f9d7f87e9d7b1761c165e08e37cd6a3237c8212a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965518"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>使用系統指派的受控識別來部署 Service Fabric 應用程式

為了存取 Azure Service Fabric 應用程式的受控識別功能, 您必須先在叢集上啟用受控識別權杖服務。 此服務負責使用其受控識別來驗證 Service Fabric 應用程式, 並代表其取得存取權杖。 啟用服務之後, 您可以 Service Fabric Explorer 在左窗格的 [**系統**] 區段底下的 [ManagedIdentityTokenService] 中看到它, 並在 [其他系統服務] 旁的 [ **Fabric:/system/** ] 底下執行。

> [!NOTE] 
> 從 API 版本`"2019-06-01-preview"`開始, 支援部署具有受控識別的 Service Fabric 應用程式。 您也可以針對應用程式類型、應用程式類型版本和服務資源使用相同的 API 版本。 支援的最小 Service Fabric 執行時間為 6.5 CU2。

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別

### <a name="application-template"></a>應用程式範本

若要使用系統指派的受控識別來啟用應用程式, 請將**identity**屬性新增至應用程式資源, 其類型為**systemAssigned** , 如下列範例所示:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
這個屬性會分別宣告 (以 Azure Resource Manager, 以及受控識別和 Service Fabric 資源提供者, 此資源應具有隱含 (`system assigned`) 受控識別。

### <a name="application-and-service-package"></a>應用程式和服務套件

1. 更新應用程式資訊清單, 以在**主體**區段中新增**microsoft.managedidentity**元素, 其中包含單一專案, 如下所示:

    **ApplicationManifest .xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    這會將指派給應用程式的身分識別對應至易記名稱, 以進一步指派給組成應用程式的服務。 

2. 在對應至要指派受控識別之服務的**ServiceManifestImport**區段中, 新增**IdentityBindingPolicy**元素, 如下所示:

    **ApplicationManifest .xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    這個元素會將應用程式的身分識別指派給服務;若沒有此指派, 服務將無法存取應用程式的身分識別。 在上述程式碼片段中, `SystemAssigned`身分識別 (也就是保留關鍵字) 會對應至易記名稱`WebAdmin`下的服務定義。

3. 更新服務資訊清單, 以在**Resources**區段內新增**microsoft.managedidentity**元素, 其名稱符合應用程式資訊清單`ServiceIdentityRef`中`IdentityBindingPolicy`定義的設定值:

    **ServiceManifest .xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    這是與上述服務的身分識別對應, 但從服務定義的觀點來看。 這裡會依應用程式資訊清單中宣告的`WebAdmin`易記名稱 () 來參考此身分識別。

## <a name="related-articles"></a>相關文章

* 審查 Azure Service Fabric 中的[受控識別支援](./concepts-managed-identity.md)

* [部署新的](./configure-new-azure-service-fabric-enable-managed-identity.md)具有受控識別支援的 Azure Service Fabric 叢集 

* 在現有的 Azure Service Fabric 叢集中[啟用受控識別](./configure-existing-cluster-enable-managed-identity-token-service.md)

* 從原始程式碼運用 Service Fabric 應用程式的[受控識別](./how-to-managed-identity-service-fabric-app-code.md)

* [使用使用者指派的受控識別來部署 Azure Service Fabric 應用程式](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

* [將其他 Azure 資源的存取權授與 Azure Service Fabric 應用程式](./how-to-grant-access-other-resources.md)
