---
title: 在 Azure DevTest Labs 中使用的平台為-即服務 (PaaS) 服務 |Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中使用的平台為-即服務 (Pass) 服務。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 7cdd185cddbd2403b72ff0e06530913af0b031de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233117"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中使用的平台為-即服務 (PaaS) 服務
DevTest Labs 支援 PaaS 透過環境功能。 在 DevTest Labs 中的環境受到 Git 存放庫中預先設定的 Azure Resource Manager 範本。 環境可以包含 PaaS 和 IaaS 資源。 可讓您建立複雜的系統，其中可以包含 Azure 資源，例如虛擬機器、 資料庫、 虛擬網路和 Web 應用程式，專為可一起搭配。 這些範本可讓一致的部署和使用原始程式碼控制的環境的改良的管理。 

適當地設定系統可讓下列案例： 

- 開發人員擁有獨立與多個環境
- 以非同步方式在不同的組態上測試
- 整合到預備和生產的管線，而不需要任何變更 」 範本
- 開發電腦和在同一個實驗室環境，可改善管理容易且成本報告。  

DevTest Labs 資源提供者建立實驗室使用者代表資源，因此不需要提供給實驗室使用者，若要在使用 PaaS 資源的任何額外的權限。 下圖顯示 Service Fabric 叢集做為在實驗室環境。

![Service Fabric 叢集設環境](./media/create-environment-service-fabric-cluster/cluster-created.png)

如需有關設定環境，請參閱[使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 DevTest Labs 具有公用存放庫的 Azure Resource Manager 範本可供您建立環境，而不必自行連接到外部的 GitHub 來源。 如需有關公用環境的詳細資訊，請參閱[在 Azure DevTest Labs 中的設定並使用公用環境](devtest-lab-configure-use-public-environments.md)。

在大型組織中，開發小組通常會提供環境，例如自訂/隔離測試環境。 可能會有可供業務單位或部門內的所有小組的環境。 IT 小組可能會想要提供其可供組織中的所有小組的環境。  

## <a name="customizations"></a>自訂項目

#### <a name="sandbox"></a>沙箱 
實驗室擁有者可以自訂實驗室環境，以變更使用者的角色**讀者**要**參與者**資源群組內。 這項功能處於**實驗室設定**頁面**組態和原則**的實驗室。 在角色中的這項變更可讓使用者加入或移除該環境內的資源。 如果您想要限制存取更進一步，使用 Azure 原則。 這項功能可讓您自訂的資源或無法存取訂用帳戶層級的組態。

#### <a name="custom-tokens"></a>自訂權杖
沒有外部的資源群組，並且專屬於範本可以存取的環境的某些自訂實驗室資訊。 以下列出其中一些： 

- 實驗室網路識別碼
- 位置
- 預存的 Resource Manager 範本檔案的儲存體帳戶。 
 
#### <a name="lab-virtual-network"></a>實驗室的虛擬網路
[連接到實驗室的虛擬網路的環境](connect-environment-lab-virtual-network.md)篇文章說明如何修改您的 Resource Manager 範本，以使用`$(LabSubnetId)`語彙基元。 建立環境時，`$(LabSubnetId)`語彙基元會取代第一個子網路標示所在**建立虛擬機器中的可使用**選項設定為 **，則為 true**。 它可讓我們的環境，以使用先前建立的網路。 如果您想要在測試中的環境中使用相同的 Resource Manager 範本，做為預備和生產環境，使用`$(LabSubnetId)`做為 Resource Manager 範本參數中的預設值。 

#### <a name="environment-storage-account"></a>環境的儲存體帳戶
DevTest Labs 支援使用[Resource Manager 範本的巢狀](../azure-resource-manager/resource-group-linked-templates.md)。 [如何 Azure DevTest Labs 可讓巢狀的 Resource Manager 範本部署用於測試環境更容易](https://azure.microsoft.com/updates/azure-devtest-labs-streamlined-nested-arm-template-deployment-support-for-arm-template-based-environments)篇文章說明如何使用`_artifactsLocation`和`_artifactsLocationSasToken`語彙基元，以建立 Resource Manager 範本中的 URI相同的資料夾或在主要範本巢狀資料夾中。 如需有關這兩個權杖的詳細資訊，請參閱 <<c0>  **部署成品**一節[Azure Resource Manager-最佳作法指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)。

## <a name="user-experience"></a>使用者體驗

## <a name="developer"></a>開發人員
若要建立特定的環境，開發人員會使用相同的工作流程來建立 VM。 他們選取的機器映像與環境，並輸入範本所需的必要資訊。 每個開發人員無須環境可讓部署的變更和改進的內部迴圈偵錯。 可以隨時使用最新的範本建立環境。  這項功能可讓您終結並重新建立以減少停機時間不需要手動建立系統，或從錯誤測試復原環境。  

### <a name="testing"></a>測試
研發/測試實驗室環境可允許進行獨立測試的特定程式碼和組態以非同步的方式。 常見的做法是從個別的提取要求設定程式碼的環境，並啟動任何自動化的測試。 自動化的測試執行到完成為止之後, 所有手動測試可以對執行特定的環境。 通常是完成此程序，CI/CD 管線的一部分。 

## <a name="management-experience"></a>管理體驗

### <a name="cost-tracking"></a>成本追蹤
成本追蹤功能已包含在不同的環境內的 Azure 資源的整體成本趨勢。 資源的成本不會中斷 out 環境中不同的資源，但會將環境顯示成單一的成本。

### <a name="security"></a>安全性
使用 DevTest Labs 的正確設定 Azure 訂用帳戶可以[限制只能透過實驗室的 Azure 資源的存取權](devtest-lab-add-devtest-user.md)。 具有環境、 實驗室擁有者可以允許使用者存取不允許任何其他 Azure 資源的存取權的 PaaS 資源，使用已核准的組態。 在案例中，實驗室使用者自訂環境，實驗室擁有者可以允許參與者存取權。 參與者存取權可讓實驗室使用者新增或移除只能在受管理的資源群組內的 Azure 資源。 它可讓您更容易追蹤和管理與讓使用者的參與者存取權的訂用帳戶。

### <a name="automation"></a>自動化
自動化是大規模的有效的生態系統的重要元件。 必須處理管理或跨訂用帳戶和實驗室追蹤多個環境自動化。

### <a name="cicd-pipeline"></a>CI/CD 管線
在 DevTest Labs 中的 PaaS 服務可協助改善 CI/CD 管線具有焦點控制存取實驗室的部署。

## <a name="next-steps"></a>後續步驟
請參閱下列文章中有關環境的詳細資料： 

- 
- [透過 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定，並在 Azure DevTest Labs 中使用公用環境](devtest-lab-configure-use-public-environments.md)
- [建立環境與 Azure DevTest Labs 中的獨立 Service Fabric 叢集](create-environment-service-fabric-cluster.md)
- [在 Azure DevTest Labs 的實驗室的虛擬網路連線的環境](connect-environment-lab-virtual-network.md)
- [將環境整合到您的 Azure DevOps 的 CI/CD 管線](integrate-environments-devops-pipeline.md)
 





