---
title: 協助保護使用 Azure 備份之雲端工作負載的安全性功能
description: 瞭解如何使用 Azure 備份中的安全性功能，讓備份更加安全。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: b882b8ee08c38b6313558916ab46f80ce9dd5130
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129343"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>協助保護使用 Azure 備份之雲端工作負載的安全性功能

現在越來越重視安全性問題，例如惡意程式碼、勒索軟體和入侵。 這些安全性問題在成本和資料方面付出的代價很高。 為了防範這類攻擊，Azure 備份現在提供安全性功能來協助保護備份資料，即使在刪除後也是如此。 其中一項功能就是虛刪除。 使用虛刪除時，即使惡意執行者刪除 VM 的備份（或不小心刪除備份資料），備份資料仍會保留14個額外的天數，讓該備份專案不會遺失資料。 在「虛刪除」狀態中，備份資料的額外14天保留期不會對客戶產生任何費用。

> [!NOTE]
> 虛刪除只會保護已刪除的備份資料。 如果 VM 在沒有備份的情況下刪除，虛刪除功能將不會保留資料。 所有資源都應該使用 Azure 備份來保護，以確保完整的復原能力。
>

## <a name="soft-delete"></a>虛刪除

### <a name="supported-regions"></a>支援區域

美國中西部、東亞、加拿大中部、加拿大東部、法國中部、法國南部、韓國中部、南韓南部、英國南部、英國西部、澳大利亞東部、澳大利亞東南部、北歐、美國西部、West 美國2、美國中部、南部，目前支援虛刪除東亞、美國中北部、美國中南部、日本東部、日本西部、印度南部、印度中部、印度西部、美國東部2、瑞士北部、瑞士西部和所有國家地區。

### <a name="soft-delete-for-vms"></a>Vm 的虛刪除

1. 為了刪除 VM 的備份資料，必須停止備份。 在 Azure 入口網站中，移至您的復原服務保存庫，以滑鼠右鍵按一下備份專案，然後選擇 [**停止備份**]。

   ![Azure 入口網站備份專案的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下列視窗中，您將會獲得刪除或保留備份資料的選項。 如果您選擇 [**刪除備份資料**] 然後**停止備份**，將不會永久刪除 VM 備份。 相反地，備份資料會保留14天的「虛刪除」狀態。 如果選擇 [**刪除備份資料**]，則會將刪除電子郵件警示傳送至設定的電子郵件識別碼，通知使用者14天保留備份資料的延伸保留期。 此外，第12天會傳送電子郵件警示，通知您有兩天的時間來 resurrect 已刪除的資料。 刪除作業會延後到15天后，將會進行永久刪除，並傳送最終的電子郵件警示來通知永久刪除資料。

   ![[停止備份] 畫面 Azure 入口網站的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在這14天的復原服務保存庫中，虛刪除的 VM 旁會出現紅色的「虛刪除」圖示。

   ![Azure 入口網站的螢幕擷取畫面，VM 處於虛刪除狀態](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保存庫中有任何虛刪除的備份專案，就無法在該時間刪除保存庫。 請在永久刪除備份專案之後嘗試刪除保存庫，而且保存庫中沒有任何已虛刪除狀態的專案。

4. 若要還原已虛刪除的 VM，則必須先將它取消刪除。 若要取消刪除，請選擇虛刪除的 VM，然後按一下 [取消**刪除**] 選項。

   ![Azure 入口網站、取消刪除 VM 的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   將會出現一個警告，指出如果選擇 [取消刪除]，將會取消刪除 VM 的所有還原點，並可執行還原作業。 VM 將會保留在「停止保護並保留資料」狀態，並暫停備份並保留備份資料，且不會有任何備份原則生效。

   ![Azure 入口網站的螢幕擷取畫面，確認刪除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此時，您也可以從選擇的還原點選取 [**還原 vm** ] 來還原 vm。  

   ![Azure 入口網站，Restore VM 選項的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在使用者執行**繼續備份**作業之後，垃圾收集行程才會執行並清除過期的復原點。

5. 在取消刪除程式完成之後，狀態會回到 [停止備份並保留資料]，然後您可以選擇 [**繼續備份**]。 [**繼續備份**] 作業會將備份專案帶回 [作用中] 狀態，並與定義備份和保留排程的使用者所選取的備份原則相關聯。

   ![[繼續備份] 選項 Azure 入口網站的螢幕擷取畫面](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程圖會顯示備份專案的不同步驟和狀態：

![虛刪除之備份專案的生命週期](./media/backup-azure-security-feature-cloud/lifecycle.png)

如需詳細資訊，請參閱下面的[常見問題一節](backup-azure-security-feature-cloud.md#frequently-asked-questions)。

## <a name="other-security-features"></a>其他安全性功能

### <a name="storage-side-encryption"></a>儲存端加密

Azure 儲存體會在將資料保存到雲端時，自動將其加密。 加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 Azure 儲存體中的資料會使用256位 AES 加密（可用的最強區塊密碼之一），以透明的方式進行加密和解密，且符合 FIPS 140-2 標準。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。 Azure 備份會在儲存前自動加密資料。 Azure 儲存體會在擷取資料前進行解密。  

在 Azure 中，Azure 儲存體與保存庫之間傳輸中的資料會受到 HTTPS 保護。 此資料會保持在 Azure 中樞網路上。

如需詳細資訊，請參閱待用[資料的 Azure 儲存體加密](https://docs.microsoft.com/en-in/azure/storage/common/storage-service-encryption)。

### <a name="vm-encryption"></a>VM 加密

您可以使用 Azure 備份服務，備份及還原具有加密磁片的 Windows 或 Linux Azure 虛擬機器（Vm）。 如需指示，請參閱[使用 Azure 備份備份和還原已加密的虛擬機器](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption)。

### <a name="protection-of-azure-backup-recovery-points"></a>保護 Azure 備份復原點

復原服務保存庫所使用的儲存體帳戶會被隔離，而且使用者無法存取以因應任何惡意用途。 只有 Azure 備份管理作業（例如 restore）允許存取。 這些管理作業是透過以角色為基礎的存取控制（RBAC）來控制。

如需詳細資訊，請參閱[使用以角色為基礎的存取控制來管理 Azure 備份復原點](https://docs.microsoft.com/en-us/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="soft-delete"></a>虛刪除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>我是否需要在每個保存庫上啟用虛刪除功能？

否，預設會針對所有復原服務保存庫建立並啟用它。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>我可以設定在刪除作業完成後，我的資料會保留在虛刪除狀態的天數嗎？

否，在刪除作業之後，它會固定為14天的額外保留期。
 
#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>我是否需要支付此額外14天保留期的費用？

否，這是14天的額外保留空間，不是虛刪除功能的一部分。
 
#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>當我的資料處於虛刪除狀態時，可以執行還原作業嗎？

否，您必須取消刪除已虛刪除的資源，才能還原。 取消刪除作業會將資源恢復為 [**停止保護並保留資料] 狀態**，讓您可以還原到任何時間點。 在此狀態下，垃圾收集行程會保持暫停。
 
#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>我的快照集會遵循與保存庫中的復原點相同的生命週期嗎？

是的。
 
#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何針對虛刪除的資源，再次觸發排程備份？

取消刪除後接著繼續作業將會再次保護資源。 Resume 作業會建立備份原則的關聯，以觸發已選取保留期限的排程備份。 此外，一旦繼續作業完成，垃圾收集行程就會立即執行。 如果您想要從已超過到期日的復原點執行還原，建議您在觸發繼續作業之前先執行此操作。
 
#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保存庫中有虛刪除的專案，是否可以刪除我的保存庫？

如果保存庫中有已虛刪除狀態的備份專案，則無法刪除復原服務保存庫。 刪除作業14天后，會永久刪除虛刪除的專案。 只有在清除所有已虛刪除的專案之後，才能刪除保存庫。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>刪除之後，我可以刪除14天之前的資料嗎？

資料分割 您不能強制刪除虛刪除的專案，它們會在14天之後自動刪除。 這項安全性功能可防止意外或惡意刪除已備份的資料。  在 VM 上執行任何其他動作之前，您應該先等待14天。  虛刪除的專案將不會收費。  如果您需要在14天內重新保護標示為要虛刪除的 Vm 至新的保存庫，請洽詢 Microsoft 支援服務。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>可以在 PowerShell 或 CLI 中執行虛刪除作業嗎？

否，目前無法使用 PowerShell 或 CLI 的支援。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>是否支援其他雲端工作負載的虛刪除，例如 Azure Vm 中的 SQL Server 和 Azure Vm 中的 SAP Hana？

資料分割 目前只有 Azure 虛擬機器支援虛刪除。

## <a name="next-steps"></a>後續步驟

* 閱讀[Azure 備份的安全性控制](backup-security-controls.md)。
