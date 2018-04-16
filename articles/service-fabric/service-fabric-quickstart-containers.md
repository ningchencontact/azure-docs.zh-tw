---
title: 建立 Azure Service Fabric Windows 容器應用程式 | Microsoft Docs
description: 在本教學課程中，您會在 Azure Service Fabric 上建立第一個 Windows 容器應用程式。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 47a4e75699e024dae367524f16eb23fb72043ef5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>快速入門：在 Azure 上部署 Service Fabric Windows 容器應用程式
Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理可調整和可信賴的微服務與容器。 

在 Service Fabric 叢集上的 Windows 容器中執行現有的應用程式，不需要變更您的應用程式。 本快速入門示範如何在 Service Fabric 應用程式中部署預先建立的 Docker 容器映像。 當您完成時，您會有執行中的 Windows Server 2016 Nano Server 和 IIS 容器。 本快速入門說明如何部署 Windows 容器，請閱讀[本快速入門](service-fabric-quickstart-containers-linux.md)以部署 Linux 容器。

![IIS 預設網頁][iis-default]

在此快速入門中，您將了解如何：

* 封裝 Docker 映像容器
* 設定通訊
* 建置及封裝 Service Fabric 應用程式
* 將容器應用程式部署至 Azure

## <a name="prerequisites"></a>先決條件
* Azure 訂用帳戶 (您可以建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))。
* 執行下列項目的開發電腦︰
  * Visual Studio 2015 或 Visual Studio 2017。
  * [Service Fabric SDK 和工具](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>使用 Visual Studio 封裝 Docker 映像容器
Service Fabric SDK 和工具會提供一個服務範本，協助您將容器部署到 Service Fabric 叢集。

以「系統管理員」身分啟動 Visual Studio。  選取 [檔案] > [新增] > [專案]。

選取 [Service Fabric 應用程式]，將它命名為 "MyFirstContainer"，然後按一下 [確定]。

從 [裝載的容器和應用程式] 範本選取 [容器]。

在 [映像名稱] 中，輸入 "microsoft/iis:nanoserver"、[Windows Server Nano Server 和 IIS 基底映像](https://hub.docker.com/r/microsoft/iis/)。 

將您的服務命名為 "MyContainerService"，然後按一下 [確定]。

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>設定通訊和容器連接埠對主機連接埠的對應
此服務需要端點進行通訊。  在此快速入門中，容器化服務會接聽連接埠 80。  在 [方案總管] 中，開啟 *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*。  更新 ServiceManifest.xml 檔案中現有的 `Endpoint`，並新增通訊協定、連接埠及 uri 配置： 

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```
提供 `UriScheme`，就會自動向「Service Fabric 命名」服務註冊容器端點以供搜尋。 本文結尾會提供完整的 ServiceManifest.xml 範例檔案。 

設定容器連接埠對主機的連接埠對應，以便讓通訊埠 80 上服務的連入要求對應到容器上的連接埠 80。  在 [方案總管] 中，開啟 *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* 並在 `ContainerHostPolicies` 中指定 `PortBinding` 原則。  在本快速入門中，`ContainerPort` 為 80，而 `EndpointRef` 是 "MyContainerServiceTypeEndpoint" (服務資訊清單中所定義的端點)。    

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

本文結尾會提供完整的 ApplicationManifest.xml 範例檔案。

## <a name="create-a-cluster"></a>建立叢集
若要將應用程式部署到 Azure 中的叢集，您可以加入合作對象叢集。 合作對象的叢集是免費的限時 Service Fabric 叢集，裝載於 Azure 上，並且由任何人都可以部署應用程式並了解平台的 Service Fabric 小組執行。  叢集會針對節點對節點和用戶端對節點安全性，使用單一的自我簽署憑證。 合作對象叢集支援容器。 如果您決定設定和使用您自己的叢集，叢集必須在支援容器的 SKU 上執行 (例如 Windows Server 2016 Datacenter with Containers)。

登入並[加入 Windows 叢集](http://aka.ms/tryservicefabric) \(英文\)。 藉由按一下 [PFX] 連結，將 PFX 憑證下載至您的電腦。 按一下 [如何連線至安全的合作對象叢集?] 連結，並複製憑證密碼。 後續步驟中會使用憑證、憑證密碼和 [連線端點] 值。

![PFX 和連線端點](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> 每小時可用的合作對象叢集數目有限。 如果您在嘗試註冊合作對象叢集時收到錯誤，您可以等候一段時間再重試，也可以遵循[部署 .NET 應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application)教學課程中的這些步驟，在您的 Azure 訂用帳戶中建立 Service Fabric 叢集並對其部署應用程式。 透過 Visual Studio 建立的叢集支援容器。 在叢集中部署並確認應用程式之後，您可以直接跳到本快速入門的＜[Service Fabric 應用程式和服務資訊清單的完整範例](#complete-example-service-fabric-application-and-service-manifests)＞。 
>

在 Windows 電腦上，將 PFX 安裝在 *CurrentUser\My* 憑證存放區中。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

請記住指紋以在後續步驟中使用。  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>使用 Visual Studio 將應用程式部署至 Azure
應用程式備妥後，即可直接從 Visual Studio 將其部署到叢集。

以滑鼠右鍵按一下 [方案總管] 中的 **MyFirstContainer**，並選擇 [發佈]。 [發行] 對話方塊隨即出現。

將合作對象叢集頁面上的 [連線端點] 複製到 [連線端點] 欄位。 例如： `zwin7fh14scd.westus.cloudapp.azure.com:19000`。 按一下 [進階連線參數] 並確認連線參數資訊。  *FindValue* 和 *ServerCertThumbprint* 值必須符合前一個步驟中安裝的憑證指紋。 

![[發佈] 對話方塊](./media/service-fabric-quickstart-containers/publish-app.png)

按一下 [發佈] 。

叢集中的每個應用程式都必須有一個唯一的名稱。  合作對象叢集是公用的共用環境，可能會與現有的應用程式發生衝突。  如果發生名稱衝突，請將 Visual Studio 專案重新命名並再次部署。

開啟瀏覽器並瀏覽至合作對象叢集頁面中指定的**連線端點**。 您可以選擇性地在 URL 前面加上配置識別碼 `http://`，並將連接埠 `:80` 附加到 URL。 例如： http://zwin7fh14scd.westus.cloudapp.azure.com:80。 您應會看到 IIS 預設網頁：![IIS 預設網頁][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>完整範例 Service Fabric 應用程式和服務資訊清單
以下是本快速入門中使用的完整服務和應用程式資訊清單。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已了解如何：

* 封裝 Docker 映像容器
* 設定通訊
* 建置及封裝 Service Fabric 應用程式
* 將容器應用程式部署至 Azure

若要深入了解如何在 Service Fabric 中使用 Windows 容器，請繼續進行教學課程以了解 Windows 容器應用程式。

> [!div class="nextstepaction"]
> [建立 Windows 容器應用程式](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
