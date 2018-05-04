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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>檢視 Service Fabric 容器服務的記錄
Azure Service Fabric 是一種容器協調器，可支援 [Linux 和 Windows 容器](service-fabric-containers-overview.md)。  本文說明如何檢視執行中容器服務的容器記錄，以便您診斷問題並進行疑難排解。

## <a name="access-container-logs"></a>存取容器記錄
您可以使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 來存取容器記錄。  在 Web 瀏覽器中瀏覽至 [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)，從叢集的管理端點開啟 Service Fabric Explorer。  

容器記錄位於執行容器服務執行個體所在的叢集節點上。 例如，您可以取得 [Linux 投票範例應用程式](service-fabric-quickstart-containers-linux.md)的 Web 前端容器記錄。 在樹狀檢視中，依序展開 [Cluster]\(叢集\)>[Applications]\(應用程式\)>[VotingType]>[fabric:/Voting/azurevotefront]。  然後展開分割區 (在此範例中為 d1aa737e-f22a-e347-be16-eec90be24bc1)，並查看叢集節點 _lnxvm_0 上執行的容器。

在樹狀結構檢視中尋找 _lnxvm_0 節點上的程式碼套件，請展開 [Nodes]\(節點\)>[_lnxvm_0]>[fabric:/Voting]>[azurevotfrontPkg]>[Code Packages]\(程式碼套件\)>[code]\(程式碼\)。  然後選取 [容器記錄] 選項以顯示容器記錄。

![Service Fabric 平台][Image1]


## <a name="next-steps"></a>後續步驟
- 逐步解說[建立 Linux 容器應用程式的教學課程](service-fabric-tutorial-create-container-images.md)。
- 深入了解 [Service Fabric 和容器](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
