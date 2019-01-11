---
title: Azure 備份：使用 Azure 入口網站還原虛擬機器
description: 使用 Azure 入口網站從復原點還原 Azure 虛擬機器
services: backup
author: geethalakshmig
manager: vijayts
keywords: 還原備份；如何還原；復原點；
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793371"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>使用 Azure 入口網站來還原虛擬機器
於定義的間隔進行資料快照，來保護您的資料。 這些快照稱為復原點，而且儲存在復原服務保存庫中。 如果需要修復或重新建立虛擬機器 (VM)，您可以從任何已儲存的復原點還原 VM。 當您還原復原點時，您可以：

* 建立新的 VM：從備份 VM 的時間點復原點。
* 還原磁碟：使用程序隨附的範本來自訂還原的 VM，或進行個別檔案復原。

本文說明如何將 VM 還原至新的 VM，或還原所有已備份的磁碟。 關於個別檔案復原，請參閱[從 Azure VM 備份復原檔案](backup-azure-restore-files-from-vm.md)。

![三種可從 VM 備份還原的方式](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


從 VM 備份還原一個 VM 或所有磁碟需要兩個步驟︰

* 選取要還原的還原點。
* 選取還原類型
    - 選項 1：建立新的 VM
    - 選項 2：還原磁碟。

## <a name="select-a-restore-point-for-restore"></a>選取要還原的還原點
1. 登入 [Azure 入口網站](http://portal.azure.com/)。

2. 在 Azure 功能表上，選取 [所有服務]。 在服務清單中，輸入**復原服務**，或移至 [儲存體]，其中會列出**復原服務保存庫**，請加以選取。

    ![復原服務保存庫](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. 便會顯示訂用帳戶中保存庫的清單。

    ![復原服務保存庫清單](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. 從復原服務保存庫清單中，選取與您所要還原 VM 相關聯的保存庫。 當您選取保存庫時，其儀表板隨即開啟。

    ![已選取的復原服務保存庫](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. 在保存庫儀表板的 [備份項目] 圖格上，選取 [Azure 虛擬機器]。

    ![保存庫儀表板](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. [備份項目] 刀鋒視窗與 Azure VM 的清單會隨即開啟。

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. 從清單中選取 VM，以開啟儀表板。 VM 儀表板會開啟至包含 [復原點] 圖格的 [監視] 區域。 所有 VM 層級的作業 (例如 [立即備份]、[檔案復原]、[停止備份]) 都可以從這個刀鋒視窗中執行。
從此刀鋒視窗中執行還原有很多種方式。 請注意，此刀鋒視窗只會列出過去 30 天的復原點。

    - 若要使用來自過去 30 天的還原點進行還原，以滑鼠右鍵按一下 VM > [還原 VM]。
    - 若要使用過去 30 天以前的還原點進行還原，可按一下 [還原點] 底下的連結。
    - 若要使用自訂的日期列出並篩選 VM，可按一下功能表中的 [還原 VM]。 使用篩選條件來變更所顯示還原點的時間範圍。 您也可以篩選不同類型的資料一致性。
8. 檢閱還原點設定：
    - 資料一致性會在復原點中顯示[一致性類型](backup-azure-vms-introduction.md#consistency-types)。
    - **復原類型**會顯示儲存復原點的位置 (位於保存庫、儲存體帳戶或兩者)。 [深入了解](https://azure.microsoft.com/blog/large-disk-support/)立即復原點。

  ![還原點](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. 選取還原點。

10. 選取 [還原設定]。 [還原組態] 刀鋒視窗隨即開啟。

## <a name="choose-a-vm-restore-configuration"></a>選擇 VM 還原組態
在選取還原點之後，請選擇 VM 還原組態。 若要設定還原的 VM，您可以使用 Azure 入口網站或 PowerShell。

### <a name="restore-options"></a>還原選項

**選項** | **詳細資料**
--- | ---
**新建- 建立 VM** | 相當於快速建立 VM。 從還原點啟動基本 VM 並加以執行。<br/><br/> VM 設定可在還原過程中加以修改。
**新建 - 還原磁碟** | 建立您可在還原過程中自訂的 VM。<br/><br/> 此選項會將 VHD 複製到您指定的儲存體帳戶。<br/><br/> - 儲存體帳戶應該與保存庫位於相同位置。<br/><br/> 如果您沒有適當的儲存體帳戶，則需建立一個。<br/><br/> 儲存體帳戶複寫類型會顯示在括號中。 不支援區域備援儲存體 (ZRS)。<br/><br/> 從儲存體帳戶中，您可以將還原的磁碟連結到現有的 VM，或使用 PowerShell 從還原的磁碟 建立新的 VM。
**取代現有的** | 使用此選項，您不需要建立 VM。<br/><br/> 目前的 VM 必須存在。 如果已刪除，則無法使用這個選項。<br/><br/> Azure 備份會取得現有 VM 的快照集，並將它儲存於指定的暫存位置。 接著使用選取的還原點來取代連線到 VM 的現有磁碟。 先前建立的快照集會複製到保存庫，並根據您的備份保留原則儲存為復原點。<br/><br/> 針對未加密的受控 VM 支援取代現有的項目。 不支援非受控磁碟、[一般化的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 或[使用自訂映像建立的](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) \(英文\) VM。<br/><br/> 如果還原點中的磁碟數目多於或少於目前的 VM，則還原點中的磁碟數目只會反映該 VM。

> [!NOTE]
> 如果您想要使用進階網路設定還原 VM (例如，如果它們由內部或外部負載平衡器所管理，或有多個 NIC 或多個保留的 IP 位址)，請使用 PowerShell 進行還原。 [深入了解](#restore-vms-with-special-network-configurations)。
> 如果 Windows VM 使用[中樞授權](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，請使用 [新建 - 還原磁碟] 選項，然後使用 PowerShell 或還原範本來建立 VM 並將 [授權類型] 設為 [Windows_Server]。 此設定也可以在建立 VM 之後套用。


指定還原設定，如下所示：

1. 在 [還原] 中，選取[還原點](#select-a-restore-point-for-restore) > [還原設定]。
2. 在 [還原設定] 中，根據上表所摘要的設定，選取您想要還原的 VM。

    ![還原組態精靈](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>新建 - 建立 VM

1. 在 [還原設定] > [新建] > [還原類型] 中，選取 [建立虛擬機器]。
2. 在 [虛擬機器名稱] 中，指定訂用帳戶中不存在的 VM。
3. 在 [資源群組] 中，選取新 VM 的現有資源群組，或使用全域的唯一名稱來建立新的資源群組。 如果您指派已經存在的名稱，Azure 就會為群組指派與 VM 相同的名稱。
4. 在 [虛擬網路] 中，選取將放置 VM 的 VNet。 與訂用帳戶相關聯的所有 VNet 均會顯示。 選取子網路。 預設會選取第一個子網路。
5. 在 [儲存體位置] 中，指定針對 VM 所使用的儲存體類型。

    ![還原組態精靈](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. 在 [還原設定] 中，選取 [確定]。 在 [還原] 中，按一下 [還原] 以觸發還原作業。



## <a name="create-new-restore-disks"></a>新建 - 還原磁碟

1. 在 [還原設定] > [新建] > [還原類型] 中，選取 [還原磁碟]。
2. 在 [資源群組] 中，選取已還原磁碟的現有資源群組，或使用全域的唯一名稱來建立新的資源群組。
3. 在 [儲存體帳戶] 中，指定要將 VHD 複製到其中的帳戶。 請確定該帳戶位於與保存庫相同的區域。

    ![已完成復原設定](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. 在 [還原設定] 中，選取 [確定]。 在 [還原] 中，按一下 [還原] 以觸發還原作業。
5. 還原磁碟之後，您可以執行下列任何動作來完成 VM 還原作業。

    - 使用還原作業期間所產生的範本來自訂設定，並觸發 VM 部署。 您會編輯預設範本設定，並提交範本以進行 VM 部署。
    - 您可以[將還原的磁碟連結](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)至現有 VM。
    - 您可以使用 PowerShell，從還原的磁碟[建立新的 VM](backup-azure-vms-automation.md#restore-an-azure-vm)。


## <a name="replace-existing-disks"></a>取代現有的磁碟

使用此選項，將目前 VM 中的現有磁碟取代為選取的還原點。

1. 在 [還原設定] 中，按一下 [取代現有的]。
2. 在 [還原類型] 中，選取 [取代磁碟] (將取代一或多個現有 VM 磁碟的還原點)。
3. 在 [暫存位置] 中，指定應儲存目前受控磁碟快照集的位置。

   ![還原組態精靈取代現有的](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>追蹤還原作業
在觸發還原作業之後，備份服務會建立一個作業以便追蹤還原作業。 備份服務也會建立，並會在入口網站的 [通知] 區域暫時顯示通知。 如果您沒看到通知，請選取 [通知] 符號以檢視您的通知。

![已觸發還原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

按一下通知的超連結以移至 **BackupJobs** 清單。 指定作業的所有作業詳細資料位於 **BackupJobs** 清單中。 您可以按一下 [備份作業] 圖格前往保存庫儀表板中的 **BackupJobs**，選取 [Azure 虛擬機器] 以顯示與保存庫相關聯的作業。

[備份作業] 刀鋒視窗會開啟並顯示作業的清單。

![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

[還原詳細資料] 刀鋒視窗中現在已提供 [進度列]。 [還原詳細資料] 刀鋒視窗可藉由按一下任何狀態為**進行中**的還原作業來開啟。 [進度列] 適用於還原的所有變體，例如**新建**、**還原磁碟**及**取代現有的**。 還原進度列所提供的詳細資料為**估計的還原時間**、**還原百分比**及**已傳輸的位元組數目**。

還原進度列詳細資料如下：

- **估計的還原時間**一開始會提供完成還原作業所花費的時間。 作業進行時，所花費的時間會減少，並在還原作業完成時到達 0。
- **還原百分比**會提供資料完成還原作業的百分比。
- **已傳輸的位元組數目**會在透過 [建立新的 VM] 進行還原時於子工作中提供。 這會根據要傳輸的位元組數目來提供已傳輸了多少位元組數目的詳細資料。


## <a name="use-templates-to-customize-a-restored-vm"></a>使用範本自訂還原的 VM
[還原磁碟作業完成](#Track-the-restore-operation)之後，請使用還原作業進行過程中所產生的範本，來建立具有不同於備份組態之組態的新 VM。 您也可以使用它來自訂在從還原點建立新 VM 的程序期間所建立之資源的名稱。

![還原作業向下鑽研](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

若要取得在還原磁碟選項中產生的範本：

1. 請移至對應於作業的**還原作業詳細資料**。

2. 在 [還原作業詳細資料] 畫面上，選取 [部署範本] 來起始範本部署。

3. 在自訂部署的 [部署範本] 刀鋒視窗上，使用範本部署來[編輯和部署範本](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，或在部署之前[撰寫範本](../azure-resource-manager/resource-group-authoring-templates.md)以附加更多自訂。

  ![載入範本部署](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. 輸入所需值之後，接受 [條款及條件]，然後選取 [購買]。

  ![提交範本部署](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>還原後的步驟
* 如果您使用 cloud-init 型 Linux 散發套件 (例如 Ubuntu)，基於安全理由，還原後會封鎖密碼。 請在還原的 VM 上使用 VMAccess 擴充功能[重設密碼](../virtual-machines/linux/reset-password.md)。 建議您在這些散發套件上使用 SSH 金鑰，以避免在還原後重設密碼。
* 系統會安裝出現在備份組態期間的擴充功能，但不會加以啟用。 如果您發現問題，請重新安裝擴充功能。
* 如果備份的 VM 在還原後具有靜態 IP，還原的 VM 有動態 IP 可避免在建立還原的 VM 時發生衝突。 深入了解如何[將靜態 IP 加入至還原的 VM](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)。
* 還原的 VM 不會有可用性設定值組。 從 PowerShell 建立 VM 或使用還原的磁碟建立範本時，我們建議使用還原磁碟選項來[新增可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)。


## <a name="backup-for-restored-vms"></a>備份已還原的 VM
如果您將 VM 還原至相同的資源群組，並使用與原始備份 VM 相同的名稱，則會在還原後於 VM 上繼續進行備份。 如果您將 VM 還原到不同的資源群組，或為還原的 VM 指定了不同的名稱，則會將該 VM 視為新的 VM。 您必須為還原的 VM 設定備份。

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>在 Azure 資料中心發生災害時還原 VM
如果 VM 執行的主要資料中心發生災害，而您已將備份保存庫設定為異地備援，Azure 備份可讓您將備份 VM 還原至配對的資料中心。 在這種情況中，請選取出現在配對資料中心的儲存體帳戶。 還原程序的其餘部分會維持不變。 備份會使用配對之地理區域的計算服務來建立還原 VM。 如需詳細資訊，請參閱 [Azure 資料中心復原功能](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)。

## <a name="restore-domain-controller-vms"></a>還原網域控制站 VM
備份網域控制站 (DC) VM 是備份支援的案例。 不過，在還原程序進行期間請務必小心。 正確的還原程序取決於網域結構。 最簡單的情況是您在單一網域中擁有單一 DC。 而在生產環境的負載中，較常見的情況是您擁有單一網域且內含多個 DC，其中有些 DC 可能位於內部部署環境。 最後，您也可能擁有內含多個網域的樹系。

從 Active Directory 的觀點來看，Azure VM 就像是位於新式的受支援 Hypervisor 上的其他 VM。 與內部部署 Hypervisor 的主要差異是，Azure 沒有提供 VM 主控台。 在某些情況下，您必須使用主控台，例如使用裸機復原 (BMR) 類型的備份進行復原。 不過，從備份保存庫來還原 VM 可完整取代 BMR。 我們也提供了目錄服務還原模式 (DSRM)，因此，您可以進行所有的 Active Directory 復原案例。 如需詳細資訊，請參閱[虛擬網域控制站的備份和還原考量](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers)以及[規劃 Active Directory 樹系復原](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx)。

### <a name="single-dc-in-a-single-domain"></a>單一網域中的單一 DC
VM 可以從 Azure 入口網站或使用 PowerShell 還原 (就像任何其他 VM)。

### <a name="multiple-dcs-in-a-single-domain"></a>單一網域中的多個 DC
如果您可以透過網路來連線到相同網域內的其他 DC，您就可以像 VM 一樣地還原 DC。 如果該 DC 是網域內剩餘的最後一個 DC，或者您是在隔離的網路中進行復原，則您必須遵循樹系的復原程序。

### <a name="multiple-domains-in-one-forest"></a>單一樹系中的多個網域
如果您可以透過網路來連線到相同網域內的其他 DC，您就可以像 VM 一樣地還原 DC。 若是其他情況，則建議進行樹系復原。

## <a name="restore-vms-with-special-network-configurations"></a>還原具有特殊網路組態的 VM
可以備份和還原具有下列特殊網路組態的 VM。 不過，進行還原程序時，這些組態需要一些特殊的考量：

* 負載平衡器底下的 VM (內部與外部)
* 具有多個保留的 IP 的 VM
* 具有多個 NIC 的 VM

> [!IMPORTANT]
> 為 VM 建立特殊網路組態時，您必須使用 PowerShell 從還原的磁碟建立 VM。
>
>

若要在還原至磁碟後，完全重新建立 VM，請依照以下步驟執行：

1. 使用 [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) 從復原服務保存庫還原磁碟。

1. 使用 PowerShell Cmdlet 建立負載平衡器/多個 NIC/多個保留的 IP 所需的 VM 組態。 使用該組態建立具備想要之組態的 VM：

   a. 在具有[內部負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)的雲端服務中建立 VM。

   b. 建立 VM 來連線至[網際網路面向負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)。

   c. 建立具有[多個 NIC](../virtual-machines/windows/multiple-nics.md) 的 VM。

   d. 建立具有[多個保留 IP](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md) 的 VM。

## <a name="next-steps"></a>後續步驟
您現在可以還原您的 VM，請參閱疑難排解文章中有關 VM 常見錯誤的資訊。 此外，請參閱有關您的 VM 管理工作的文章。

* [針對錯誤進行疑難排解](backup-azure-vms-troubleshoot.md#restore)
* [管理虛擬機器](backup-azure-manage-vms.md)
