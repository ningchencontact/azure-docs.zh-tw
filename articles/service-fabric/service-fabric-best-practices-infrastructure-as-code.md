---
title: Azure Service Fabric 基礎結構即程式碼最佳做法 | Microsoft Docs
description: 管理 Service Fabric 為基礎結構即程式碼的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237422"
---
# <a name="infrastructure-as-code"></a>基礎結構即程式碼

在生產案例中，使用 Resource Manager 範本建立 Azure Service Fabric 叢集。 Resource Manager 範本可提供更好的資源屬性控制，並可確保您擁有一致的資源模型。

您可以在 [GitHub 上的 Azure 範例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中取得可供 Windows 和 Linux 使用的 Resource Manager 範本範例。 這些範本可以用作叢集範本的起點。 下載 `azuredeploy.json` 和 `azuredeploy.parameters.json`，並編輯它們以符合您的自訂需求。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要部署您在上面下載的 `azuredeploy.json` 和 `azuredeploy.parameters.json` 範例，請使用下列 Azure CLI 命令：

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

使用 PowerShell 建立資源

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric 資源

您可以透過 Azure Resource Manager 將應用程式和服務部署到 Service Fabric 叢集。 請參閱[將應用程式和服務視為 Azure Resource Manager 資源進行管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource)了解詳細資訊。 以下是在 Resource Manager 範本資源中包含 Service Fabric 應用程式特定資源的最佳做法。

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

若要 Azure Resource Manager 部署您的應用程式，必須先[建立 sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric 應用程式封裝。 以下 python 指令碼為示範如何建立 sfpkg 的範例：

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure 虛擬機器的作業系統自動升級設定 
升級您的虛擬機器是由使用者啟動的作業，並建議您改用[虛擬機器擴展集自動作業系統升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)適用於 Azure Service Fabric 叢集主機修補程式管理;修補程式協調流程應用程式是一種替代的解決方案，適用於 Azure、 外部裝載雖然 POA 可以在 Azure 中，搭配裝載在 Azure 中常見的原因，偏好使用虛擬機器的作業系統自動升級的 POA 的額外負荷透過 POA。 以下是要啟用自動 OS 升級計算虛擬機器擴展集 Resource Manager 範本內容：

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
搭配使用時自動 OS 升級 Service Fabric，新的 OS 映像會推出一個更新網域來維持在 Service Fabric 中執行之服務的高可用性一次。 若要在 Service Fabric 中利用自動 OS 升級，您的叢集必須設定為使用銀級耐久性層或更高。

請確定下列登錄機碼設定為 false，以防止 windows 主機電腦啟動未經協調的更新：HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

以下是將 WindowsUpdate 登錄機碼設定為 false 計算虛擬機器擴展集 Resource Manager 範本內容：
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric 叢集的升級設定
以下是 Service Fabric 叢集 Resource Manager 範本屬性，以啟用自動升級：
```json
"upgradeMode": "Automatic",
```
若要手動升級您的叢集，下載封包/deb 發佈至叢集虛擬機器，並接著叫用下列 PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>後續步驟

* 在 VM 或執行 Windows Server 的電腦上建立叢集：[適用於 Windows Server 的 Service Fabric 叢集建立](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)
