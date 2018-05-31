---
title: Azure Service Fabric DNS 服務 | Microsoft Docs
description: 使用 Service Fabric 的 DNS 服務來從叢集內部探索微服務。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/27/2017
ms.author: msfussell
ms.openlocfilehash: 656aed1f1fbd3294c4318520058ace480fd2219c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204989"
---
# <a name="dns-service-in-azure-service-fabric"></a>Azure Service Fabric 中的 DNS 服務
「DNS 服務」是一個選用的系統服務，您可以在叢集中啟用以使用 DNS 通訊協定來探索其他服務。 

許多服務 (特別是容器化服務) 都可以有現有的 URL 名稱，因此能夠使用標準 DNS 通訊協定 (而不是「命名服務」通訊協定) 來解析這些名稱，特別是在「隨即轉移」案例中。 DNS 服務可讓您將 DNS 服務對應到某個服務名稱，再由此解析端點 IP 位址。 

DNS 服務會將 DNS 名稱對應到服務名稱，接著服務名稱會由命名服務解析並傳回服務端點。 服務的 DNS 名稱是在建立時提供的。

![服務端點](./media/service-fabric-dnsservice/dns.png)

DNS 服務不支援動態連接埠。 若要解決動態連接埠上所公開的服務，請使用[反向 Proxy 服務](./service-fabric-reverseproxy.md)。

## <a name="enabling-the-dns-service"></a>啟用 DNS 服務
當您使用入口網站建立叢集時，預設會在 [叢集組態] 功能表的 [包括 DNS 服務] 核取方塊中啟用 DNS 服務：

![透過入口網站啟用 DNS 服務][2]

如果您不會使用入口網站來建立叢集，或者您是要更新現有叢集，則需要在範本中啟用 DNS 服務：

- 若要部署新叢集，您可以使用[範例範本](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)或建立自己的 Resource Manager 範本。 
- 若要更新現有叢集，您可以在入口網站上瀏覽至叢集的資源群組，然後按一下 [自動化指令碼] 以使用可反映叢集目前狀態和群組中其他資源的範本。 若要深入了解，請參閱[從資源群組匯出範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group)。

擁有範本後，您可以使用下列步驟來啟用 DNS 服務：

1. 檢查 `Microsoft.ServiceFabric/clusters` 資源的 `apiversion` 是否已設定為 `2017-07-01-preview` 或更新版本，如果不是請加以更新，如下列程式碼片段所示：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 現在，在 `fabricSettings` 區段之後新增下列 `addonFeatures` 區段以啟用 DNS 服務，如下列程式碼片段所示： 

    ```json
        "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "DnsService"
        ],
    ```

3. 一旦您使用前面的變更將叢集範本進行更新之後，將它們加以套用，使升級完成。 升級完成後，DNS 系統服務就會開始在叢集中執行。 服務名稱是 `fabric:/System/DnsService`，而且您可以在 Service Fabric Explorer 的 [系統] 服務區段下找到它。 


## <a name="setting-the-dns-name-for-your-service"></a>為您的服務設定 DNS 名稱
一旦 DNS 服務已在您的叢集中執行，您現在便可以在 `ApplicationManifest.xml` 中透過宣告為預設服務設定 DNS 名稱，或透過 PowerShell 命令為服務設定 DNS 名稱。

在整個叢集中，服務的 DNS 名稱是可解析的。 強烈建議您使用 `<ServiceDnsName>.<AppInstanceName>` 的命名配置；例如，`service1.application1`。 這麼做可確保 DNS 名稱在整個叢集中是唯一的。 如果使用 Docker Compose 來部署應用程式，系統會自動使用此命名配置為服務指派 DNS 名稱。

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>在 ApplicationManifest.xml 中為預設服務設定 DNS 名稱
在 Visual Studio 或慣用的編輯器中開啟您的專案，然後開啟 `ApplicationManifest.xml` 檔案。 移至預設服務區段，然後為每個服務新增 `ServiceDnsName` 屬性。 下列範例示範如何將服務的 DNS 名稱設定為 `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
    <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
    </StatelessService>
    </Service>
```
部署完應用程式之後，在 Service Fabric Explorer 中的服務執行個體會顯示此執行個體的 DNS 名稱，如下圖所示： 

![服務端點][1]

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>使用 Powershell 為服務設定 DNS 名稱
您可以在建立服務時，使用 `New-ServiceFabricService` Powershell 為服務設定 DNS 名稱。 下列範例會建立 DNS 名稱為 `service1.application1` 的新無狀態服務

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="using-dns-in-your-services"></a>在您的服務中使用 DNS
如果您部署多個服務，您便可以使用 DNS 名稱來尋找要通訊對象的其他服務端點。 DNS 服務僅適用於無狀態服務，因為 DNS 通訊協定無法與具狀態服務通訊。 針對具狀態服務，您可以使用 HTTP 呼叫的內建[反向 Proxy 服務](./service-fabric-reverseproxy.md)來呼叫特定的服務分割區。 DNS 服務不支援動態連接埠。 您可以使用反向 Proxy 來解析使用動態連接埠的服務。

下列程式碼會示範如何呼叫另一個服務，這只是一般的 HTTP 呼叫，其中您會提供連接埠和任何選用路徑作為 URL 的一部分。

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="next-steps"></a>後續步驟
若要深入了解叢集內的服務通訊，請參閱[連接服務並與其進行通訊](service-fabric-connect-and-communicate-with-services.md)

[0]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[1]: ./media/service-fabric-dnsservice/servicefabric-explorer-dns.PNG
[2]: ./media/service-fabric-dnsservice/DNSService.PNG
