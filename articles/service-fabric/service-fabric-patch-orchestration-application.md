---
title: Azure Service Fabric 修補程式協調流程應用程式 | Microsoft Docs
description: 在 Service Fabric 叢集上將作業系統修補自動化的應用程式。
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289416"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>修補 Service Fabric 叢集中的 Windows 作業系統

> 
> [!IMPORTANT]
> 應用程式版本 1.2. * 將于2019年4月30日停止支援。 請升級至最新版本。


[Azure 虛擬機器擴展集的 OS 映像自動升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)是讓您的作業系統在 Azure 中修補的最佳做法，而修補程式協調流程應用程式 (POA) 則是 Service Fabrics Repair Manager Systems 服務相關的包裝函式，可針對非 Azure 託管的叢集，設定作業系統的修補排程。 非 Azure 託管的叢集不需要 POA，但需要排程升級網域安裝修補程式，才可以在不需要停機的情況下，修補 Service Fabric 叢集主機。

POA 是一種 Azure Service Fabric 應用程式，可在 Service Fabric 叢集上將作業系統修補自動化，而不需要停機。

修補程式協調流程應用程式提供下列功能：

- **自動化的作業系統更新安裝**。 會自動下載並安裝作業系統更新。 會視需要重新啟動叢集節點，而不需要叢集停機。

- **叢集感知的修補和健康情況整合**。 套用更新時，修補程式協調流程應用程式會監視叢集節點的健康情況。 叢集節點一次只能升級一個節點，或一次升級一個網域。 如果因修補程序而造成叢集的健康情況下降，修補就會停止，防止問題繼續惡化。

## <a name="internal-details-of-the-app"></a>應用程式的內部詳細資料

修補程式協調流程應用程式包含下列子元件︰

- **協調器服務**︰是具狀態服務，負責：
    - 協調整個叢集上的 Windows Update 作業。
    - 儲存已完成之 Windows Update 作業的結果。
- **節點代理程式服務**︰是無狀態服務，在所有 Service Fabric 叢集節點上執行。 此服務負責：
    - 啟動節點代理程式 NTService。
    - 監視節點代理程式 NTService。
- **節點代理程式 NTService**：在較高層級權限 (系統) 執行的 Windows NT 服務。 相比之下，節點代理程式服務和協調器服務則是在較低層級權限 (網路服務) 執行。 此服務會負責執行下列所有叢集節點上的 Windows Update 作業：
    - 將節點上的自動 Windows Update 停用。
    - 根據使用者提供的原則下載並安裝 Windows Update。
    - 在 Windows Update 安裝後重新啟動機器。
    - 將 Windows Update 的結果上傳至協調器服務。
    - 萬一耗盡所有重試之後作業還是失敗，就報告健康情況報告。

> [!NOTE]
> 修補程式協調流程應用程式是使用 Service Fabric 的修復管理器系統服務，將節點停用或啟用以及執行健康情況檢查。 修補程式協調流程應用程式所建立的修復工作會追蹤每個節點的 Windows Update 進度。

## <a name="prerequisites"></a>必要條件

> [!NOTE]
> 所需的最低 .NET framework 版本為4.6。

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>啟用修復管理器服務 (如果尚未執行中)

叢集必須啟用修復管理器系統服務，修補程式協調流程應用程式才能執行。

#### <a name="azure-clusters"></a>Azure 叢集

銀級耐久性層中的 Azure 叢集依預設會啟用修復管理器。 金級耐久性層中的 Azure 叢集可能會或也可能不會啟用修復管理器，取決於這些叢集的建立時間。 銅級耐久性層中的 Azure 叢集依預設不會啟用修復管理器服務。 如果已啟用服務，可以在 Service Fabric Explorer 的系統服務區段中看到它正在執行。

##### <a name="azure-portal"></a>Azure 入口網站
您可以在設定叢集時從 Azure 入口網站啟用修復管理員。 設定叢集時，選取 [附加元件功能] 底下的 [包含修復管理員] 選項。
![從 Azure 入口網站啟用修復管理員的映像](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager 部署模型
或者，您可以使用 [Azure Resource Manager 部署模型](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)，在新的和現有的 Service Fabric 叢集上啟用修復管理員服務。 取得您想要部署之叢集的範本。 您可以使用範例範本，或建立自訂的 Azure Resource Manager 部署模型範本。 

若要使用 [Azure Resource Manager 部署模型範本](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)啟用修復管理員服務：

1. 首先，確認 `Microsoft.ServiceFabric/clusters` 資源的 `apiversion` 已設為 `2017-07-01-preview`。 如果不是，您就需要將 `apiVersion` 更新為 `2017-07-01-preview` 值或更高：

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 立即啟用修復管理器服務，方法是在 `fabricSettings` 區段之後新增下列 `addonFeatures` 區段：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 一旦您使用這些變更將叢集範本進行更新之後，將它們加以套用，使升級完成。 您現在可以看到修復管理器系統服務在您的叢集中執行。 它在 Service Fabric Explorer 的系統服務區段中叫作 `fabric:/System/RepairManagerService`。 

### <a name="standalone-on-premises-clusters"></a>獨立的內部部署叢集

您可以使用[獨立 Windows 叢集的組態設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)在新的和現有的 Service Fabric 叢集上啟用修復管理器。

啟用修復管理器服務：

1. 首先，檢查[一般叢集設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)中的 `apiversion` 是否已設定為 `04-2017` 或更高版本：

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. 立即啟用修復管理器服務，方法是在 `fabricSettings` 區段之後新增下列 `addonFeatures` 區段，如下所示：

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 以這些變更更新您的叢集資訊清單，使用更新後的叢集資訊清單[建立新叢集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server)或[升級叢集設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)。 叢集開始以更新的資訊清單執行後，您現在可以在 Service Fabric Explorer 的系統服務區段下，看到修復管理器系統服務 (亦稱為 `fabric:/System/RepairManagerService`) 在叢集中執行。

### <a name="configure-windows-updates-for-all-nodes"></a>針對所有節點設定 Windows 更新

Windows 自動更新可能導致可用性遺失，因為可以在同一個時間重新啟動多個叢集節點。 依預設，修補程式協調流程應用程式會在每個叢集節點上嘗試將自動 Windows Update 停用。 不過，如果設定會由系統管理員或群組原則所管理，則建議將 Windows Update 原則明確地設定為「下載前先通知」。

## <a name="download-the-app-package"></a>下載安裝套件

若要下載應用程式套件，請流覽修補程式協調流程應用程式的 GitHub 版本[頁面](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)。

## <a name="configure-the-app"></a>設定應用程式

您可以設定修補程式協調流程應用程式的行為以符合您的需求。 在應用程式建立或更新期間傳入應用程式參數，將預設值加以覆寫。 在 `Start-ServiceFabricApplicationUpgrade` 或 `New-ServiceFabricApplication` Cmdlet 中指定 `ApplicationParameter`，即可提供應用程式參數。

|**參數**        |**型別**                          | **詳細資料**|
|:-|-|-|
|MaxResultsToCache    |long                              | Windows Update 結果的最大數目，應加以快取。 <br>預設值為 3000，是基於以下假設： <br> - 節點數目 20。 <br> - 每個月在節點上發生的更新數目為 5。 <br> - 每個作業的結果數目可為 10。 <br> - 過去 3 個月的結果皆加以儲存。 |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy 會指出協調器服務在 Service Fabric 叢集節點中用來安裝 Windows 更新的原則。<br>                         允許的值包括： <br>                                                           <b>NodeWise</b>。 一次只會在一個節點上安裝 Windows Update。 <br>                                                           <b>UpgradeDomainWise</b>。 一次只會在一個升級網域上安裝 Windows Update。 (最多，屬於升級網域的所有節點都可以進行 Windows Update。)<br> 請參閱[常見問題集](#frequently-asked-questions)一節，以了解如何決定最適合您叢集的原則。
|LogsDiskQuotaInMB   |長  <br> (預設值：1024)               |修補程式協調流程應用程式記錄的大小上限 (以 MB 為單位)，可在節點上本機保留。
| WUQuery               | string<br>(預設值："IsInstalled=0")                | 用以取得 Windows 更新的查詢。 如需詳細資訊，請參閱 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
| InstallWindowsOSOnlyUpdates | Boolean <br> (預設值：False)                 | 使用此旗標可控制所應下載並安裝的更新。 允許下列值 <br>true - 只安裝 Windows 作業系統的更新。<br>false - 在電腦上安裝所有可用的更新。          |
| WUOperationTimeOutInMinutes | Int <br>(預設值：90)                   | 指定任何 Windows Update 作業的逾時 (搜尋或下載或安裝)。 如果作業未在指定的逾時內完成，它就會中止。       |
| WURescheduleCount     | Int <br> (預設值：5)                  | 如果作業持續失敗，服務會將 Windows Update 重新排程的次數上限。          |
| WURescheduleTimeInMinutes | Int <br>(預設值：30) | 如果作業持續失敗，服務會將 Windows Update 重新排程的時間間隔。 |
| WUFrequency           | 以逗號分隔的字串 (預設值︰"Weekly, Wednesday, 7:00:00")     | 安裝 Windows Update 的頻率。 格式與可能的值如下： <br>-   Monthly, DD, HH:MM:SS，例如 Monthly, 5,12:22:32。<br>欄位 DD (天) 所允許的值為 1 到 28 範圍內的數字和「最後」。 <br> -   Weekly, DAY, HH:MM:SS，例如 Weekly, Tuesday, 12:22:32。  <br> -   Daily, HH:MM:SS，例如 Daily, 12:22:32。  <br> -  None 表示不應該進行 Windows Update。  <br><br> 請注意，時間會採用 UTC 格式。|
| AcceptWindowsUpdateEula | Boolean <br>(預設值︰true) | 藉由設定這個旗標，應用程式會代表電腦的擁有者接受 Windows Update 的使用者授權合約 (EULA)。              |

> [!TIP]
> 如果需要 Windows Update 立即發生，請將 `WUFrequency` 設定為相對於應用程式部署的時間。 例如，假設您的測試叢集有五個節點，計劃於大約下午 5:00 UTC 部署應用程式。 如果您假設應用程式的升級或部署最多會花費 30 分鐘的時間，則將 WUFrequency 設定為 "Daily, 17:30:00"

## <a name="deploy-the-app"></a>部署應用程式

1. 完成準備叢集的所有必要條件步驟。
2. 部署修補程式協調流程應用程式，和任何其他 Service Fabric 應用程式一樣。 您可以使用 PowerShell 部署此應用程式。 請遵循[使用 PowerShell 部署與移除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的步驟。
3. 若要在部署時設定應用程式，可將 `ApplicationParameter` 傳遞給 `New-ServiceFabricApplication` Cmdlet。 為了您的方便，我們提供了 Deploy.ps1 指令碼以及我們的應用程式。 若要使用指令碼：

    - 使用 `Connect-ServiceFabricCluster` 連線至 Service Fabric 叢集。
    - 利用適當的 `ApplicationParameter` 值執行 Powershell 指令碼 Deploy.ps1。

> [!NOTE]
> 將指令碼和應用程式資料夾 PatchOrchestrationApplication 保存在同一個目錄中。

## <a name="upgrade-the-app"></a>升級應用程式

若要使用 PowerShell 將現有的修補程式協調流程應用程式進行升級，請遵循[使用 PowerShell 升級 Service Fabric應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)中的步驟。

## <a name="remove-the-app"></a>移除應用程式

若要移除應用程式，請遵循[使用 PowerShell 部署與移除應用程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)中的步驟。

為了您的方便，我們提供了 Undeploy.ps1 指令碼以及我們的應用程式。 若要使用指令碼：

  - 使用 ```Connect-ServiceFabricCluster``` 連線至 Service Fabric 叢集。

  - 執行 Powershell 指令碼 Undeploy.ps1。

> [!NOTE]
> 將指令碼和應用程式資料夾 PatchOrchestrationApplication 保存在同一個目錄中。

## <a name="view-the-windows-update-results"></a>檢視 Windows Update 結果

修補程式協調流程應用程式會公開 REST API，以向使用者顯示歷程記錄的結果。 JSON 結果的範例：
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

以下描述 JSON 的欄位。

欄位 | 值 | 詳細資料
-- | -- | --
OperationResult | 0 - 成功<br> 1 - 成功但發生錯誤<br> 2 - 失敗<br> 3 - 已中止<br> 4 - 中止但逾時 | 指出整體作業 (通常牽涉到安裝一或多個更新) 的結果。
ResultCode | 與 OperationResult 相同 | 此欄位指出個別更新安裝作業的結果。
OperationType | 1 - 安裝<br> 0 - 搜尋和下載。| 「安裝」是依預設會顯示在結果中的唯一一個 OperationType。
WindowsUpdateQuery | 預設值為 "IsInstalled=0" |用來搜尋更新的 Windows Update 查詢。 如需詳細資訊，請參閱 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)。
RebootRequired | true - 需要重新開機<br> false - 不需要重新開機 | 指出完成更新的安裝是否需要重新開機。
OperationStartTime | DateTime | 表示作業（下載/安裝）開始的時間。
OperationTime | DateTime | 表示作業（下載/安裝）完成的時間。
HResult | 0-成功<br> 其他-失敗| 指出具有 updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6" 的 windows update 失敗原因。

如果尚未排程更新，JSON 結果會是空的。

登入叢集以查詢 Windows Update 結果。 接著，找出主要協調器服務的複本位址，然後點閱瀏覽器的 URL： http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults。

協調器服務的 REST 端點具有動態連接埠。 若要知道確切 URL，請查看 Service Fabric Explorer。 例如，可在 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` 找到結果。

![REST 端點的圖片](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


如果在叢集上啟用反向 Proxy，您也可以從叢集外部存取 URL。
需要點閱的端點為 http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults。

若要啟用叢集的反向 Proxy，請遵循 [Azure Service Fabric 中的反向 Proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) 中的步驟。 

> 
> [!WARNING]
> 設定反向 Proxy 之後，叢集中公開 HTTP 端點的所有微服務就能從叢集外部尋址。

## <a name="diagnosticshealth-events"></a>診斷/健康情況事件

下一節將討論如何透過 Service Fabric 叢集上的修補程式協調流程應用程式，在修補程式更新中進行檢查或診斷問題。

> [!NOTE]
> 您應該已安裝 v 1.4.0 版本的 POA，以取得下列許多稱為「自我診斷」改良功能。

NodeAgentNTService 會建立[修復](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet)工作，以在節點上安裝更新。 然後，每個工作都會根據工作核准原則進行 CoordinatorService 準備。 已準備好的工作最後會由修復管理員核准，如果叢集處於狀況不良狀態，則不會核准任何工作。 讓我們逐步瞭解如何在節點上進行更新。

1. 在每個節點上執行的 NodeAgentNTService，會在排程的時間尋找可用的 Windows Update。 如果有可用的更新，就會將它們下載到節點上。
2. 下載更新之後，NodeAgentNTService 會為名稱為 POS___ < unique_id > 的節點建立對應的修復工作。 您可以使用 Cmdlet [ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)或 [節點詳細資料] 區段中的 [SFX]，來查看這些修復工作。 一旦建立修復工作，很快就會移至[宣告的狀態](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)。
3. 協調器服務會定期尋找已宣告狀態的修復工作，並進行更新以根據 Taskapprovalpolicy 會準備狀態。 如果 Taskapprovalpolicy 會設定為 NodeWise，則只有當目前沒有任何其他修復工作正在準備/核准/執行/還原狀態時，才會備妥對應至節點的修復工作。 同樣地，在 UpgradeWise Taskapprovalpolicy 會的情況下，在上述狀態中的工作，只有屬於相同升級網域的節點，才會加以確保。 一旦將修復工作移至準備狀態，對應的 Service Fabric 節點就會[停](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps)用意圖為「重新開機」。

   POA （v 1.4.0 和更新版本）會在 CoordinaterService 上張貼屬性為 "ClusterPatchingStatus" 的事件，以顯示正在修補的節點。 下圖顯示在 _poanode_0 上安裝更新：

    [![叢集修補狀態的影像](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. 停用節點後，修復工作會移至 [執行中] 狀態。
   
   >[!NOTE]
   > 卡在停用狀態的節點可以封鎖新的修復工作，這會停止叢集上的修補作業。

5. 一旦 [修復] 工作處於 [執行中] 狀態，該節點上的修補程式安裝就會開始。 在這裡，安裝修補程式之後，節點不一定會重新開機，視修補程式而定。 將修復工作移至還原狀態的 Post，這會再次啟用節點，然後將其標示為已完成。

   在1.4.0 和更新版本的應用程式中，您可以藉由查看 NodeAgentService 上的健康狀態事件與屬性 "WUOperationStatus-[NodeName]" 來找到更新的狀態。 下圖中反白顯示的區段會顯示節點 ' poanode_0 ' 和 ' poanode_2 ' 上 windows update 的狀態：

   [![Windows update 作業狀態的影像](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Windows update 作業狀態的影像](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   您也可以使用 powershell 來取得詳細資料，方法是連接到叢集，然後使用[ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)來提取修復工作的狀態。 如以下範例所示，"POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" 工作處於 DownloadComplete 狀態。 這表示更新已下載到「poanode_2」節點上，而當工作移至執行狀態時，將會嘗試安裝。

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   如果找不到其他資訊，請登入特定 VM/Vm，以使用 Windows 事件記錄檔來尋找更多有關此問題的資訊。 上述的修復工作只能具有下列執行程式子狀態：

      ExecutorSubState | 詳細資料
    -- | -- 
      None=1 |  表示節點上沒有進行中的作業。 可能的狀態轉換。
      DownloadCompleted=2 | 表示下載作業已完成，但發生「成功」、「部分失敗」或「失敗」。
      InstallationApproved=3 | 表示下載作業已稍早完成，修復管理員已核准安裝。
      InstallationInProgress=4 | 對應至執行修復工作的狀態。
      InstallationCompleted=5 | 表示安裝已完成，但成功、部分成功或失敗。
      RestartRequested=6 | 表示修補程式安裝已完成，且節點上有暫止的重新開機動作。
      RestartNotNeeded = 7 |  表示在完成修補程式安裝之後，不需要重新開機。
      RestartCompleted=8 | 表示重新開機已順利完成。
      OperationCompleted=9 | Windows update 操作已順利完成。
      OperationAborted=10 | 表示 windows update 作業已中止。

6. 在應用程式的 v 1.4.0 和更新版本中，當節點上的 update 嘗試完成時，會在 NodeAgentService 上張貼屬性為 "WUOperationStatus-[NodeName]" 的事件，以在下次嘗試時通知，下載並安裝 update，啟動。 請參閱下圖：

     [![Windows update 作業狀態的影像](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>診斷記錄

收集修補程式協調流程應用程式的記錄，是 Service Fabric 執行階段記錄的一部分。

以免您想要透過您選擇的診斷工具/管線擷取記錄。 修補程式協調流程應用程式使用以下的固定提供者識別碼，透過[事件來源](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)記錄事件

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>健康狀態報告

修補程式協調流程應用程式在下列情況下也會發佈協調器服務或節點代理程式服務的健康情況報告：

#### <a name="the-node-agent-ntservice-is-down"></a>節點代理程式 NTService 關閉

如果節點上的節點代理程式 NTService 關閉，就會產生節點代理程式服務的警告等級健康情況報告。

#### <a name="the-repair-manager-service-is-not-enabled"></a>修復管理器服務未啟用

如果在叢集上找不到修復管理器服務，就會產生協調器服務的警告等級健康情況報告。

## <a name="frequently-asked-questions"></a>常見問題集

問： **當修補程式協調流程應用程式執行時，為什麼我看到叢集處於錯誤狀態？**

A. 在安裝程序期間，修補程式協調流程應用程式會停用或重新啟動節點，而這可能會導致叢集暫時關閉的健康情況。

根據應用程式的原則，可能是一個節點在修補作業期間關閉，「或是」整個升級網域同時關閉。

Windows Update 安裝結束時，在重新啟動前會重新啟用節點。

在下列範例中，因為兩個節點關閉，且違反 MaxPercentageUnhealthyNodes 原則，叢集會暫時進入錯誤狀態。 錯誤是暫時性的，直到修補作業進行中為止。

![健康情況不良之叢集的圖片](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

如果問題持續發生，請參閱〈疑難排解〉一節。

問： **修補程式協調流程應用程式處於警告狀態**

A. 檢查針對應用程式所公佈的健康情況報告，是否為根本原因。 警告通常包含問題的詳細資料。 如果是暫時性的問題，應用程式預期會從這個狀態中自動復原。

問： **如果我的叢集健康情況不良，我可以做什麼？我需要採取緊急作業系統更新嗎？**

A. 當叢集健康情況不良時，修補程式協調流程應用程式就不會安裝更新。 請嘗試使叢集處於良好的健康情況，以便將修補程式協調流程應用程式工作流程解除封鎖。

問： **我應該將叢集的 Taskapprovalpolicy 會設定為 ' NodeWise ' 或 ' UpgradeDomainWise ' 嗎？**

A. 'UpgradeDomainWise' 可以藉由平行修補屬於升級網域的所有節點，來加快整體叢集修補。 這表示屬於整個升級網域的節點在修補程序期間都無法使用 (處於[已停用](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)狀態)。

相較之下，'NodeWise' 原則一次只會修補一個節點，這也暗示著整體叢集修補需要較長的時間。 不過，在修補程序期間頂多只有一個節點無法使用 (處於[已停用](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)狀態)。

如果您的叢集可以容忍修補週期期間在 N-1 個升級網域上執行 (其中 N 是叢集上升級網域的總數)，則您可以將原則設為 'UpgradeDomainWise'，否則請將其設為 'NodeWise'。

問： **修補節點需要耗費多久時間？**

A. 修補一個節點可能需要幾分鐘 (例如：[Windows Defender 定義更新](https://www.microsoft.com/en-us/wdsi/definitions)) 到數小時 (例如：[Windows 累積更新](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)) 的時間。 修補節點所需的時間大多取決於下列因素： 
 - 更新的大小
 - 修補期間所需要套用的更新數目
 - 安裝更新、重新啟動節點 (如有必要) 然後完成重新啟動後安裝步驟所耗費的時間。
 - 虛擬機器/電腦和網路狀況的效能。

問： **修補整個叢集需要多久時間？**

A. 修補整個叢集所需的時間取決於下列因素：

- 修補一個節點所需的時間。
- 協調器服務的原則。 - 預設原則 `NodeWise` 一次只會修補一個節點，這樣比 `UpgradeDomainWise` 慢。 例如: 如果節點需要約 1 個小時來修補，若要修補 20 個節點 (節點類型相同) 的叢集，其中具有 5 個升級網域，每個網域包含 4 個節點。
    - 如果原則是 `NodeWise`，則需要約 20 個小時來修補整個叢集
    - 如果原則是 `UpgradeDomainWise`，則需要約 5 個小時
- 叢集負載 - 每個修補作業都必須將客戶工作負載重新放置到叢集中的其他可用節點。 正在進行修補的節點屆時會處於[停用](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling)狀態。 如果正在執行的叢集接近尖峰負載，則停用程序需要更長的時間。 因此，在如此充滿壓力的條件下，整體修補程序可能會變慢。
- 修補期間叢集健康情況失敗 - [叢集健康情況](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)如果[降低](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error)，就會中斷修補程序。 這樣會增加修補整個叢集所需的整體時間。

問： **為什麼我在 Windows Update 結果中看到某些更新已透過 REST API 取得，但未出現在電腦的 Windows Update 歷程記錄下？**

A. 某些產品更新只會出現在其各自的更新/修補歷程記錄中。 例如，Windows Defender 更新可能不會顯示在 Windows Server 2016 上的 Windows Update 歷程記錄中。

問： **「修補協調流程」應用程式可用來更新我的開發叢集 (單一節點叢集) 嗎？**

A. 否，修補協調流程應用程式無法用來修補單一節點的叢集。 此限制的設計：因為 [Service Fabric 系統服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services)或任何客戶應用程式將會面臨停機時間，所以修復管理員決不會核准以任何修復作業進行修補。

問： **在 Linux 上如何? 修補叢集節點？**

A. 請參閱[Azure 虛擬機器擴展集的自動 OS 映射升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)，以在 linux 上協調更新。

問：**更新週期為何要花很長的時間？**

A. 查詢結果 json，然後逐一查看所有節點的更新週期專案，然後您可以嘗試使用 OperationStartTime 和 OperationTime （OperationCompletionTime）來找出每個節點上的更新安裝所花費的時間。 如果沒有任何更新正在進行中的時間範圍很長，可能是因為叢集處於錯誤狀態，而且該修復管理員未核准任何其他 POA 修復工作。 如果在任何節點上安裝更新的時間很長，則可能是因為節點不會從長時間更新，而且有許多更新擱置安裝，這需要花費時間。 可能也會因為節點停滯停用狀態而封鎖節點上的修補，這通常是因為停用節點可能會導致仲裁/資料遺失的情況發生。

問： **為什麼當 POA 正在進行修補時，必須停用節點？**

A. 修補程式協調流程應用程式會停用具有「重新開機」意圖的節點，以停止/重新配置節點上執行的所有 Service fabric 服務。 這麼做是為了確保應用程式不會使用新的和舊的 dll 混合，因此不建議在不停用的情況下修補節點。

## <a name="disclaimers"></a>免責聲明

- 修補程式協調流程應用程式會代表使用者接受 Windows Update 的使用者授權合約 (EULA)。 可在應用程式的設定中選擇性地將此設定關閉。

- 修補程式協調流程應用程式會收集遙測來追蹤使用情形和效能。 應用程式的遙測會遵循 Service Fabric 執行階段遙測設定 (預設為開啟) 的設定。

## <a name="troubleshooting"></a>疑難排解

### <a name="a-node-is-not-coming-back-to-up-state"></a>節點不會回到開啟狀態

**節點可能會卡在停用中狀態，因為**：

安全性檢查擱置。 若要補救這種情況，請確定有足夠多健康情況良好的節點。

**節點可能會卡在已停用狀態，因為**：

- 已將節點手動停用。
- 因為 Azure 基礎結構作業正在進行中而導致節點停用。
- 修補程式協調流程應用程式已暫時將節點停用，以修補節點。

**節點可能會卡在關閉狀態，因為**：

- 已手動將節點置於關閉狀態。
- 節點正在重新啟動 (可能是由修補程式協調流程應用程式觸發)。
- 由於 VM 或機器故障或網路連線問題，使節點關閉。

### <a name="updates-were-skipped-on-some-nodes"></a>已略過某些節點上的更新

修補程式協調流程應用程式會根據重新排定的原則，嘗試安裝 Windows Update。 服務會根據應用程式原則，嘗試復原節點並略過更新。

在這種情況下，系統會針對節點代理程式服務產生警告等級的健康情況報告。 Windows Update 的結果也包含可能的失敗原因。

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>開始安裝更新時，叢集的健康情況出現錯誤

錯誤的 Windows Update 會損及特定節點或升級網域上應用程式或叢集的健康情況。 修補程式協調流程應用程式會中止所有後續的 Windows Update作業，直到叢集恢復良好健康情況。

系統管理員必須介入，判斷應用程式或叢集為何因為 Windows Update 變成健康情況不良。

## <a name="release-notes"></a>版本資訊

>[!NOTE]
> 從版本1.4.0 開始，您可以在 GitHub 版本[頁面](https://github.com/microsoft/Service-Fabric-POA/releases/)上找到版本資訊和版本。

### <a name="version-110"></a>1\.1.0 版
- 公開版本

### <a name="version-111"></a>1\.1.1 版
- 修正 SetupEntryPoint of NodeAgentService 中的錯誤，該錯誤導致無法安裝 NodeAgentNTService。

### <a name="version-120"></a>版本 1.2.0

- 關於系統重新啟動工作流程的錯誤修正。
- 由於準備修復工作期間健康情況檢查未如預期般發生，而在 RM 工作建立時進行的錯誤修正。
- 將 Windows 服務 POANodeSvc 的啟動模式從自動變更為延遲自動。

### <a name="version-121"></a>1\.2.1 版

- 縮小叢集範圍工作流程中的錯誤修正。 針對屬於不存在節點的 POA 修復工作，導入了記憶體回收邏輯。

### <a name="version-122"></a>1\.2.2 版

- 其他錯誤修正。
- 現在已簽署二進位檔。
- 已新增應用程式的 sfpkg 連結。

### <a name="version-130"></a>版本 1.3.0

- 現在，將 InstallWindowsOSOnlyUpdates 設定為 false 便會安裝所有可用的更新。
- 已變更停用自動更新的邏輯。 這會修正 Server 2016 和以上版本未停用自動更新的錯誤。
- POA 微服務的參數化放置條件約束，適用于 advanced 使用案例。

### <a name="version-131"></a>1\.3.1 版
- 修正 POA 1.3.0 在 Windows Server 2012 R2 或較低版本上因為停用自動更新失敗而無法運作的迴歸。 
- 修正 InstallWindowsOSOnlyUpdates 組態一律挑選為 True 的錯誤 (bug)。
- 將 InstallWindowsOSOnlyUpdates 的預設值變更為 False。

### <a name="version-132"></a>1\.3.2 版
- 修正當節點具有名稱（其為目前節點名稱的子集）時，可能會影響節點上修補生命週期的問題。 對於這類節點，很可能會遺漏修補或者會擱置重新開機。 
