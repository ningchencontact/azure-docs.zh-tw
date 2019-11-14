---
title: Service Fabric 秘密存放區 |Microsoft Docs
description: 本文說明如何使用 Service Fabric 秘密存放區。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 5315a8806f45e40204e8500e97c3440bfa9ab8b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077345"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric 秘密存放區
本文說明如何使用 Service Fabric 秘密存放區（CSS），在 Service Fabric 應用程式中建立及使用秘密。 CSS 是一個本機秘密存放區快取，用來防止機密資料（例如密碼、權杖和金鑰）在記憶體中加密。

## <a name="enabling-secrets-store"></a>啟用秘密存放區
 將下列新增至您的叢集設定底下的 `fabricSettings` 以啟用 CSS。 建議使用與適用于 CSS 的叢集憑證不同的憑證。 請確定已在所有節點上安裝加密憑證，且 `NetworkService` 擁有憑證私密金鑰的讀取權限。
  ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
```
## <a name="declare-secret-resource"></a>宣告秘密資源
您可以使用 Resource Manager 範本或使用 REST API 來建立秘密資源。

* 使用 Resource Manager 範本
```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```
上述範本會建立 `supersecret` 秘密資源，但尚未為密碼資源設定任何值。

* 使用 REST API

若要建立秘密資源，`supersecret` 提出 PUT 要求來 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`。 您需要叢集憑證或管理用戶端憑證，才能建立秘密。

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>設定秘密值
* 使用 Resource Manager 範本

下列 Resource Manager 範本會針對具有版本 `ver1`的密碼 `supersecret` 建立和設定值。
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
* 使用 REST API

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>在您的應用程式中使用密碼

1.  在 config.xml 檔案中新增包含下列內容的區段。 請注意，此值的格式為 {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. 現在匯入 ApplicationManifest 中的區段
```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
        </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```

環境變數 ' SecretPath ' 將指向所有秘密儲存所在的目錄。 [`testsecrets`] 區段底下所列的每個參數將會儲存在不同的檔案中。 應用程式現在可以使用密碼，如下所示
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. 將秘密裝載至容器

只有在容器內提供秘密所需的變更，就是在 `<ConfigPackage>`中指定一個掛接點。
以下是修改過的 ApplicationManifest .xml。  

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ConfigPackagePolicies CodePackageRef="Code">
        <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
        <!-- Linux Container
         <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
        -->
      </ConfigPackagePolicies>
    </Policies>
  </ServiceManifestImport>
```
您容器內的掛接點下會提供秘密。

4. 將秘密系結至環境變數 

您可以藉由指定 Type = ' SecretsStoreRef '，將秘密系結至進程環境變數。 以下範例說明如何將 `supersecret` 版本 `ver1` 系結至 ServiceManifest 中的環境變數 `MySuperSecret`。

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>後續步驟
深入了解[應用程式及服務安全性](service-fabric-application-and-service-security.md)