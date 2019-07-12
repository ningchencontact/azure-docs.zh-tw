---
title: 備份和還原已加密的 Azure Vm 使用 Azure 備份
description: 描述如何備份及還原與 Azure 備份服務的已加密的 Azure Vm。
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geg
ms.openlocfilehash: 24ae6ddae30110f6d125158d6f2744bf4eae5006
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704982"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>備份及還原加密的 Azure VM

這篇文章會說明如何備份及還原 Windows 或 Linux Azure 虛擬機器 (Vm)，以使用加密的磁碟[Azure 備份](backup-overview.md)服務。

如果您想要深入了解 Azure 備份的互動方式使用 Azure Vm 在開始之前，請檢閱這些資源：

- [檢閱](backup-architecture.md#architecture-direct-backup-of-azure-vms)Azure VM 備份架構。
- [深入了解](backup-azure-vms-introduction.md)Azure VM 備份和 Azure 備份擴充功能。

## <a name="encryption-support"></a>加密支援

Azure 備份支援有加密其 OS/資料磁碟與 Azure 磁碟加密 (ADE) 的 Azure Vm 的備份。 ADE 使用 BitLocker 來加密 Windows Vm 的 dm-crypt 功能適用於 Linux Vm。 ADE 可與 Azure Key Vault 來管理磁碟加密金鑰和祕密整合。 Key Vault 金鑰加密金鑰 (Kek) 可用來新增額外的安全性，加密加密密碼寫入金鑰保存庫之前。

Azure 備份可以備份和還原使用 ADE，而 Azure AD 應用程式的 Azure Vm 下, 表摘要說明。

**VM 磁碟類型** | **ADE (BEK/dm-crypt)** | **ADE 和 kek 加密**
--- | --- | ---
**未受管理** | 是 | 是
**受管理**  | 是 | 是

- 深入了解[ADE](../security/azure-security-disk-encryption-overview.md)， [Key Vault](../key-vault/key-vault-overview.md)，並[Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)。
- 讀取[常見問題集](../security/azure-security-disk-encryption-faq.md)Azure VM 磁碟加密。



### <a name="limitations"></a>限制

- 您可以備份及還原加密的 Vm 位於相同的訂用帳戶和區域內。
- Azure 備份支援使用獨立金鑰加密的 Vm。 目前不支援任何金鑰，這是用來將 VM 加密的憑證的一部分。
- 您可以備份及還原加密的 Vm 位於相同訂用帳戶和區域與復原服務備份保存庫中。
- 加密的 VM 無法在檔案/資料夾層級復原。 您需要復原整個 VM 還原檔案和資料夾。
- 當還原 VM，您不能使用[取代現有的 VM](backup-azure-arm-restore-vms.md#restore-options)已加密 Vm 的選項。 此選項只有在未加密的受控磁碟上執行。




## <a name="before-you-start"></a>開始之前

開始之前，請執行下列作業：

1. 請確定您有一或多個[Windows](../security/azure-security-disk-encryption-windows.md)或是[Linux](../security/azure-security-disk-encryption-linux.md) ADE Vm 已啟用。
2. [請檢閱支援對照表](backup-support-matrix-iaas.md)Azure VM 備份
3. [建立](backup-azure-arm-vms-prepare.md#create-a-vault)如果您沒有帳戶，復原服務備份保存庫。
4. 如果您啟用已啟用備份的 Vm 的加密，您只需要提供備份，讓備份可以不中斷地繼續存取 Key Vault 的權限。 [了解更多](#provide-permissions)需指派這些權限。

此外，有一些您可能需要在某些情況下執行的事項：

- **在 VM 上安裝 VM 代理程式**:為機器上執行的 Azure VM 代理程式安裝擴充功能，以 Azure 備份來備份 Azure VM。 如果您的 VM 建立自 Azure marketplace 映像，代理程式已安裝並執行。 如果您建立自訂的 VM，或在內部部署機器移轉，您可能需要[手動安裝代理程式](backup-azure-arm-vms-prepare.md#install-the-vm-agent)。
- **明確允許輸出存取**:一般而言，您不需要明確允許輸出網路存取的 Azure VM，才能讓它與 Azure 備份通訊。 不過，某些 Vm 可能會遇到連線問題，顯示**ExtensionSnapshotFailedNoNetwork**嘗試連線時發生錯誤。 如果發生這種情況，您應該[明確允許輸出存取](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access)，所以 Azure 備份擴充功能可以與 Azure 的備份流量的公用 IP 位址通訊。



## <a name="configure-a-backup-policy"></a>設定備份原則

1. 如果您尚未建立復原服務備份保存庫，請遵循[這些指示](backup-azure-arm-vms-prepare.md#create-a-vault)
2. 在入口網站中，開啟保存庫，然後選取**備份**中**開始使用**一節。

    ![備份刀鋒視窗](./media/backup-azure-vms-encryption/select-backup.png)

3. 在 **備份目標** > **地方執行您的工作負載？** 選取**Azure**。
4. 在 **您想要備份什麼？** 選取**虛擬機器** > **確定**。

      ![案例刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 在 **備份原則** > **選擇備份原則**，選取您要向保存庫相關聯的原則。 然後按一下 [確定]  。
    - 備份原則會指定包含執行備份，並儲存的時間長度。
    - 預設原則的詳細資料便會列在下拉式功能表之下。

    ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 如果您不想要使用的預設原則，請選取**新建**，並[建立自訂原則](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。


7. 選擇您想要使用選取的原則，備份加密的 Vm，然後選取**確定**。

      ![選取加密的 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 如果您使用 Azure 金鑰保存庫，在保存庫 頁面上，您會看到一則訊息，Azure 備份需要金鑰和祕密金鑰保存庫中的唯讀存取。

    - 如果您收到此訊息時，不不需要任何動作。

        ![存取 OK](./media/backup-azure-vms-encryption/access-ok.png)

    - 如果您收到這則訊息，您需要設定權限，如中所述[下列程序](#provide-permissions)。

        ![存取警告](./media/backup-azure-vms-encryption/access-warning.png)

9. 按一下 **啟用備份**部署中保存庫的備份原則，並為選取的 Vm 啟用備份。


## <a name="trigger-a-backup-job"></a>觸發備份作業

初始備份會依照排程進行，但您可以執行它立即，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]  。
2. 在 [備份項目]  中，按一下 [Azure 虛擬機器]  。
3. 在 **備份項目**清單中，按一下省略符號 （...）。
4. 按一下 [立即備份]  。
5. 在 **立即備份**，使用日曆控制項選取的復原點保留的最後一天。 然後按一下 [確定]  。
6. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業]   > [進行中]  。 根據您的 VM 大小，建立初始備份可能需要花一點時間。


## <a name="provide-permissions"></a>提供權限

Azure VM 需要備份的金鑰和密碼，以及相關聯的 Vm 的唯讀存取。

- 您的 Key Vault 是 Azure 訂用帳戶的 Azure AD 租用戶相關聯。 如果你**成員使用者**，Azure 備份取得金鑰保存庫不需要進一步動作的存取。
- 如果你**來賓使用者**，您必須提供存取金鑰保存庫的 Azure 備份的權限。

若要設定權限：

1. 在 Azure 入口網站中，選取**所有的服務**，然後搜尋**金鑰保存庫**。
2. 選取您要備份加密的 VM 相關聯的金鑰保存庫。
3. 選取 **存取原則** > **新增**。
4. 選取 **選取主體**，然後輸入**備份管理**。
5. 選取 **備份管理服務** > **選取**。

    ![備份服務選取項目](./media/backup-azure-vms-encryption/select-backup-service.png)

6. 在 **新增存取原則** > **從範本 （選擇性） 設定**，選取**Azure 備份**。
    - 該備份會在 [金鑰權限]  和 [祕密權限]  預先填入必要的權限。
    - 如果您的 VM 會使用加密**僅限 BEK**，移除選取的項目**金鑰權限**因為您只需要祕密權限。

    ![Azure 備份選取項目](./media/backup-azure-vms-encryption/select-backup-template.png)

6. 按一下 [確定]  。 **備份管理服務**新增至**存取原則**。

    ![存取原則](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. 按一下 **儲存**為 Azure 備份提供權限。

## <a name="restore-an-encrypted-vm"></a>還原已加密的 VM

您將還原已加密的 Vm，如下所示：

1. [還原 VM 磁碟](backup-azure-arm-restore-vms.md#restore-disks)。
2. 然後執行下列其中一項：
    - 使用自訂 VM 設定，以及觸發 VM 部署在還原作業期間所產生的範本。 [深入了解](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    - 從還原的磁碟使用 Powershell 建立新的 VM。 [深入了解](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

## <a name="next-steps"></a>後續步驟

如果您遇到任何問題，請檢閱

- [常見的錯誤](backup-azure-vms-troubleshoot.md)加密時，備份和還原 Azure Vm。
- [Azure VM 代理程式/備份擴充功能](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)問題。
