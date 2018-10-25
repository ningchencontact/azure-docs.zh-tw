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
ms.openlocfilehash: 450314dddd49825bae689701b694f9a26758835e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377634"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>使用 Azure 入口網站來還原虛擬機器
於定義的間隔進行資料快照，來保護您的資料。 這些快照稱為復原點，而且儲存在復原服務保存庫中。 如果需要修復或重新建立虛擬機器 (VM)，您可以從任何已儲存的復原點還原 VM。 當您還原復原點時，您可以：

* 建立新的 VM，作為已備份 VM 的時間點表示。
* 還原磁碟，並使用程序隨附的範本來自訂還原的 VM，或進行個別檔案復原。

本文說明如何將 VM 還原至新的 VM，或還原所有已備份的磁碟。 關於個別檔案復原，請參閱[從 Azure VM 備份復原檔案](backup-azure-restore-files-from-vm.md)。

![三種可從 VM 備份還原的方式](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure 有兩種用來建立和使用資源的部署模型：[Azure Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 本文提供的資訊和程序可用來還原使用 Resource Manager 模型部署的 VM。
>
>

從 VM 備份還原一個 VM 或所有磁碟需要兩個步驟︰

* 選取要還原的還原點。
* 選取還原類型、建立新的 VM 或還原磁碟，並指定必要的參數。

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

    a) 以滑鼠右鍵按一下此刀鋒視窗中的復原點 (低於 30 天)，並起始 [還原 VM]。

    b) 若要還原大於 30 天的復原點，可以使用刀鋒視窗中提供的 [按一下這裡]。

    c) 功能表標頭中的 [還原 VM] 會提供選項，可依偏好在自訂日期中列出並篩選 VM。

    使用 [篩選] 變更所顯示還原點的時間範圍。 根據預設，系統會顯示所有一致性的還原點。 修改 [所有還原點] 篩選器，可選取特定的還原點一致性。 如需每種還原點類型的詳細資訊，請參閱[資料一致性](backup-azure-vms-introduction.md#data-consistency)。

    還原點一致性選項：
    - 當機時保持一致還原點
    - 應用程式保持一致還原點
    - 系統檔案保持一致還原點
    - 所有還原點

  ![還原點](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > 復原類型可表示還原點是位在客戶儲存體帳戶、保存庫或兩者之中。 深入了解[立即復原點](https://azure.microsoft.com/blog/large-disk-support/)。

8. 在 [還原] 刀鋒視窗中，選取 [還原點]。

    ![選取還原點](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    按一下 [確定]後，[還原] 刀鋒視窗會顯示還原點已設定完成。
9. 如果您尚未到達該位置，請移至 [還原] 刀鋒視窗。 確定[已選取還原點](#select-a-restore-point-for-restore)，然後選取**還原組態**。 [還原組態] 刀鋒視窗隨即開啟。

## <a name="choose-a-vm-restore-configuration"></a>選擇 VM 還原組態
在選取還原點之後，請選擇 VM 還原組態。 若要設定還原的 VM，您可以使用 Azure 入口網站或 PowerShell。

1. 如果您尚未到達該位置，請移至 [還原] 刀鋒視窗。 確定[已選取還原點](#select-a-restore-point-for-restore)，然後選取**還原組態**。 [還原組態] 刀鋒視窗隨即開啟。
2. 此刀鋒視窗中目前有兩個選項，一個是 [新建]，這是預設值，而另一個是 [取代現有]，也就是就地還原，以取代僅保留現有設定和擴充功能的磁碟。

> [!NOTE]
> 我們正努力在未來的幾個月將整個 VM 取代為磁碟、網路設定、組態和擴充功能。
>
>

 在將資料還原至新 VM 或新磁碟的 [新建] 選項中，您有兩個選擇：

 ![還原組態精靈](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **建立虛擬機器**
 - **還原磁碟**

![還原組態精靈](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

入口網站提供還原 VM 專用的 [快速建立] 選項。 若要自訂 VM 組態或在建立新的 VM 選項時所建立之資源的名稱，請使用 PowerShell 或入口網站來還原備份的磁碟。 使用 PowerShell 命令，將它們連結至您所選擇的 VM 組態。 或者，您也可以使用還原的磁碟所隨附的範本，來自訂還原的 VM 。 如需有關如何還原具有多個 NIC 或在負載平衡器管理下之 VM 的相關資訊，請參閱[使用特殊網路組態還原 VM](#restore-vms-with-special-network-configurations)。 如果您的 Windows VM 使用[中樞授權](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，請還原磁碟並使用此文章所指定的 PowerShell/範本建立 VM。 請確定您在建立 VM 時將 [授權類型] 指定為 "Windows_Server"，以在還原的 VM 上利用中樞優點。 請注意，這也可以在稍後建立 VM 之後完成。

## <a name="create-a-new-vm-from-a-restore-point"></a>從還原點建立新的 VM
1. 在前一節中所述的 [還原組態] 刀鋒視窗上，輸入或選取下列各欄位的值︰

    a. **還原類型**。 建立虛擬機器。

    b. **虛擬機器名稱**。 輸入不存在於訂用帳戶中的 VM 名稱。

    c. **資源群組**。 使用現有的資源群組或建立新群組。 如果您要還原傳統 VM，請使用此欄位來指定新雲端服務的名稱。 如果您是建立新的資源群組/雲端服務，此名稱必須是全域唯一的名稱。 一般來說，雲端服務名稱會與公眾對應 URL 相關聯：例如，[cloudservice].cloudapp.net。 如果您嘗試使用已經使用的雲端資源群組/雲端服務名稱，Azure 會指派給資源群組/雲端服務與 VM 相同的名稱。 Azure 會顯示未與任何同質群組相關聯的資源群組/雲端服務和 VM。 如需詳細資訊，請參閱[如何從同質群組移轉至區域虛擬網路](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。

    d. **虛擬網路**。 在建立 VM 時，請選取虛擬網路。 此欄位提供與訂用帳戶相關聯的所有虛擬網路。 顯示的 VM 資源群組會以括號括住。

    e. **子網路**。 如果虛擬網路有子網路，預設會選取第一個子網路。 如果有其他子網路，請選取您需要的子網路。

    f. **儲存位置**。 儲存體帳戶是預備環境位置。 此功能表會列出與復原服務保存庫位於相同位置的儲存體帳戶。 不支援區域備援的儲存體帳戶。 如果沒有與復原服務保存庫相同位置的儲存體帳戶，您必須在開始還原作業之前，建立一個儲存體帳戶。 儲存體帳戶的複寫類型會顯示在括號中。

    ![還原組態精靈](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * 虛擬網路對於傳統 VM 是選擇性的，但對於部署 Resource Manager 的 VM 則為必要。

    > * 預備環境位置中儲存體帳戶所提供的儲存體類型 (進階或標準) 會決定還原磁碟儲存體類型。 我們目前不支援在還原時使用混合的磁碟模式。
    >
    >

2. 在 [還原組態] 刀鋒視窗上，選取 [確定] 完成還原組態。 在 [還原] 刀鋒視窗上，選取 [還原] 以觸發還原作業。

## <a name="restore-backed-up-disks"></a>還原備份的磁碟
若 [還原組態] 刀鋒視窗中的還原類型值為 [還原磁碟]，則可允許使用自訂組態來建立 VM。 在還原磁碟時，選取的儲存體帳戶應與復原服務保存庫位於相同的位置。 如果沒有與復原服務保存庫相同位置的儲存體帳戶，您必須建立一個儲存體帳戶。 不支援 ZRS 儲存體帳戶。 儲存體帳戶的複寫類型會顯示在括號中。

在還原作業之後，請使用下列項目：
* [使用範本自訂還原的 VM](#use-templates-to-customize-restore-vm)
* [使用還原的磁碟連結至現有 VM](../virtual-machines/windows/attach-managed-disk-portal.md)
* [使用 PowerShell 從還原的磁碟建立新的 VM](./backup-azure-vms-automation.md#restore-an-azure-vm)

在 [還原組態] 刀鋒視窗上，選取 [確定] 完成還原組態。 在 [還原] 刀鋒視窗上，選取 [還原] 以觸發還原作業。

![已完成復原設定](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

透過索引標籤 [取代現有] 完成 [就地還原]。

## <a name="replace-existing-disks-from-a-restore-point"></a>從還原點取代現有磁碟
[取代現有] 選項可協助將目前 VM 中的現有磁碟取代為選取的還原點。 僅當目前的 VM 存在時才能執行此作業。 如果因任何原因而刪除 VM，則無法執行這項作業；或者，建議您 [新建] VM 或磁碟，以繼續進行還原作業。 在取代現有磁碟作業期間，我們會在起始取代磁碟作業之前先備份資料，以防患未然。 如果還原點中的磁碟多/少於目前的 VM，則還原點中的磁碟數目只會反映在 VM 中。 目前僅支援受控磁碟和未加密 VM 的取代現有選項。  

 在 [還原組態] 刀鋒視窗中，唯一需要選取的輸入是 [預備環境位置]。

   ![還原組態精靈取代現有的](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **還原類型**。 取代磁碟代表所選取的還原點將會取代現有 VM 中的磁碟。

 b. **預備環境位置**。 儲存體帳戶是受控磁碟的預備環境位置。 此功能表會列出與復原服務保存庫位於相同位置的儲存體帳戶。 不支援區域備援的儲存體帳戶。 如果沒有與復原服務保存庫相同位置的儲存體帳戶，您必須在開始還原作業之前，建立一個儲存體帳戶。 儲存體帳戶的複寫類型會顯示在括號中。

## <a name="track-the-restore-operation"></a>追蹤還原作業
在觸發還原作業之後，備份服務會建立一個作業以便追蹤還原作業。 備份服務也會建立，並會在入口網站的 [通知] 區域暫時顯示通知。 如果您沒看到通知，請選取 [通知] 符號以檢視您的通知。

![已觸發還原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

按一下通知的超連結以移至 **BackupJobs** 清單。 指定作業的所有作業詳細資料位於 **BackupJobs** 清單中。 您可以按一下 [備份作業] 圖格前往保存庫儀表板中的 **BackupJobs**，選取 [Azure 虛擬機器] 以顯示與保存庫相關聯的作業。

[備份作業] 刀鋒視窗會開啟並顯示作業的清單。

![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/restore-job-completed.png)

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
