---
title: App Service on Azure Stack：容錯網域更新 | Microsoft Docs
description: 如何跨容錯網域，在 Azure Stack 上轉散發 Azure App Service
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 851747263879aa89fabe8b168876238a004ea8b2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>如何跨容錯網域，在 Azure Stack 上轉散發 Azure App Service

「適用於：Azure Stack 整合系統」

使用 1802 更新，Azure Stack 現在支援跨容錯網域散發工作負載，這是對於高可用性很重要的一項功能。

> [!IMPORTANT]
> 您必須將您的 Azure Stack 整合系統更新為 1802，才能夠利用容錯網域支援。  本文件僅適用於在 1802 更新之前就已完成的 App Service 資源提供者部署。  如果您在 1802 更新套用到 Azure Stack 之後，才部署 App Service on Azure Stack，則資源提供者已經跨容錯網域散發。
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>跨容錯網域重新平衡 App Service 資源提供者

若要針對 App Service 資源提供者轉散布部署的擴展集，您必須為每個擴展集執行下列步驟。  根據預設，擴展集名稱是：

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> 如果某些背景工作角色層擴展集中未部署任何執行個體，您就不需要重新平衡那些擴展集。  當您在未來將其相應放大時，擴展集將會正確平衡。
>
>

1. 瀏覽至 Azure Stack 系統管理員入口網站中的虛擬機器擴展集。  部署為 App Service 部署一部分的現有擴展集會與執行個體計數資訊一起列出。

    ![列在虛擬機器擴展集 UX 中的 Azure App Service 擴展集][1]

2. 接下來相應放大每個擴展集。  例如，如果您在擴展集中有 3 個現有執行個體，您必須相應放大為 6 個，以便跨容錯網域佈建 3 個新執行個體。
    a. [在 PowerShell 中設定 Azure Stack 管理環境](azure-stack-powershell-configure-admin.md) b. 使用以下範例來相應放大擴展集的規模：
        ```powershell
                Login-AzureRMAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> 視角色類型和執行個體數目而定，此步驟可能需要幾小時的時間才能完成。
>
>

3. 在 [App Service 系統管理角色] 刀鋒視窗中監視新角色執行個體的狀態。  藉由在清單中按一下角色類型，檢查個別角色執行個體的狀態

    ![Azure App Service on Azure Stack 角色][2]

4. 當新的執行個體處於「就緒」狀態時，返回 [虛擬機器擴展集] 刀鋒視窗並且**刪除**舊的執行個體。

5. 針對**每個**虛擬機器擴展集重複這些步驟。

## <a name="next-steps"></a>後續步驟

您也可以嘗試其他[平台即服務 (PaaS) 服務](azure-stack-tools-paas-services.md)。

* [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 資源提供者](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
