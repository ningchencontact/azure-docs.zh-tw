---
title: 在 Azure 中部署 OpenShift 容器平台 | Microsoft Docs
description: 在 Azure 中部署 OpenShift 容器平台。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 296bc42313ef80425004d3c9b43c6792cbaf97f4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718249"
---
# <a name="deploy-openshift-container-platform-in-azure"></a>在 Azure 中部署 OpenShift 容器平台

您可以使用數種方法其中之一，在 Azure 中部署 OpenShift 容器平台：

- 您可以手動部署必要的 Azure 基礎結構元件，然後依照 [OpenShift 容器平台文件](https://docs.openshift.com/container-platform)進行。
- 您也可以使用能夠簡化 OpenShift 容器平台叢集部署的現有 [Resource Manager 範本](https://github.com/Microsoft/openshift-container-platform/) \(英文\)。
- 另一個選項是使用 [Azure Marketplace 供應項目](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview) \(英文\)。

對於所有選項，都需要有 Red Hat 訂用帳戶。 在部署期間，Red Hat Enterprise Linux 執行個體會向 Red Hat 訂用帳戶註冊，並且附加至包含 OpenShift 容器平台之權利的集區識別碼。
請確定您具備有效的 Red Hat Subscription Manager (RHSM) 使用者名稱、密碼和集區識別碼。 您可以使用啟用金鑰、組織識別碼和集區識別碼。 您可以登入 https://access.redhat.com 來驗證這項資訊。


## <a name="deploy-using-the-openshift-container-platform-resource-manager-template"></a>使用 OpenShift 容器平台 Resource Manager 範本部署

### <a name="private-clusters"></a>私用叢集

部署私用的 OpenShift 叢集，則需要多個只是不具有公用 IP 相關聯之主要的負載平衡器 （web 主控台） 上，或基礎結構負載平衡器 （路由器）。  自訂的 DNS 伺服器 （不是預設的 Azure DNS）、 自訂網域名稱 （例如 contoso.com) 和預先定義的虛擬網路，通常會使用私用的叢集。  對於私人叢集，您需要事先設定所有適當的子網路和 DNS 伺服器設定的虛擬網路。  然後使用**existingMasterSubnetReference**， **existingInfraSubnetReference**， **existingCnsSubnetReference**，和**existingNodeSubnetReference**來指定用於現有的子網路叢集。

如果已選取 私用的主要 (**masterClusterType**= 私用)，必須指定的靜態私人 IP **masterPrivateClusterIp**。  此 IP 會指派給主要的負載平衡器前端中。  IP 必須在主要的子網路，且不在使用 CIDR。  **masterClusterDnsType**必須設為 「 自訂 」，也必須提供 DNS 名稱是主機**masterClusterDns**。  DNS 名稱必須對應至靜態私人 IP，並將用於存取主要節點上的主控台。

如果已選取 私用路由器 (**routerClusterType**= 私用)，必須指定的靜態私人 IP **routerPrivateClusterIp**。  此 IP 會指派給前端的基礎結構負載平衡器。  IP 必須為 CIDR 內基礎結構子網路且並非使用中。  **routingSubDomainType**的路由，必須提供給必須設為"custom"和萬用字元 DNS 名稱**routingSubDomain**。  

如果已選取私人主機和私人的路由器，自訂網域名稱也必須輸入為**domainName**

成功部署之後，防禦節點是具有公用 IP，您可以 ssh 到唯一的節點。  即使主要節點會設定為公用存取，他們不會被公開的 ssh 存取。

若要使用 Resource Manager 範本進行部署，請使用參數檔案來提供輸入參數。 若要進一步自訂部署，請分支處理 GitHub 存放庫並變更適當的項目。

某些常見的自訂選項包括但不限於：

- 防禦 VM 大小 (azuredeploy.json 中的變數)
- 命名慣例 (azuredeploy.json 中的變數)
- OpenShift 叢集詳細規格，已透過主機檔案 (deployOpenShift.sh) 修改

### <a name="configure-the-parameters-file"></a>設定參數檔案

[OpenShift 容器平台範本](https://github.com/Microsoft/openshift-container-platform)有多個分支可用於不同版本的 OpenShift 容器平台。  根據您的需求，您可以直接從存放庫部署或可以分支處理存放庫，並且在部署前，先對範本或指令碼進行自訂變更。

使用您稍早針對 `aadClientId` 參數所建立之服務主體中的 `appId` 值。

下列範例顯示名為 azuredeploy.parameters.json，且具有所有必要輸入的參數檔案。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

以您的特定資訊取代參數。

不同版本可能有不同的參數，因此請確認您所用分支的必要參數。

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy。Parameters.json 檔案說明

| 屬性 | 描述 | 有效的選項 | 預設值 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 成品 （json、 指令碼等等） 的 URL |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 要部署資源的 azure 區域 |  |  |
| `masterVmSize` | 在主要 VM 的大小。 選取其中一個允許的 azuredeploy.json 檔案中列出的 VM 大小 |  | Standard_E2s_v3 |
| `infraVmSize` | 大小基礎結構 VM。 選取其中一個允許的 azuredeploy.json 檔案中列出的 VM 大小 |  | Standard_D4s_v3 |
| `nodeVmSize` | 應用程式節點 VM 的大小。 選取其中一個允許的 azuredeploy.json 檔案中列出的 VM 大小 |  | Standard_D4s_v3 |
| `cnsVmSize` | 容器 (CN) 的原生儲存體節點 VM 的大小。 選取其中一個允許的 azuredeploy.json 檔案中列出的 VM 大小 |  | Standard_E4s_v3 |
| `osImageType` | 若要使用 RHEL 映像。 defaultgallery:隨;marketplace︰ 第三方映像 | defaultgallery <br> Marketplace | defaultgallery |
| `marketplaceOsImage` | 如果`osImageType`是 marketplace 中，然後輸入適當的值 'publisher'、 '提供的'、 'sku'、 'version' 的 marketplace 供應項目。 這個參數是物件類型 |  |  |
| `storageKind` | 若要使用的儲存體類型  | 受控<br> 未受管理 | 受控 |
| `openshiftClusterPrefix` | 叢集用來設定所有節點的主機名稱的前置詞。  介於 1 到 20 個字元 |  | mycluster |
| `minoVersion` | 若要部署 OpenShift 容器平台 3.11 次要版本 |  | 69 |
| `masterInstanceCount` | 若要部署的主機節點的數目 | 1, 3, 5 | 3 |
| `infraInstanceCount` | 數字的基礎結構部署的節點 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 若要部署的節點數目 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | 若要部署的 CN 節點數目 | 3, 4 | 3 |
| `osDiskSize` | （以 gb 為單位） 的 VM 的 OS 磁碟大小 | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | 要附加至節點 （以 gb 為單位） 的 Docker 磁碟區的資料磁碟大小 | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | 以連結至使用 CN 節點 （以 gb 為單位的 glusterfs 的資料磁碟的大小 | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | OS (VM) 登入和初始 OpenShift 使用者的系統管理員使用者名稱 |  | ocpadmin |
| `enableMetrics` | 啟用度量。 計量可讓您需要更多資源所以基礎結構 vm 選擇適當的大小 | true <br> false | false |
| `enableLogging` | 啟用記錄。 elasticsearch pod 需要 8 GB 的 RAM 因此，請選取適當的大小為基礎的 VM | true <br> false | false |
| `enableCNS` | 啟用容器原生儲存體 | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat 訂用帳戶管理員使用者名稱或組織識別碼 |  |  |
| `rhsmPoolId` | 包含 OpenShift 權利的計算節點的 Red Hat Subscription Manager 集區識別碼 |  |  |
| `rhsmBrokerPoolId` | Red Hat Subscription Manager 集區識別碼包含 OpenShift 權利主機和基礎結構節點。 如果您不需要不同的集區識別碼，請輸入 'rhsmPoolId' 相同的集區識別碼 |  |
| `sshPublicKey` | 複製 SSH 公用金鑰這裡 |  |  |
| `keyVaultSubscriptionId` | 包含金鑰保存庫的訂用帳戶的訂用帳戶識別碼 |  |  |
| `keyVaultResourceGroup` | 包含金鑰保存庫的資源群組名稱 |  |  |
| `keyVaultName` | 您所建立的金鑰保存庫名稱 |  |  |
| `enableAzure` | 啟用 Azure 雲端提供者 | true <br> false | true |
| `aadClientId` | Azure 作用中的目錄用戶端識別碼也稱為應用程式識別碼的服務主體 |  |  |
| `domainName` | 若要使用 （如果適用） 的自訂網域名稱的名稱。 設定為 「 無 」 如果沒有部署的完全私人叢集 |  | None |
| `masterClusterDnsType` | OpenShift web 主控台中的網域類型。 'default' 將基礎結構使用 DNS 標籤，主機的公用 IP。 'custom' 可讓您定義您自己的名稱 | 預設值 <br> 自訂 | 預設值 |
| `masterClusterDns` | 自訂的 DNS 名稱，用來存取 OpenShift web 主控台中，如果您選取 [自訂] `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | 如果設定為 'nipio'，`routingSubDomain`將 nip.io。  如果您有自己的網域，您想要使用的路由，請使用 'custom' | nipio <br> 自訂 | nipio |
| `routingSubDomain` | 您想要使用的路由，如果您選取 [自訂] 萬用字元 DNS 名稱 `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 選取要使用現有的虛擬網路，或建立新的虛擬網路 | 現有的 <br> new | new |
| `virtualNetworkResourceGroupName` | 新的虛擬網路，如果您選取 'new' 的資源群組名稱 `virtualNetworkNewOrExisting` |  | resourceGroup().name |
| `virtualNetworkName` | 如果為 'new' 選取建立新的虛擬網路的名稱 `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | 新的虛擬網路的位址首碼 |  | 10.0.0.0/14 |
| `masterSubnetName` | 主要的子網路名稱 |  | mastersubnet |
| `masterSubnetPrefix` | CIDR 用於主要的子網路： 必須是子集的位址首碼 |  | 10.1.0.0/16 |
| `infraSubnetName` | 名稱的基礎結構子網路 |  | infrasubnet |
| `infraSubnetPrefix` | 使用 CIDR 子網路： 必須是子集的位址首碼的基礎結構 |  | 10.2.0.0/16 |
| `nodeSubnetName` | 節點的子網路名稱 |  | nodesubnet |
| `nodeSubnetPrefix` | 必須是位址首碼的子集用於節點的子網路的 CIDR |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 現有的子網路用於主要節點的完整參考。 不需要建立新的 vNet / 子網路 |  |  |
| `existingInfraSubnetReference` | 完整參考現有的子網路的基礎結構節點。 不需要建立新的 vNet / 子網路 |  |  |
| `existingCnsSubnetReference` | CN 節點的現有子網路的完整參考。 不需要建立新的 vNet / 子網路 |  |  |
| `existingNodeSubnetReference` | 至計算節點的現有子網路的完整參考。 不需要建立新的 vNet / 子網路 |  |  |
| `masterClusterType` | 指定的叢集是否使用私人或公用的主要節點。 如果選擇了私用，則主要節點將不會暴露於網際網路透過公用 IP。 相反地，它會使用指定的私人 IP `masterPrivateClusterIp` | 公開 <br> 私用 | 公開 |
| `masterPrivateClusterIp` | 如果未選取私用的主要節點，則私人 IP 位址必須指定用於對主要節點內部的負載平衡器。 此靜態 IP 必須在主要的子網路，且尚未在使用的 CIDR 區塊。 如果未選取公用的主要節點，這個值不會使用，但仍必須指定 |  | 10.1.0.200 |
| `routerClusterType` | 指定是否叢集使用私用或公用基礎結構節點。 如果選擇私用，則基礎結構節點將不會公開網際網路透過公用 IP。 相反地，它會使用指定的私人 IP `routerPrivateClusterIp` | 公開 <br> 私用 | 公開 |
| `routerPrivateClusterIp` | 如果是私用的基礎結構選取節點，則必須指定私人 IP 位址使用的內部負載平衡器的基礎結構節點。 此靜態 IP 必須在主要的子網路，且尚未在使用的 CIDR 區塊。 如果公開金鑰基礎結構中所選取節點，這個值不會使用，但仍必須指定 |  | 10.2.0.200 |
| `routingCertType` | 用於路由網域或預設的自我簽署的憑證的自訂憑證-遵循指示**自訂憑證**區段 | selfsigned <br> 自訂 | selfsigned |
| `masterCertType` | 用於主機網域或預設的自我簽署的憑證的自訂憑證-遵循指示**自訂憑證**區段 | selfsigned <br> 自訂 | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署

> [!NOTE] 
> 下列命令需要 Azure CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti)。

下列範例會使用 myOpenShiftCluster 的部署名稱，將 OpenShift 叢集和所有相關的資源部署到名為 openshiftrg 的資源群組。 範本直接參考自 GitHub 儲存機制，而且會使用名為 azuredeploy.parameters.json 檔案的本機參數檔案。

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

部署需要至少 60 分鐘才能完成，根據部署的節點和設定選項的總數。 部署完成時，OpenShift 主控台的防禦 DNS FQDN 和 URL 會列印到終端機。

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

如果您不想困在命令列等候部署完成，請新增 `--no-wait` 作為群組部署的選項之一。 您可以從 Azure 入口網站，在資源群組的部署區段中擷取部署中的輸出。

## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，請從部署的輸出區段中擷取連線。 藉由連線至 OpenShift 主控台與您的瀏覽器**OpenShift 主控台 URL**。 您也可以防禦主機來透過 ssh 連線。 在以下範例中，管理員使用者名稱是 clusteradmin，而防禦公用 IP DNS FQDN 是 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源

當不再需要資源時，您可以使用 [az group delete](/cli/azure/group) 命令，移除資源群組、OpenShift 叢集和所有相關資源。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-post-deployment.md)
- [Azure 中的 OpenShift 部署疑難排解](./openshift-troubleshooting.md)
- [開始使用 OpenShift 容器平台](https://docs.openshift.com/container-platform) \(英文\)
