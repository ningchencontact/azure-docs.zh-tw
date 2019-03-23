---
title: 設定 Azure Service Fabric 容器服務的 gMSA | Microsoft Docs
description: 了解如何為 Azure Service Fabric 中執行的容器設定 gMSA。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo, subramar
ms.openlocfilehash: 8f0c569495775dcda6dec189d223067435368249
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370205"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>為在 Service Fabric 上執行的 Windows 容器設定 gMSA

為了設定 gMSA (群組受控服務帳戶)，所有叢集節點上都會放置認證規格檔案 (`credspec`)。 您可以使用 VM 擴充功能將此檔案複製到所有節點上。  `credspec` 檔案必須包含 gMSA 帳戶資訊。 如需詳細資訊`credspec`檔案，請參閱 <<c2> [ 建立認證的規格](https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)。認證規格和 `Hostname` 標記會於應用程式資訊清單中指定。 `Hostname` 標記必須符合用來執行容器的 gMSA 帳戶名稱。  `Hostname` 標記可讓容器使用 Kerberos 驗證向網域中的其他服務驗證其自身。  下列程式碼片段會顯示用來在應用程式資訊清單中指定 `Hostname` 和 `credspec` 的範例：

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
若要了解下一個步驟，請閱讀下列文章：

* [將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
