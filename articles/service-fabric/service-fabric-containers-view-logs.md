---
title: 在 Azure Service Fabric 中檢視容器記錄 | Microsoft Docs
description: 說明如何使用 Service Fabric Explorer，對執行中的 Service Fabric 容器服務檢視其容器記錄。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: ryanwi
ms.openlocfilehash: b2b3562f65e7e861b7e4dff7b7c26d58081ff29e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211920"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>檢視 Service Fabric 容器服務的記錄
Azure Service Fabric 是一種容器協調器，可支援 [Linux 和 Windows 容器](service-fabric-containers-overview.md)。  本文說明如何檢視執行中容器服務或無作用容器的容器記錄，以便您診斷問題並進行疑難排解。

## <a name="access-the-logs-of-a-running-container"></a>存取執行中容器的記錄
您可以使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 來存取容器記錄。  在 Web 瀏覽器中瀏覽至 [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)，從叢集的管理端點開啟 Service Fabric Explorer。  

容器記錄位於執行容器服務執行個體所在的叢集節點上。 例如，您可以取得 [Linux 投票範例應用程式](service-fabric-quickstart-containers-linux.md)的 Web 前端容器記錄。 在樹狀檢視中，依序展開 [Cluster]\(叢集\)>[Applications]\(應用程式\)>[VotingType]>[fabric:/Voting/azurevotefront]。  然後展開分割區 (在此範例中為 d1aa737e-f22a-e347-be16-eec90be24bc1)，並查看叢集節點 _lnxvm_0 上執行的容器。

在樹狀結構檢視中尋找 _lnxvm_0 節點上的程式碼套件，請展開 [Nodes]\(節點\)>[_lnxvm_0]>[fabric:/Voting]>[azurevotfrontPkg]>[Code Packages]\(程式碼套件\)>[code]\(程式碼\)。  然後選取 [容器記錄] 選項以顯示容器記錄。

![Service Fabric 平台][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>存取無作用或已損毀容器的記錄
從 v6.2 開始，您也可以使用 [REST API](/rest/api/servicefabric/sfclient-index) 或 [Service Fabric CLI (SFCTL)](service-fabric-cli.md) 命令來擷取無作用或已損毀容器的記錄。

### <a name="rest"></a>REST
使用[取得部署在節點上的容器記錄](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode)作業，來取得已損毀容器的記錄。 指定執行容器的節點名稱、應用程式名稱、服務資訊清單名稱，以及程式碼套件名稱。  指定 `&Previous=true`。 回應包含的容器記錄會來自程式碼套件執行個體的無作用容器。

要求 URI 具有下列形式：

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

要求範例︰
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 回應本文：
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
使用 [sfctl service get-container-logs](service-fabric-sfctl-service.md) 命令來擷取已損毀容器的記錄。  指定執行容器的節點名稱、應用程式名稱、服務資訊清單名稱，以及程式碼套件名稱。 指定 `-previous` 旗標。  回應包含的容器記錄會來自程式碼套件執行個體的無作用容器。

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –previous
```
回應：
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>後續步驟
- 逐步解說[建立 Linux 容器應用程式的教學課程](service-fabric-tutorial-create-container-images.md)。
- 深入了解 [Service Fabric 和容器](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
