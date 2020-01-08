---
title: Azure Service Fabric-設定容器存放庫認證
description: 設定存放庫認證以從 container registry 下載映射
author: arya
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: arya
ms.openlocfilehash: 25fe3c69b19d397137d1e1802e941e0433a1b160
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351664"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>設定應用程式的儲存機制認證以下載容器映射

將 `RepositoryCredentials` 新增至 ApplicationManifest.xml 檔案的 `ContainerHostPolicies` 中，以設定容器登錄驗證。 為 myregistry.azurecr.io 容器登錄新增帳戶和密碼，讓服務從存放庫中下載容器映像。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

建議您使用部署到叢集所有節點的加密憑證，以加密存放庫密碼。 當 Service Fabric 將服務套件部署至叢集時，會使用加密憑證將加密文字解密。 Invoke-ServiceFabricEncryptText Cmdlet 會用來建立密碼的加密文字，其已新增至 ApplicationManifest.xml 檔案。
如需憑證和加密語義的詳細資訊，請參閱[秘密管理](service-fabric-application-secret-management.md)。

## <a name="configure-cluster-wide-credentials"></a>設定整個叢集的認證

Service Fabric 可讓您設定整個叢集的認證，以供應用程式用來作為預設存放庫認證。

若要啟用或停用這項功能，您可以在 ApplicationManifest 中，將 `UseDefaultRepositoryCredentials` 屬性新增至具有 `true` 或 `false` 值的 `ContainerHostPolicies`。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric 接著會使用預設存放庫認證，您可以在 [`Hosting`] 區段下的 [ClusterManifest] 中指定。  如果 `UseDefaultRepositoryCredentials` 是 `true`，Service Fabric 會從 ClusterManifest 中讀取下列值：

* DefaultContainerRepositoryAccountName (字串)
* DefaultContainerRepositoryPassword (字串)
* IsDefaultContainerRepositoryPasswordEncrypted (布林值)
* DefaultContainerRepositoryPasswordType (字串) --- 從 6.4 執行階段後開始支援

以下是可在 ClusterManifestTemplate 的 `Hosting` 區段內新增的範例。 您可以在建立叢集時或稍後於設定升級中新增 [`Hosting`] 區段。 如需詳細資訊，請參閱[變更 Azure Service Fabric 叢集設定](service-fabric-cluster-fabric-settings.md)及[管理 Azure Service Fabric 應用程式祕密](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>使用受控識別服務（MSI）來運用虛擬機器擴展集的受控識別

Service Fabric 支援使用權杖做為認證，以下載容器的映射。  此功能會利用基礎虛擬機器擴展集的受控識別來向登錄進行驗證，而不必管理使用者認證。  如需 MSI 的詳細資訊，請參閱[受控服務識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。  使用此功能需要進行下列步驟：

1.  確定 VM 已啟用系統指派的受控識別（請參閱下面的螢幕擷取畫面）

    ![建立虛擬機器擴展集識別](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  之後，將許可權授與 VM （SS），以從登錄提取/讀取映射。  透過 Azure 分頁移至您的 ACR 存取控制（IAM），並為您的 VM （SS）提供正確的許可權，如下所示：

    ![將 VM 主體新增至 ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  完成上述步驟之後，請修改您的 applicationmanifest 檔案。  尋找標示為 "ContainerHostPolicies" 的標記，並將屬性 `‘UseTokenAuthenticationCredentials=”true”`。

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > 旗標 `UseDefaultRepositoryCredentials` 設定為 true，而 `UseTokenAuthenticationCredentials` 為 true 時，會在部署期間造成錯誤。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Container registry 驗證](/azure/container-registry/container-registry-authentication)。
