---
title: 深入了解建立及發佈.Net Core 應用程式到遠端的 Azure Service Fabric Linux 叢集 |Microsoft Docs
description: 建立及發佈.Net Core 目標為遠端的 Linux 叢集，從 Visual Studio 的應用程式
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: 46d76edbe8cede12e8c7811f43c28a65c1ebaed0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078660"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>使用 Visual Studio 來建立及發佈.Net Core 為目標遠端 Linux Service Fabric 叢集應用程式
使用 Visual Studio 工具，您可以開發及發佈 Service Fabric 的.Net Core 為目標 Linux Service Fabric 叢集應用程式。 SDK 版本必須為 3.4 或更新版本才能部署.Net Core 為目標 Linux Service Fabric 的應用程式叢集從 Visual Studio。

> [!Note]
> Visual Studio 不支援以 Linux 為目標的偵錯 Service Fabric 應用程式。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>建立以.Net Core 為目標的 Service Fabric 應用程式
1. 以**系統管理員**身分啟動 Visual Studio。
2. 建立專案，具有**檔案]-> [新增專案]-> [** 。
3. 在 [**新的專案**] 對話方塊中，選擇**雲端 Service Fabric 應用程式]-> [** 。
![create-application]
4. 應用程式命名，然後按一下**Ok**。
5. 在上**新的 Service Fabric**頁面上，選取您想要在建立的服務的型別 **.Net Core 區段**。
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>部署至遠端 Linux 叢集
1. 在 [方案總管] 中，以滑鼠右鍵按一下應用程式，然後選取**建置**。
![build-application]
2. 一旦完成應用程式的建置程序，以滑鼠右鍵按一下服務，並選取 編輯**csproj 檔案**。
![edit-csproj]
3. 編輯 UpdateServiceFabricManifestEnabled 屬性從 True 表示**假**服務是否**動作項目專案類型**。 如果您的應用程式並沒有動作項目服務，請跳至步驟 4。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> 將 UpdateServiceFabricManifestEnabled 設定為 false，將會停用更新 ServiceManifest.xml 以在建置期間。 因為新增、 移除或重新命名為服務的任何變更這類不將會反映在 ServiceManifest.xml 中上。 如果您進行任何變更必須的更新 ServiceManifest 以手動方式或暫時設為 true，並建置服務，將會更新 ServiceManifest.xml，然後再將其還原 UpdateServiceFabricManifestEnabled 備份為 false。
>

4. 更新從 win7-x64 RuntimeIndetifier，目標平台服務專案中。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. 的 ServiceManifest 中更新 進入點程式移除.exe。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 在 [方案總管] 中，以滑鼠右鍵按一下應用程式，然後選取**發佈**。 [發佈]  對話方塊隨即出現。
7. 在 **連線端點**，選取您想要為目標遠端 Service Fabric Linux 叢集的端點。
![publish-application]

<!--Image references-->
[create-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publish-application]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>後續步驟
* 深入了解[Service Fabric 和.Net Core 使用者入門](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
