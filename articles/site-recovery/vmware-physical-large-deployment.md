---
title: 設定要使用 Azure Site Recovery 針對 VMware Vm 或實體伺服器的大型數字 Azure 的災害復原 |Microsoft Docs
description: 了解如何設定大量內部部署 VMware Vm 或實體伺服器使用 Azure Site Recovery 的 azure 的災害復原。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237271"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>設定 VMware Vm/實體伺服器為大規模的災害復原

這篇文章說明如何設定 azure 的災害復原的數目很大 (> 1000) 在內部部署 VMware Vm 或實體伺服器，在生產環境中，使用[Azure Site Recovery](site-recovery-overview.md)服務。


## <a name="define-your-bcdr-strategy"></a>定義您的 BCDR 策略

您的業務續航力和災害復原 (BCDR) 策略的一部分，您會定義復原點目標 (Rpo) 和您的企業營運應用程式和工作負載的復原時間目標 (Rto)。 RTO 可測量的時間和服務的持續時間內的層級的商務應用程式或處理序必須還原並可用，以避免持續性問題。
- Site Recovery 提供了連續複寫 VMware Vm 和實體伺服器，以及[SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)的 RTO。
- 當您針對 VMware Vm 和找出所需的 Azure 資源大規模災害復原計劃，您可以指定將用於產能計算的 RTO 值。


## <a name="best-practices"></a>最佳作法

大規模的災害復原的一些一般最佳作法。 在文件的下一節中詳細討論這些最佳作法。

- **識別目標需求**:估計出在 Azure 中的容量和資源需求，然後再設定災害復原。
- **規劃部署 Site Recovery 元件**:找出您需要以符合您的預估的容量的 Site Recovery 元件 （組態伺服器、 處理序伺服器）。
- **設定一或多個向外延展處理序伺服器**:請勿使用並預設在組態伺服器執行的處理序伺服器。 
- **執行最新的更新**:Site Recovery 小組發行新版本的定期執行，Site Recovery 元件，您應該要確定您正在執行的最新版本。 若要幫助，追蹤[最新消息](site-recovery-whats-new.md)更新，並[啟用和安裝更新](service-updates-how-to.md)發行。
- **主動監視**:啟動並執行，您會收到災害復原，您應該主動監視的狀態和複寫的機器和基礎結構資源的健全狀況。
- **災害復原演練，又**:您應該定期執行災害復原演練。 這些不會影響生產環境，但有助於確保容錯移轉至 Azure 將會正常運作需要時。



## <a name="gather-capacity-planning-information"></a>收集容量規劃資訊

收集您的內部部署環境，以協助評估，並且估計您目標 (Azure) 的容量需求的相關資訊。
- 適用於 VMware，執行部署規劃工具，適用於 VMware Vm，若要這樣做。
- 針對實體伺服器，請以手動方式收集的資訊。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>執行部署規劃工具，適用於 VMware Vm

部署規劃工具可協助您收集 VMware 在內部部署環境的相關資訊。

- 執行部署規劃工具期間，表示一般的變換為您的 Vm。 這會產生更精確的預估和建議。
- 我們建議您在組態伺服器機器上執行部署規劃工具，因為規劃工具會計算從伺服器執行所在的輸送量。 [了解更多](site-recovery-vmware-deployment-planner-run.md#get-throughput)有關測量輸送量。
- 如果您還沒有組態伺服器設定：
    - [取得概觀](vmware-physical-azure-config-process-server-overview.md)Site Recovery 元件。
    - [設定組態伺服器](vmware-azure-deploy-configuration-server.md)，以便在其上執行部署規劃工具。

然後執行規劃工具，如下所示：

1. [深入了解](site-recovery-deployment-planner.md)Deployment Planner。 您可以從入口網站中，下載最新版本或[直接下載](https://aka.ms/asr-deployment-planner)。
2. 檢閱[必要條件](site-recovery-deployment-planner.md#prerequisites)並[最新的更新](site-recovery-deployment-planner-history.md)針對部署規劃，並[下載並解壓縮](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)工具。
3. [執行部署規劃工具](site-recovery-vmware-deployment-planner-run.md)組態伺服器上。
4. [產生報告](site-recovery-vmware-deployment-planner-run.md#generate-report)，提供預估和建議摘要。
5. 分析[報告的建議](site-recovery-vmware-deployment-planner-analyze-report.md)並[成本估計](site-recovery-vmware-deployment-planner-cost-estimation.md)。

>[!NOTE]
> 根據預設，此工具會設定為設定檔，並會產生報表的最多 1000 個 Vm。 您可以藉由增加的 MaxVMsSupported 索引鍵值 ASRDeploymentPlanner.exe.config 檔案中變更這項限制。

## <a name="plan-target-azure-requirements-and-capacity"></a>規劃的目標 (Azure) 需求和容量

使用收集到的預估和建議，您可以規劃的目標資源和容量。 如果您執行部署規劃工具，適用於 VMware Vm，您可以使用許多[報告的建議](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)可協助您。

- **相容的 Vm**:您可以使用此號碼來識別已準備好進行災害復原至 Azure 的 Vm 數目。 有關網路頻寬和 Azure 核心的建議根據這個數字。
- **所需的網路頻寬**:請注意相容 Vm 的差異複寫所需的頻寬。 
    - 當執行規劃工具會以分鐘為單位指定所需的 RPO。 這些建議會顯示以符合當時的 RPO 100%，90%的時間所需的頻寬。 
    - 網路頻寬建議會考慮到組態伺服器和 Planner 中建議的處理序伺服器總數所需的頻寬。
- **需要 Azure 核心**:請注意您必須在目標 Azure 區域中的核心數目取決於相容的 Vm 數目。 如果您沒有足夠的核心，在容錯移轉 Site Recovery 無法建立必要的 Azure Vm。
- **建議的 VM 批次大小**:建議的批次大小根據能夠完成初始複寫批次，在 72 小時內依預設，同時可以滿足的 100 %rpo。 可以修改的小時值。

您可以使用這些建議來規劃 Azure 資源、 網路頻寬，以及 VM 批次。

## <a name="plan-azure-subscriptions-and-quotas"></a>規劃 Azure 訂用帳戶和配額

我們想要確定目標訂用帳戶中可用的配額不足以處理容錯移轉。

**Task** | **詳細資料** | **動作**
--- | --- | ---
**核取核心** | 如果可用的配額中的核心不等於或超過總目標計數，在容錯移轉時，容錯移轉將會失敗。 | 針對 VMware Vm，請檢查目標訂用帳戶，以符合部署規劃工具的核心建議事項中有足夠的核心。<br/><br/> 針對實體伺服器，請檢查 Azure 核心，符合您手動的估計。<br/><br/> 若要檢查配額，在 Azure 入口網站 >**訂用帳戶**，按一下**使用量 + 配額**。<br/><br/> [了解更多](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)需增加配額。
**查看容錯移轉限制** | 容錯移轉數不得超過 Site Recovery 容錯移轉的限制。 |  如果容錯移轉超過限制，您可以新增訂用帳戶，並容錯移轉至多個訂用帳戶，或增加訂用帳戶的配額。 


### <a name="failover-limits"></a>容錯移轉限制

限制表示容錯移轉受到 Site Recovery 在一小時，假設每部電腦的三個磁碟的數目。

什麼會符合標準？ 若要啟動 Azure VM，Azure 需要處於開機開始，有些驅動程式和服務，例如 DHCP 設為自動啟動。
- 符合的機器將會已備妥這些設定。
- 對於執行 Windows 機器，您可以主動檢查合規性，並使其符合規範，如有需要。 [深入了解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- Linux 機器只會納入在容錯移轉期間的合規性。

**電腦符合 < 使用 Azure 嗎？** | **Azure VM 的限制 （受控的磁碟容錯移轉）**
--- | --- 
是 | 2000
否 | 1000

- 限制假設，最低的其他作業正在進行的訂用帳戶的目標區域中。
- 某些 Azure 區域比較小，並可能會有稍微較低的限制。

## <a name="plan-infrastructure-and-vm-connectivity"></a>規劃基礎結構和 VM 連線

容錯移轉至 Azure 之後，您會需要您的工作負載運作如同在內部，並讓使用者能夠存取 Azure Vm 上執行的工作負載。

- [了解更多](site-recovery-active-directory.md#test-failover-considerations)即將容錯移轉至 Azure 您 Active Directory 或 DNS 在內部部署基礎結構。
- [了解更多](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)有關準備在容錯移轉後連接到 Azure Vm。



## <a name="plan-for-source-capacity-and-requirements"></a>如需來源容量和需求的計劃

請務必您有足夠的組態伺服器和向外延展處理序伺服器，以滿足容量需求。 當您開始您的大規模部署時，開始單一組態伺服器和單一的向外延展處理序伺服器。 在您達到指定的限制，加入額外的伺服器。

>[!NOTE]
> 針對 VMware Vm，部署規劃工具可讓您需要的組態和處理序伺服器相關的一些建議。 我們建議您使用下列程序，而不是依照部署規劃工具建議中包含的資料表。 


## <a name="set-up-a-configuration-server"></a>設定組態伺服器
 
組態伺服器的容量會受到複寫的機器數目，並不是由資料變換率。 若要找出您是否需要額外的組態伺服器，請使用這些定義 VM 的限制。

**CPU** | **記憶體** | **快取磁碟** | **複寫機器限制**
 --- | --- | --- | ---
8 個 vCPU<br> 2 個通訊端 * 4 核心 @ 2.5ghz | 16 GB | 600 TB | 最多 550 部機器<br> 假設每一部機器，有三個 100 gb 的磁碟。

- 這些限制根據使用 OVF 範本設定組態伺服器。
- 限制會假設您不使用預設組態伺服器執行的處理序伺服器。

如果您要新增新的組態伺服器，請遵循下列指示：

- [設定組態伺服器](vmware-azure-deploy-configuration-server.md)針對 VMware VM 災害復原，使用 OVF 範本。
- [設定組態伺服器](physical-azure-set-up-source.md)手動針對實體伺服器，或無法使用 OVF 範本的 VMware 部署。

當您設定組態伺服器時，注意下列事項︰

- 當您設定組態伺服器時，務必考慮的訂用帳戶和保存庫所在，因為這些不應該在安裝之後變更。 如果您需要變更保存庫，您必須取消關聯組態伺服器從保存庫，並重新註冊它。 這會停止 Vm 的保存庫中的複寫。
- 如果您想要設定組態伺服器與多個網路介面卡，您應該這麼設定期間。 您無法在保存庫註冊組態伺服器之後來這麼做。

## <a name="set-up-a-process-server"></a>設定處理序伺服器

處理序伺服器的容量會受到影響的資料變換率，而不是由啟用複寫的機器數目。

- 針對大型部署，您應該永遠有至少一個向外延展處理序伺服器。
- 若要了解您是否需要額外的伺服器，請使用下表。
- 我們建議您新增具有最高規格的伺服器。 


**CPU** | **記憶體** | **快取磁碟** | **變換率**
 --- | --- | --- | --- 
12 個 vCPU<br> 2 個通訊端 * 6 核心 @ 2.5ghz | 24 GB | 1 GB | 一天最多 2 TB

設定處理序伺服器，如下所示：

1. 請檢閱[必要條件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 安裝中的伺服器[入口網站](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)，或從[命令列](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)。
3. 設定複寫的機器，以使用新的伺服器。 如果您已經有複寫的機器：
    - 您可以[移動](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)至新的處理序伺服器的整個處理序伺服器工作負載。
    - 或者，您可以[移動](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)特定 Vm 至新的處理序伺服器。



## <a name="enable-large-scale-replication"></a>啟用大規模的複寫

規劃容量及部署之後的必要的元件和基礎結構，啟用複寫的 Vm 數目很大。

1. 為批次排序機器。 您為 Vm 啟用複寫的批次內，並移至下一個批次。

    - 針對 VMware Vm，您可以使用[建議 VM 批次大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)部署規劃工具報告中。
    - 實體機器，我們建議您識別根據機器具有相同的大小和數量的資料，並根據可用的網路輸送量的批次。 目標是時間的批次的電腦，可能會完成在其初始複寫差不多。
    
2. 如果機器的磁碟變換很高，或超過限制，在部署 thePlanner 中的，您可以移動您不需要關閉機器複寫 （例如記錄檔傾印或 temp 檔案） 的非重要檔案。 針對 VMware Vm，可以將這些檔案移到不同的磁碟，然後[排除該磁碟](vmware-azure-exclude-disk.md)不要複寫。
3. 啟用複寫之前，請檢查的機器符合[複寫需求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 設定複寫原則[VMware Vm](vmware-azure-set-up-replication.md#create-a-policy)或是[實體伺服器](physical-azure-disaster-recovery.md#create-a-replication-policy)。
5. 啟用複寫[VMware Vm](vmware-azure-enable-replication.md)或是[實體伺服器](physical-azure-disaster-recovery.md#enable-replication)。 這樣就能開始選取的機器的初始複寫。

## <a name="monitor-your-deployment"></a>監視您的部署

開始進行第一批 Vm 的複寫之後，開始監視您的部署，如下所示：  

1. 將指定的災害復原的系統管理員來監視複寫的機器的健全狀況狀態。
2. [監視事件](site-recovery-monitor-and-troubleshoot.md)複寫的項目和基礎結構。
3. [監視健全狀況](vmware-physical-azure-monitor-process-server.md)的向外延展處理序伺服器。
4. 若要取得註冊[電子郵件通知](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications)事件，以便進行監視。
5. 定期舉行[災害復原演練，又](site-recovery-test-failover-to-azure.md)，以確保一切運作正常。


## <a name="plan-for-large-scale-failovers"></a>針對大規模的容錯移轉的計劃

在嚴重損壞的情況下，您可能需要在容錯移轉至 Azure 的機器/工作負載一大堆。 準備這種類型的事件，如下所示。

您可以事先準備的容錯移轉，如下所示：

- [準備您的基礎結構和 Vm](#plan-infrastructure-and-vm-connectivity)和，讓您的工作負載容錯移轉之後，將會都使用，讓使用者可以存取 Azure Vm。
- 附註[容錯移轉限制](#failover-limits)稍早在這份文件。 請確定您的容錯移轉將會落在這些限制範圍內。
- 執行定期[災害復原演練](site-recovery-test-failover-to-azure.md)。 鑽研可協助：
    - 尋找您在容錯移轉前的部署中的間距。
    - 評估您的應用程式的端對端 RTO。
    - 評估您的工作負載的端對端 RPO。
    - 找出 IP 位址範圍衝突。
    - 當您執行演練，我們建議不用於鑽研的生產環境網路，避免在生產和測試網路中，使用相同的子網路名稱，並清除測試容錯移轉之後每個向下鑽研。

若要執行大規模的容錯移轉，我們建議下列各項：

1. 建立工作負載容錯移轉的復原方案。
    - 每個復原方案可以觸發多達 50 個機器的容錯移轉。
    - [深入了解](recovery-plan-overview.md) 復原計劃。
2. 將 Azure 自動化 runbook 指令碼新增至復原計劃，來自動化 Azure 上的任何手動工作。 一般工作包括設定負載平衡器、 更新 DNS 等等。 [深入了解](site-recovery-runbook-automation.md)
2. 容錯移轉之前，準備 Windows 機器，使其符合與 Azure 環境。 [容錯移轉限制](#plan-azure-subscriptions-and-quotas)高符合的機器。 [了解更多](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)runbook 相關。
4.  觸發容錯移轉[開始 AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet，並搭配復原計劃。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [監視 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
