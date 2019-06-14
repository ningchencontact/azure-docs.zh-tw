---
title: 建立環境與 Azure DevTest Labs 中的 Service Fabric 叢集 |Microsoft Docs
description: 了解如何建立環境與獨立的 Service Fabric 叢集，以及啟動和停止使用排程的叢集。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312022"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>建立環境與 Azure DevTest Labs 中的獨立 Service Fabric 叢集
本文章提供有關如何使用獨立的 Service Fabric 叢集，在 Azure DevTest Labs 中建立環境的資訊。 

## <a name="overview"></a>概觀
DevTest Labs Azure Resource Management 範本所定義，可以建立獨立的測試環境。 這些環境包含兩個的 IaaS 資源，例如虛擬機器和 PaaS 資源，例如 Service Fabric。 DevTest Labs 可讓您管理在環境中的虛擬機器提供命令，以控制虛擬機器。 這些命令可讓您能夠啟動或停止虛擬機器上的排程。 同樣地，DevTest Labs 也有助於您管理在環境中的 Service Fabric 叢集。 您可以啟動或停止 Service Fabric 叢集的環境中，以手動方式或透過排程。

## <a name="create-a-service-fabric-cluster"></a>建立 Service Fabric 叢集
在 DevTest Labs 中使用環境來建立 Service Fabric 叢集。 在 Git 儲存機制中的 Azure Resource Manager 範本會定義每個環境。 [公開的 Git 儲存機制](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)研發/測試實驗室包含建立中的 Service Fabric 叢集 Resource Manager 範本[ServiceFabric 叢集](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)資料夾。 

1. 使用下列文章中的指示，首先，在 Azure DevTest Labs 建立實驗室：[建立實驗室](devtest-lab-create-lab.md)。 請注意，**公用環境**選項**上**預設。 
2. 確認 Service Fabric 提供者註冊訂用帳戶，遵循下列步驟：
    1. 選取 **訂用帳戶**左側導覽功能表中，然後選取您**訂用帳戶**
    2. 在 **訂用帳戶**頁面上，選取**資源提供者**中**設定**左側功能表上的一節。 
    3. 如果**Microsoft.ServiecFabric**並不是已註冊、 select**註冊**。 
3. 在實驗室的 [DevTest Lab]  頁面上，選取工具列上的 [+ 新增]  。 
    
    ![在工具列上加入按鈕](./media/create-environment-service-fabric-cluster/add-button.png)
3. 在 **選擇基底**頁面上，選取**Service Fabric 實驗室叢集**清單中。 

    ![選取清單中的 Service Fabric 實驗室叢集](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 在 **設定**頁面上，執行下列步驟： 
    1. 指定**名稱**為您的叢集**環境**。 這是在 Azure 中將建立 Service Fabric 叢集資源群組的名稱。 
    2. 選取 **作業系統 (OS)** 叢集虛擬機器。 預設值為：**Windows**。
    3. 指定的名稱**系統管理員**叢集。 
    4. 指定**密碼**系統管理員。 
    5. 針對**憑證**，Base64 編碼字串形式輸入您的憑證資訊。 若要建立的憑證，請執行下列步驟：
        1. 下載**建立 ClusterCertificate.ps1**檔案[Git 儲存機制](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)。 或者，複製您的電腦上的存放庫。 
        2. 啟動 **PowerShell**。 
        3. 執行**ps1**檔案中，使用命令`.\Create-ClusterCertificate.ps1`。 您會看到文字檔案，您需要填寫與憑證相關的欄位，在此頁面上的資訊在記事本中開啟。 上也提供本文中使用的原始碼。 
    6. 請輸入**憑證密碼**。
    7. 指定**指紋**為您的憑證。
    8. 選取 **新增**上**Configure Settings**頁面。 

        ![設定叢集設定](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 在建立叢集之後，您會看到您在上一個步驟中提供的環境的資源群組名稱。 當您展開時，您會看到它的 Service Fabric 叢集。 如果資源群組的狀態停留在**Creating**，選取**重新整理**工具列上。 **Service Fabric 叢集**環境會建立在 Linux 或 Windows 上的 5 個節點 1 nodetype 叢集。

    在下列範例中， **mysfabricclusterrg**是專為 Service Fabric 叢集中建立的資源群組名稱。 請務必請注意，實驗室環境都會自行包含要在其中建立資源群組內。 這表示範本，以定義環境中，只能存取新建立的資源群組內的資源或[設定為可供實驗室的虛擬網路](devtest-lab-configure-vnet.md)。 上面這個範例會在相同的資源群組中建立所有必要的資源。

    ![建立叢集](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>啟動或停止叢集
您可以啟動或停止叢集從可能是研發/測試實驗室頁面本身或 DevTest Labs 所提供的 [Service Fabric 叢集] 頁面。 

### <a name="from-the-devtest-lab-page"></a>從 [研發/測試實驗室] 頁面
您可以啟動或停止在研發/測試實驗室設定 頁面上的叢集，您的實驗室。 

1. 選取**三個點 （...）** Service Fabric 叢集，如下圖所示： 

    ![啟動和停止叢集的命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 您會看到在內容功能表中的兩個命令**開始**並**停止**叢集。 Start 命令會啟動在叢集中所有節點。 [停止] 命令會停止在叢集中的所有節點。 叢集停止之後，Service Fabric 叢集本身仍處於就緒狀態，但沒有節點都可用，直到 start 命令會重新發出在實驗室中叢集上。

    有幾個測試環境中使用 Service Fabric 叢集時應注意的考量。 可能需要一些時間讓 Service Fabric 叢集，以完全解除凍結之後重新啟動節點。 若要保留啟動將關閉的資料，資料必須先儲存受控磁碟連接至虛擬機器上。 使用連結受控的磁碟，所以建議僅適用於測試環境時，則需要有造成不良效能影響。 如果用來儲存資料磁碟不受支援，然後資料會遺失在叢集上發出停止命令時。

### <a name="from-the-service-fabric-cluster-page"></a>從 Service Fabric 叢集 頁面 
還有另一種方式啟動或停止叢集。 

1. 在樹狀檢視中，在 DevTest 實驗室 頁面上，選取 Service Fabric 叢集。 

    ![選取您的叢集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 在  **Service Fabric 叢集**頁面叢集，您看到工具列啟動或停止叢集上的命令。 

    ![啟動或停止 Service Fabric 叢集 頁面中的命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>設定自動啟動及自動關機排程
Service Fabric 叢集也可以啟動或停止排程。 這項體驗是類似於在實驗室中的虛擬機器的體驗。 若要節省成本，根據預設，每個叢集在實驗室中建立的自動關閉在實驗室中定義的時間[關機原則](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown)。 指定是否應該關閉，叢集，或指定叢集已關閉的時間，您可以覆寫。 

![現有的排程，自動開始] 及 [自動關機](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>選擇要自動啟動排程
若要選擇啟動排程，執行下列步驟：

1. 選取 **自動啟動**左側功能表上
2. 選取 **上**for**允許這個可以排程為自動啟動的 service fabric 叢集**。 實驗室擁有者可以為自動啟動的使用者，他們的虛擬機器或 Service Fabric 叢集時，才啟用此頁面。
3. 在工具列上選取 [儲存]  。 

    ![自動星狀 頁面](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>設定自動關閉設定 
若要變更設定關機，請執行下列步驟：

1. 選取 **自動關機**左側功能表上。 
2. 在此頁面上，您可以選擇不自動關閉選取**關**如**已啟用**。 
3. 如果您已選取**上**for**已啟用**，請遵循下列步驟：
    1. 指定**時間**關機。
    2. 指定**時區**次。 
    3. 指定是否要傳送的 DevTest Labs**通知**之前自動關閉。 
    4. 如果您選取 **[是]** 通知選項時，指定**Webhook URL**和/或**電子郵件地址**來傳送通知。 
    5. 在工具列上選取 [儲存]  。

        ![自動關閉頁面](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>若要檢視 Service Fabric 叢集中的節點
稍早看到步驟中的 Service Fabric 叢集頁面旨在 DevTest Labs 頁面。 它並不會顯示節點叢集中。 若要查看叢集的詳細資訊，請遵循下列步驟：

1. 上**研發/測試實驗室**頁面上，為您實驗室中，選取**資源群組**中的樹狀檢閱中**我的虛擬機器**一節。

    ![選取資源群組](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 在 [**資源群組**] 頁面上，您會看到清單中的資源群組中的所有資源。 選取您**Service Fabric 叢集**從清單中。 

    ![在清單中選取您的叢集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 您會看到**Service Fabric 叢集**頁面為您的叢集。 這是 Service Fabric 提供的頁面。 您會看到所有的叢集節點、 節點型別等相關資訊。

    ![Service Fabric 叢集的首頁](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>後續步驟
請參閱下列文章中有關環境的詳細資料： 

- [透過 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定，並在 Azure DevTest Labs 中使用公用環境](devtest-lab-configure-use-public-environments.md)
