---
title: "Azure 備份：準備備份虛擬機器 | Microsoft Docs"
description: "確認在 Azure 中備份虛擬機器的環境已準備就緒。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "備份；備份；"
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/3/2017
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 3c2ea9e5872454b0bac67c39362a1f94b6fa47b8
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>準備環境以備份 Resource Manager 部署的虛擬機器

本文提供的步驟來準備您的環境，備份 Azure Resource Manager 部署虛擬機器 (VM)。 程序中展示的步驟使用 Azure 入口網站。  

Azure 備份服務有兩種類型的保存庫保護您的 Vm： 備份保存庫與復原服務保存庫。 備份保存庫協助保護透過傳統部署模型所部署的 Vm。 復原服務保存庫協助保護*傳統部署和資源管理員部署 Vm*。 如果您想要保護的資源管理員部署的 VM，您必須使用復原服務保存庫。

> [!NOTE]
> Azure 有兩種用來建立和使用資源的部署模型： [Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。

您可以保護，或備份資源管理員部署虛擬機器之前，請確定具有下列必要條件：

* 建立復原服務保存庫 （或識別現有的復原服務保存庫）*與您的 VM 位於相同位置中*。
* 選取案例、定義備份原則及定義要保護的項目。
* 請檢查虛擬機器上的 VM 代理程式安裝。
* 檢查網路連線。
* 適用於 Linux Vm，如果您想要自訂您的應用程式一致的備份的備份環境，請遵循[步驟來設定前快照集與後快照集指令碼](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)。

如果您的環境中已存在這些條件，繼續進行[備份您的 Vm](backup-azure-arm-vms.md)發行項。 如果您需要設定，或任何一項先決條件檢查，這篇文章會引導您逐步完成。

## <a name="supported-operating-systems-for-backup"></a>備份支援的作業系統
 * **Linux**: Azure Backup 支援[一份發佈 Azure 背書](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，除非 CoreOS Linux。 
 
    > [!NOTE] 
    > 其他使-您-擁有-Linux 散發套件可能會運作，只要虛擬機器上的 VM 代理程式可和 Python 存在的支援。 不過，我們不背書備份這些散發。
 * **Windows Server**：不支援比 Windows Server 2008 R2 更舊的版本。

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>備份和還原 VM 時的限制
準備您的環境之前，請務必了解這些限制：

* 不支援備份具有 16 個以上資料磁碟的虛擬機器。
* 不支援備份資料的虛擬機器磁碟大小大於 1023 GB。

  > [!NOTE]
  > 我們有私用的預覽，以支援備份具有 1 TB （或更高） 的未受管理磁碟 Vm。 如需詳細資訊，請參閱[私人預覽中的大型磁碟 VM 備份支援](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)。
  >

* 不支援備份具有保留的 IP 且沒有已定義之端點的虛擬機器。
* 不支援備份 Vm 就 BitLocker 加密金鑰 (BEK) 可透過加密。 不支援備份 Linux 整合金鑰安裝程式 (LUKS) 加密透過加密的 Linux Vm。
* 我們不建議備份包含叢集共用磁碟區 (CSV) 或向外延展檔案伺服器設定的 Vm。 它們需要牽涉到快照集工作期間的叢集組態中包含的所有 Vm。 Azure 備份無法保持 VM 間的一致性。 
* 備份資料不包含掛接的網路磁碟機連接到 VM。
* 不支援在還原期間取代現有的虛擬機器。 如果您嘗試在 VM 存在時還原 VM，還原作業將會失敗。
* 不支援跨區域備份和還原。
* 目前不支援備份和還原的儲存體 ACLed Vm。 如果您已啟用儲存體上 VNET 功能，可讓儲存體帳戶只能從特定的 Vnet/子網路和/或 Ip 來存取，則不支援 Vm 的備份。
* 您可以備份所有公用 Azure 的區域中的虛擬機器。 (請參閱[檢查清單](https://azure.microsoft.com/regions/#services)支援的地區。)如果您要尋找的地區不支援目前，它不會出現在下拉式清單在保存庫建立期間。
* 只有透過 PowerShell 才支援還原屬於多網域控制站 (DC) 組態的 DC VM。 若要進一步了解，請參閱[還原多個 DC 的網域控制站](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)。
* 僅支援透過 PowerShell 還原具有以下特殊網路組態的虛擬機器。 還原作業完成之後，還原工作流程，在 UI 中透過建立的 Vm 不會有這些網路組態。 若要深入了解，請參閱 [還原具有特殊網路組態的 VM](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)。
  * 負載平衡器組態下的虛擬機器 (內部與外部)
  * 具有多個保留的 IP 位址的虛擬機器
  * 具有多個網路介面卡的虛擬機器

## <a name="create-a-recovery-services-vault-for-a-vm"></a>復原服務保存庫建立 vm
復原服務保存庫是儲存的備份和復原點已建立一段時間的實體。 復原服務保存庫也包含與受保護的虛擬機器相關聯的備份原則。

若要建立復原服務保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在**中樞**功能表上，選取**瀏覽**，然後輸入**復原服務**。 當您開始輸入時，您的輸入篩選的資源清單。 選取**復原服務保存庫**。

    ![在方塊中輸入，然後在結果中選取 「 復原服務保存庫 」](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    隨即會出現 [復原服務保存庫] 清單。
3. 在**復原服務保存庫**功能表上，選取**新增**。

    ![建立復原服務保存庫的步驟 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **復原服務保存庫** 窗格隨即開啟。 它會提示您提供的資訊**名稱**，**訂用帳戶**，**資源群組**，和**位置**。

    ![「 復原服務保存庫 」 窗格](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. 在 [名稱] 中，輸入易記名稱來識別保存庫。 必須是 Azure 訂用帳戶中唯一的名稱。 輸入包含 2 到 50 個字元的名稱。 必須以字母為開頭，它可以包含字母、 數字和連字號。
5. 選取**訂用帳戶**若要查看可用的訂用帳戶清單。 如果您不確定要使用哪一個訂用帳戶，使用預設值 （或建議） 訂用帳戶。 有多個選項只有當您的工作或學校帳戶與多個 Azure 訂用帳戶相關聯。
6. 選取**資源群組**若要查看可用的資源群組的清單，或選取**新增**來建立新的資源群組。 完整資源群組的詳細資訊，請參閱[Azure 資源管理員概觀](../azure-resource-manager/resource-group-overview.md)。
7. 選取**位置**選取保存庫的地理區域。 保存庫*必須*與您想要保護的虛擬機器位於相同區域。

   > [!IMPORTANT]
   > 如果您不確定您的 VM 所在的位置，請關閉 [建立保存庫] 對話方塊，並移至入口網站中的虛擬機器的清單。 如果您有多個區域中的虛擬機器，您需要在每個區域中建立的復原服務保存庫。 請先在第一個位置建立保存庫，再進入下一個位置。 沒有需要指定儲存體帳戶來儲存備份的資料。 復原服務保存庫和 Azure 備份服務會自動處理的。
   >
   >

8. 選取 [建立] 。 要等復原服務保存庫建立好，可能需要一些時間。 監視入口網站的右上方區域中的狀態通知。 建立您的保存庫之後，它會顯示復原服務保存庫清單中。 如果您沒有看到您的保存庫，選取**重新整理**。

    ![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

現在您已建立好保存庫，接下來要了解如何設定儲存體複寫。

## <a name="set-storage-replication"></a>設定儲存體複寫
儲存體複寫選項可讓您選擇地理備援儲存體和本機備援儲存體之間。 根據預設，保存庫具有異地備援儲存體。 如果這是您的主要備份，請讓選項繼續設定為異地備援儲存體。 如果您想要更便宜但不持久的選項，請選擇本地備援儲存體。

若要編輯儲存體複寫設定︰

1. 在**復原服務保存庫** 窗格中，選取您的保存庫。
    當您選取您的保存庫**設定**窗格 （其中的保存庫名稱上方） 和保存庫詳細資料窗格中開啟。

   ![從備份保存庫的清單中選擇您的保存庫](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. 在**設定** 窗格中，使用垂直滑桿向下捲動至**管理**區段，然後選取**備份基礎結構**。 在**一般**區段中，選取**備份設定**。 在**備份設定** 窗格中，選擇您的保存庫的儲存體複寫選項。 根據預設，保存庫具有異地備援儲存體。

   ![備份保存庫的清單](./media/backup-azure-arm-vms-prepare/full-blade.png)

   如果您使用 Azure 做為備份的主要儲存體端點，繼續使用異地備援儲存體。 如果您使用 Azure 做為非主要備份儲存體端點，請選擇 本機備援儲存體。 如需詳細資訊中的儲存體選項[Azure 儲存體複寫概觀](../storage/common/storage-redundancy.md)。

3. 如果您變更儲存體複寫類型，選取**儲存**。
    
您選擇儲存體選項，您保存庫之後，您準備將 VM 與保存庫關聯。 若要開始關聯，請探索及註冊 Azure 虛擬機器。

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>選取備份目標、 設定原則，以及定義要保護的項目
您將 VM 向保存庫之前，先執行探索程序，以確保會識別任何新的虛擬機器已加入至訂用帳戶。 處理序會查詢 Azure 訂用帳戶，以及資訊中的虛擬機器的清單，例如雲端服務名稱和區域。 

在 Azure 入口網站，*案例*指的是您要將放在復原服務保存庫中。 *原則*是的頻率如何及何時建立復原點排程。 原則也會包含復原點的保留範圍。

1. 如果您已開啟復原服務保存庫，請繼續步驟 2。 如果您還沒有開啟復原服務保存庫，開啟[Azure 入口網站](https://portal.azure.com/)。 在**中樞**功能表上，選取**更多服務**。

   a. 在資源清單中輸入 **復原服務**。 當您開始輸入時，您的輸入篩選清單。 當您看到 [復原服務保存庫] 時，請選取它。

      ![在方塊中輸入，然後在結果中選取 「 復原服務保存庫 」](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      隨即會出現 [復原服務保存庫] 清單。 如果您的訂用帳戶中沒有任何保存庫，這份清單是空的。

      ![復原服務保存庫清單檢視](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. 在 [復原服務保存庫] 清單中選取保存庫。

      **設定**窗格和所選的保存庫儀表板保存庫開啟。

      ![設定 窗格和保存庫儀表板](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. 在保存庫儀表板功能表上，選取**備份**。

   ![備份的按鈕](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **備份**和**備份目標** 窗格會開啟。

3. 上**備份目標** 窗格中，設定**您的工作負載執行？**至**Azure**和**怎麼辦想要備份？**至**虛擬機器**。 然後選取 [確定]。

   ![備份及備份目標窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   此步驟向保存庫註冊的 VM 擴充功能。 **備份目標** 窗格隨即關閉，而**備份原則** 窗格隨即開啟。

   ![「 備份 」 和 「 備份原則 」 窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. 在**備份原則** 窗格中，選取您想要套用至保存庫的備份原則。

   ![選取備份原則](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   預設原則的詳細資料便會列在下拉式功能表之下。 如果您想要建立新原則，請在下拉式功能表中選取 [建立新的]  。 如需定義備份原則的指示，請參閱 [定義備份原則](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)。
    選取**確定**與保存庫中的備份原則。

   **備份原則** 窗格隨即關閉，而**選取虛擬機器** 窗格隨即開啟。
5. 在**選取虛擬機器** 窗格中，選擇與指定的原則建立關聯，並選取虛擬機器**確定**。

   ![「 選取的虛擬機器 」 窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   選取的虛擬機器便會接受驗證。 如果您沒有看到您會看到虛擬機器，檢查它們存在於相同的 Azure 復原服務保存庫位置，以及尚未保護另一個保存庫中。 保存庫儀表板會顯示復原服務保存庫的位置。

6. 既然您已定義上的所有設定保存庫，**備份**窗格中，選取**啟用備份**。 此步驟會將原則部署到保存庫和 Vm。 這個步驟不會建立虛擬機器的初始的復原點。

   ![[啟用備份] 按鈕](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

已成功啟用備份之後，您的備份原則將依排程執行。 如果您想要產生視備份作業，來備份虛擬機器，請參閱[觸發備份工作](./backup-azure-arm-vms.md#triggering-the-backup-job)。

如果您有登錄虛擬機器時遇到問題，請參閱下列資訊和網路連線上安裝 VM 代理程式。 如果您要保護在 Azure 中建立的虛擬機器，則不一定需要下列資訊。 但是，如果您移轉至 Azure 的虛擬機器，請務必正確安裝 VM 代理程式以及虛擬機器，可以與虛擬網路進行通訊。

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>在虛擬機器上安裝 VM 代理程式
備份延伸模組，才能執行，Azure [VM 代理程式](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux)必須安裝在 Azure 虛擬機器。 如果您的 VM 建立 Azure marketplace，VM 代理程式已存在於虛擬機器。 

下列資訊提供給您的情況下*不*Azure marketplace 使用的 VM 建立。 例如，您會從內部部署資料中心移轉 VM。 在這種情況下，VM 代理程式必須安裝，才能保護虛擬機器。

如果您有備份 Azure VM 的問題，請使用下表來檢查 Azure VM 代理程式已正確安裝在虛擬機器上。 資料表會提供 Windows 與 Linux Vm 的 VM 代理程式的其他資訊。

| **作業** | **Windows** | **Linux** |
| --- | --- | --- |
| 安裝 VM 代理程式 |下載並安裝 [代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。 |安裝最新的 [Linux 代理程式](../virtual-machines/linux/agent-user-guide.md)。 您需要有系統管理員權限，才能完成安裝。 我們建議您從散發儲存機制安裝代理程式。 我們「不建議」您直接從 Github 安裝 Linux VM 代理程式。  |
| 更新 VM 代理程式 |更新 VM 代理程式與重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一樣簡單。 <br><br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 |請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的指示。 我們建議您更新從發佈儲存機制的代理程式。 我們*不建議*更新直接從 GitHub 的 Linux VM 代理程式。<br><br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 |
| 驗證 安裝 VM 代理程式 |1.瀏覽至 Azure VM 中的 C:\WindowsAzure\Packages 資料夾。 <br><br>2.尋找 WaAppAgent.exe 檔案。 <br><br>3.在該檔案上按一下滑鼠右鍵，前往 [屬性]，然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更高版本。 |N/A |

### <a name="backup-extension"></a>備份擴充功能
虛擬機器上安裝 VM 代理程式之後，Azure Backup 服務可以安裝 VM 代理程式的備用分機號碼。 備份服務順暢地升級和修補程式備用分機號碼。

備份服務會安裝備用分機號碼，不論 VM 正在執行。 執行中的 VM 提供了取得應用程式一致復原點的絕佳機會。 不過，備份服務會繼續備份 VM，即使它已關閉，無法安裝擴充功能。 這稱為*離線 VM*。 在此情況下，復原點將會是「當機時保持一致」 。

## <a name="establish-network-connectivity"></a>建立網路連線
若要管理 VM 快照，備份的擴充功能會需要連線到 Azure 的公用 IP 位址。 不正確的網際網路連線，虛擬機器的 HTTP 要求逾時時間，備份作業失敗。 如果您的部署具有存取限制就地-透過網路安全性群組 (NSG)，例如選擇其中一個選項來備份的流量提供清楚的路徑：

* [白名單的 Azure 資料中心 IP 範圍](http://www.microsoft.com/en-us/download/details.aspx?id=41653)。
* 部署 HTTP Proxy 伺服器來路由傳送流量。

當您決定要使用哪個選項時，可管理性、 更精細地控制，以及成本之間就會取捨。

| 選項 | 優點 | 缺點 |
| --- | --- | --- |
| 將 IP 範圍列入允許清單 |沒有額外的成本。<br><br>如需在某個 NSG 中開啟存取權，請使用 **Set-AzureNetworkSecurityRule** Cmdlet。 |複雜而難以管理做為受影響的 IP 範圍隨時間變更。<br><br>提供完整的 Azure，並不只是儲存體的存取。 |
| 使用 HTTP proxy |更精確地控制 proxy 中的儲存體允許的 Url。<br><br>Vm 的單一點的網際網路存取。<br><br>不會隨著 Azure IP 位址變更。 |使用 Proxy 軟體執行 VM 時的額外成本。 |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>將 Azure 資料中心 IP 範圍列入允許清單
若要允許清單的 Azure 資料中心 IP 範圍，請參閱[Azure 網站](http://www.microsoft.com/en-us/download/details.aspx?id=41653)如 IP 範圍和指示的詳細資訊。

您可以使用允許連線到存放裝置的特定區域[服務標記](../virtual-network/security-overview.md#service-tags)。 請確定的規則，可讓您存取儲存體帳戶具有的優先順序高於封鎖網際網路存取的規則。 

![NSG 與區域的儲存體標籤](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> 儲存體標籤僅在特定區域中提供使用，目前仍是預覽狀態。 如需區域的清單，請參閱[儲存體服務標記](../virtual-network/security-overview.md#service-tags)。

### <a name="use-an-http-proxy-for-vm-backups"></a>用於 VM 備份的 HTTP proxy
當您正在備份 VM 時，VM 上的備份延伸模組會傳送至 Azure 儲存體的快照集管理命令使用 HTTPS API。 備用分機號碼將流量路由透過 HTTP proxy，因為它是唯一的公用網際網路存取設定的元件。

> [!NOTE]
> 我們不建議您應該使用的特定 proxy 軟體。 請確定您選擇相容的設定步驟的 proxy 後面。
>
>

下圖的範例顯示使用 HTTP proxy 所需的三個設定步驟：

* 應用程式 VM 路由 proxy VM 透過公用網際網路繫結所有 HTTP 流量。
* Proxy VM 的 Vm 所傳來允許連入流量，虛擬網路中。
* 名為 NSF 鎖定的網路安全性群組必須允許從 proxy VM 輸出的網際網路流量的安全性規則。

若要使用 HTTP proxy 公用網際網路通訊，請完成下列步驟。

> [!NOTE]
> 步驟執行此範例中，使用特定名稱和值。 當您要輸入 （或貼上） 到您的程式碼的詳細資料為您的部署使用的名稱和值。

#### <a name="step-1-configure-outgoing-network-connections"></a>步驟 1： 設定外寄網路連接
###### <a name="for-windows-machines"></a>Windows 電腦
此程序會設定本機系統帳戶的 proxy 伺服器組態。

1. 下載[PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 開啟 Internet Explorer 從提升權限的提示字元執行下列命令：

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. 在 Internet Explorer 中，移至**工具** > **網際網路選項** > **連線** > **LAN 設定**.
4. 請確認系統帳戶的 proxy 設定。 設定 proxy IP 和連接埠。
5. 關閉 Internet Explorer。

下列指令碼會設定全機器 proxy 設定，並會將它用於任何連出的 HTTP 或 HTTPS 流量。 如果您已經設定 proxy 伺服器上目前的使用者帳戶 （而不是本機系統帳戶），請將它們套用到 SYSTEMACCOUNT 使用此指令碼。

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> 如果您在 proxy 伺服器記錄檔中觀察 「 （407） 要求 Proxy 需要驗證 」，請檢查您的驗證已正確設定。
>
>

###### <a name="for-linux-machines"></a>Linux 電腦
在 ```/etc/environment``` 檔案中新增以下文字行：

```
http_proxy=http://<proxy IP>:<proxy port>
```

將下列幾行新增至 ```/etc/waagent.conf``` 檔案：

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>步驟 2: Proxy 伺服器上允許連入連線
1. 在 Proxy 伺服器上，開啟 [Windows 防火牆]。 最簡單的方式來存取防火牆是搜尋**具有進階安全性的 Windows 防火牆**。
2. 在**具有進階安全性的 Windows 防火牆**對話方塊中，以滑鼠右鍵按一下**輸入規則**選取**新規則**。
3. 在 新增輸入規則精靈，在**規則類型**頁面上，選取**自訂**選項，然後選取**下一步**。
4. 在**程式**頁面上，選取**所有程式**選取**下一步**。
5. 在**通訊協定和連接埠**頁面上，輸入下列資訊並選取**下一步**:
   * 如**通訊協定類型**，選取**TCP**。
   * 如**本機連接埠**，選取**特定連接埠**。 在下列方塊中，指定已設定的 proxy 連接埠數目。
   * 如**遠端連接埠**，選取**所有連接埠**。

針對精靈的其餘部分，接受預設設定，直到到達結束。 然後此規則的名稱命名。 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>步驟 3： 新增至 NSG 例外規則
下列命令會新增 NSG 例外狀況。 這個例外狀況允許 TCP 流量從 10.0.0.5 上任何連接埠 80 (HTTP) 或 443 (HTTPS) 連接埠上的任何網際網路位址。 如果您需要公用網際網路上的特定連接埠，請務必新增至該通訊埠```-DestinationPortRange```。

在 Azure PowerShell 命令提示字元中，輸入下列命令：

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>有疑問嗎？
如果您有問題，或如果沒有任何功能，您想要包含在內，請參閱 <<c0> [ 傳送意見反應](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>後續步驟
您現在已經備妥環境來備份您的 VM，您的下一個邏輯步驟是建立備份。 規劃文章會提供備份 VM 的詳細資訊。

* [備份虛擬機器](backup-azure-arm-vms.md)
* [規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)
* [管理虛擬機器備份](backup-azure-manage-vms.md)
