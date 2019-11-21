---
title: Service Fabric Azure Files Volume Driver (GA) | Microsoft Docs
description: Service Fabric 支援使用 Azure 檔案服務以備份來自您容器的磁碟區。
services: service-fabric
author: athinanthny
manager: chackdan
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.topic: conceptual
ms.date: 6/10/2018
ms.author: atsenthi
ms.openlocfilehash: 168e4327dc66474b2e43afdec091c5a44c1399eb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229269"
---
# <a name="service-fabric-azure-files-volume-driver"></a>Service Fabric Azure Files Volume Driver
The Azure Files volume plugin, a [Docker volume plugin](https://docs.docker.com/engine/extend/plugins_volume/) that provides [Azure Files](/azure/storage/files/storage-files-introduction) based volumes for Docker containers is now **GA (Generally Available)** .

此 Docker 磁碟區外掛程式會封裝為可部署至 Service Fabric 叢集的 Service Fabric 應用程式。 其目的是為部署至叢集的其他 Service Fabric 容器應用程式，提供以 Azure 檔案儲存體為基礎的磁碟區。

> [!NOTE]
> Version 6.5.661.9590 of the Azure Files volume plugin is a GA(Generally available) release. 
>

## <a name="prerequisites"></a>必要條件
* Azure 檔案服務磁碟區外掛程式的 Windows 版本只能在 [Windows Server 1709 版](/windows-server/get-started/whats-new-in-windows-server-1709)、[Windows 10 1709 版](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709)或更新版本的作業系統上運作。

* Azure 檔案服務磁碟區外掛程式的 Linux 版本可在 Service Fabric 所支援的所有作業系統版本上運作。

* Azure 檔案磁碟區外掛程式只適用於 Service Fabric 6.2 版及更新版本。

* 請依照 [Azure 檔案服務文件](/azure/storage/files/storage-how-to-create-file-share) \(英文\) 中的指示，為 Service Fabric 容器應用程式建立一個檔案共用作為磁碟區使用。

* 您將會需要安裝[包含 Service Fabric 模組的 Powershell](/azure/service-fabric/service-fabric-get-started) 或 [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)。

* If you are using Hyper-V containers, the following snippets need to be added in the ClusterManifest (local cluster) or fabricSettings section in your Azure Resource Manager template (Azure cluster) or ClusterConfig.json (standalone cluster).

在 ClusterManifest 中，必須在 Hosting 區段中新增下列內容。 In this example, the volume name is **sfazurefile** and the port it listens to on the cluster is **19100**. Replace them with the correct values for your cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

In the fabricSettings section in your Azure Resource Manager template (for Azure deployments) or ClusterConfig.json (for standalone deployments), the following snippet needs to be added. Again, replace the volume name and port values with your own.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```


## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Deploy a sample application using Service Fabric Azure Files volume driver

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Using Azure Resource Manager via the provided Powershell script (recommended)

If your cluster is based in Azure, we recommend deploying applications to it using the Azure Resource Manager application resource model for ease of use and to help move towards the model of maintaining infrastructure as code. This approach eliminates the need to keep track of the app version for the Azure Files volume driver. It also enables you to maintain separate Azure Resource Manager templates for each supported OS. The script assumes you are deploying the latest version of the Azure Files application and takes parameters for OS type, cluster subscription ID, and resource group. You can download the script from the [Service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip). Note that this automatically sets the ListenPort, which is the port on which the Azure Files volume plugin listens for requests from the Docker daemon, to 19100. You can change it by adding parameter named "listenPort". Ensure that the port does not conflict with any other port that the cluster or your applications uses.
 

Azure Resource Manager deployment command for Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Azure Resource Manager deployment command for Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Once you've successfully run the script, you can skip to the [configuring your application section.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Manual deployment for standalone clusters

The Service Fabric application that provides the volumes for your containers can be downloaded from the [Service Fabric download site](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.516.9494.zip). 您可以透過 [PowerShell](./service-fabric-deploy-remove-applications.md)、[CLI](./service-fabric-application-lifecycle-sfctl.md) 或 [FabricClient API](./service-fabric-deploy-remove-applications-fabricclient.md) 將應用程式部署到叢集。

1. Using the command line, change directory to the root directory of the downloaded application package.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Next, copy the application package to the image store  with the appropriate values for [ApplicationPackagePath] and [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. 註冊應用程式類型

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Create the application, paying close attention to the **ListenPort** application parameter value. This value is the port on which the Azure Files volume plugin listens for requests from the Docker daemon. Ensure that the port provided to the application matches the VolumePluginPorts in the ClusterManifest and does not conflict with any other port that the cluster or your applications uses.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> Windows Server 2016 Datacenter 不支援將 SMB 掛接對應至容器 ([該功能僅在 Windows Server 1709 版上受到支援](/virtualization/windowscontainers/manage-containers/container-storage))。 這條件約束可避免在早於 1709 的版本上使用網路磁碟區對應和 Azure 檔案服務磁碟區驅動程式。

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>在本機開發叢集上部署應用程式
Follow steps 1-3 from the [above.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 Azure 檔案服務磁碟區外掛程式應用程式的預設服務執行個體計數為 -1，這表示針對叢集中的每個節點都有部署一個服務的執行個體。 但是，將 Azure 檔案服務磁碟區外掛程式應用程式部署到本機開發叢集上時，服務執行個體計數應指定為 1。 這可以透過 **InstanceCount** 應用程式參數來完成。 Therefore, the command for creating the Azure Files volume plugin application on a local development cluster is:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>設定您的應用程式以使用磁碟區
The following snippet shows how an Azure Files based volume can be specified in the application manifest file of your application. 需要關注的特定元素為 **Volume** 標記：

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Azure 檔案服務磁碟區外掛程式的驅動程式名稱為 **sfazurefile**。 This value is set for the **Driver** attribute of the **Volume** tag element in the application manifest.

In the **Volume** tag in the snippet above, the Azure Files volume plugin requires the following attributes:
- **Source**：這是磁碟區的名稱。 使用者可以為他們的磁碟區選擇任何名稱。
- **Destination** - This attribute is the location that the volume is mapped to within the running container. 因此，您的目的地不能是容器內的現有位置

如上述程式碼片段的 **DriverOption** 元素中所示，Azure 檔案服務磁碟區外掛程式支援下列驅動程式選項：
- **shareName**：為容器提供磁碟區之 Azure 檔案服務檔案共用的名稱。
- **storageAccountName**：包含 Azure 檔案服務檔案共用之 Azure 儲存體帳戶的名稱。
- **storageAccountKey**：包含 Azure 檔案服務檔案共用之 Azure 儲存體帳戶的存取金鑰。
- **storageAccountFQDN** - 與儲存體帳戶相關聯的網域名稱。 如果未指定 storageAccountFQDN，網域名稱會使用預設的尾碼 (.file.core.windows.net) 和 storageAccountName 來組成。  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>使用您自己的磁碟區或記錄驅動程式
Service Fabric 也允許使用您自己的自訂[磁碟區](https://docs.docker.com/engine/extend/plugins_volume/)或[記錄](https://docs.docker.com/engine/admin/logging/overview/)驅動程式。 如果叢集上未安裝 Docker 磁碟區/記錄驅動程式，可以使用 RDP/SSH 通訊協定來手動安裝它。 您可以透過[虛擬機器擴展集啟動指令碼](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)或 [SetupEntryPoint 指令碼](/azure/service-fabric/service-fabric-application-model)，使用這些通訊協定執行安裝。

安裝[適用於 Azure 的 Docker 磁碟區驅動程式](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)的指令碼範例如下所示：

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

在您的應用程式中，若要使用自己安裝的磁碟區或記錄驅動程式，您必須在應用程式資訊清單 **ContainerHostPolicies** 底下的 **Volume** 和 **LogConfig** 元素中指定適當的值。

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

指定磁碟區外掛程式時，Service Fabric 會使用指定的參數自動建立磁碟區。 **[磁碟區]** 元素的 **[來源]** 標記是磁碟區的名稱，而 **[驅動程式]** 標記會指定磁碟區驅動程式的外掛程式。 [目的地] 標記是 [來源] 在執行容器內所對應的位置。 因此，您的目的地不能是您容器內的現有位置。 您可以使用 [DriverOption] 標記來指定選項，如下所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

先前資訊清單程式碼片段中所示的磁碟區支援應用程式參數 (尋找 `MyStorageVar` 以取得使用範例)。

如果指定了 Docker 記錄驅動程式，您就必須在叢集中部署代理程式 (或容器) 來處理記錄。 [DriverOption] 標記可以用來指定記錄驅動程式的選項。

## <a name="next-steps"></a>後續步驟
* 若要查看容器範例 (包括磁碟區驅動程式)，請瀏覽 [Service Fabric 容器範例](https://github.com/Azure-Samples/service-fabric-containers) \(英文\)
* 若要將容器部署到 Service Fabric 叢集，請參閱[在 Service Fabric 上部署容器](service-fabric-deploy-container.md)一文
