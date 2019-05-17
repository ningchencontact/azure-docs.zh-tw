---
title: 在 Azure Vm 中的 SQL Server 資料庫備份 |Microsoft Docs
description: 了解如何在 Azure Vm 中的 SQL Server 資料庫備份
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: ae1f5f9148fa516c98d78afdd57887d4279f92dc
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827676"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>備份 Azure VM 中的 SQL Server 資料庫

SQL Server 資料庫是重要的工作負載所需的低復原點目標 (RPO) 和長期保留。 您可以備份使用的 Azure 虛擬機器 (Vm) 上執行的 SQL Server 資料庫[Azure 備份](backup-overview.md)。

本文說明如何備份至 Azure 備份復原服務保存庫的 Azure VM 執行的 SQL Server 資料庫。

在本文中，您將了解如何：

> [!div class="checklist"]
> * 建立和設定保存庫。
> * 探索資料庫，並設定備份。
> * 為資料庫設定自動保護。


## <a name="prerequisites"></a>必要條件

您將 SQL Server 資料庫備份之前，請檢查下列條件：

1. 識別或建立[復原服務保存庫](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)在相同的區域或地區設定為裝載 SQL Server 執行個體的 VM。
2. 請檢查[VM 所需的權限](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)來備份 SQL 資料庫。
3. 請確認 VM 已[網路連線](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。
4. 請確定 SQL Server 資料庫遵循[之 Azure 備份的資料庫命名方針](#database-naming-guidelines-for-azure-backup)。
5. 確認沒有其他啟用資料庫的備份解決方案。 您的資料庫備份之前，請停用所有其他 SQL Server 備份。

> [!NOTE]
> 您可以在 Azure VM，也不會發生衝突的 VM 上執行的 SQL Server 資料庫，讓 Azure 備份。


### <a name="establish-network-connectivity"></a>建立網路連線

所有作業，SQL Server VM 會需要連線到 Azure 公用 IP 位址。 （資料庫探索、 設定備份、 排程備份、 還原復原點等等） 的 VM 作業失敗，而不需要連線到 Azure 的公用 IP 位址。

使用下列選項之一，以建立連線：

- **允許 Azure datacenter IP 範圍**。 此選項可讓[IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)下載中。 若要存取的網路安全性群組 (NSG)，使用 Set-azurenetworksecurityrule cmdlet。 如果您是設為允許清單僅特定地區的 Ip，您將也需要列入白名單 Azure Active Directory (Azure AD) 服務標籤來啟用驗證。

- **允許存取使用 NSG 標記**。 如果您使用 Nsg 來限制連線能力時，此選項會將您使用 AzureBackup 標記可讓 Azure 備份的輸出存取的 NSG 規則。 除了這個標記中，您也需要對應[規則](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags)適用於 Azure AD 和 Azure 儲存體，以允許連線的驗證和資料傳輸。 AzureBackup 標記只是目前可用的 powershell。 若要使用 AzureBackup 標記建立規則：

    - 新增 Azure 帳戶認證，並更新國家/地區雲端<br/>
    `Add-AzureRmAccount`

    - 選取 NSG 訂用帳戶<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - 選取 NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - 新增可讓 Azure 備份服務標記的輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - 儲存 NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **允許存取使用的 Azure 防火牆標記**。 如果您使用 Azure 防火牆，建立應用程式規則使用 AzureBackup [FQDN 標記](https://docs.microsoft.com/en-us/azure/firewall/fqdn-tags)。 這可讓 Azure 備份的輸出存取。
- **部署 HTTP proxy 伺服器路由傳送流量**。 當您在 Azure VM 備份的 SQL Server 資料庫時，在 VM 上的備份擴充功能會使用 HTTPS Api 將管理命令傳送至 Azure 備份 」 和 「 Azure 儲存體的資料。 備份擴充功能也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 延伸模組是已針對至公用網際網路存取之唯一元件。

連線能力選項包括下列的優點和缺點：

**選項** | **優點** | **缺點**
--- | --- | ---
允許 IP 範圍 | 不需要額外費用 | 若要管理的 IP 位址範圍會隨著時間變更的複雜 <br/><br/> 提供整個 Azure 中，不只是 Azure 儲存體的存取
使用 NSG 服務標籤 | 更輕鬆地管理範圍的變更會自動合併 <br/><br/> 不需要額外費用 <br/><br/> | 可以只使用具有 Nsg <br/><br/> 提供整個服務的存取權
使用 Azure 防火牆 FQDN 標記 | 容易管理，自動管理所需的 Fqdn | 可以只使用與 Azure 防火牆
使用 HTTP Proxy | 更精確地控制在 proxy 中的儲存體允許 Url <br/><br/> 單一點的網際網路存取 Vm <br/><br/> 不受 Azure IP 位址變更 | 使用 proxy 軟體執行 VM 的額外成本

### <a name="set-vm-permissions"></a>設定 VM 權限

當您設定 SQL Server 資料庫的備份時，Azure 備份會執行下列動作：

- 新增 AzureBackupWindowsWorkload 延伸模組。
- 建立 NT SERVICE\AzureWLBackupPluginSvc 帳戶來探索虛擬機器上的資料庫。 此帳戶用於備份和還原需要 SQL 系統管理員權限。
- 探索資料庫為虛擬機器上執行的 Azure 備份會使用 NT AUTHORITY\SYSTEM 帳戶。 此帳戶必須是公用登入 SQL。

如果您未在 Azure Marketplace 中建立 SQL Server VM，您可能會收到 UserErrorSQLNoSysadminMembership 時發生錯誤。 如需詳細資訊，請參閱功能項考量與限制一節中找到[有關在 Azure Vm 中 SQL Server 備份](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)。

### <a name="database-naming-guidelines-for-azure-backup"></a>命名指導方針適用於 Azure 備份的資料庫

避免在資料庫名稱中使用下列項目：

  * 結尾與開頭空白
  * 尾端驚嘆號 （！）
  * 右方括弧 (])
  * 開頭為 F:\

別名功能適用於不支援的字元，但我們建議您避免它們。 如需詳細資訊，請參閱 [了解表格服務資料模型](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)。


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>探索 SQL Server 資料庫

如何探索的 VM 上執行的資料庫：

1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您用來備份資料庫的復原服務保存庫。

2. 在 **復原服務保存庫**儀表板，選取**備份**。

   ![選取 [備份] 以開啟 [備份目標] 功能表](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在**備份目標**，將**地方執行您的工作負載？** 來**Azure**。

4. 在 [您要備份什麼?] 中，選取 [Azure VM 中 SQL Server]。

    ![選取 Azure VM 中的 SQL Server 來進行備份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. 在 [備份目標] > [探索 VM 中的 DB] 中，選取 [開始探索] 以搜尋訂用帳戶中未受保護的 VM。 此搜尋可能需要一段時間，視受保護的訂用帳戶中的 Vm 數目而定。

   - 在探索之後，未受保護的 VM 應該會出現在清單中，並依照名稱和資源群組列出。
   - 如果 VM 未列出您所預期，請參閱是否已經備份保存庫中。
   - 多個 Vm 可以有相同的名稱，但它們會屬於不同的資源群組。

     ![在搜尋 VM 中的 DB 期間會將備份擱置](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 清單中，選取執行 SQL Server 資料庫的 VM，然後選取 [探索資料庫]。

7. 追蹤資料庫探索**通知**。 此動作所需的時間取決於 VM 的資料庫數目。 探索到選取的資料庫之後，即會出現成功訊息。

    ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure 備份會探索 VM 上的所有 SQL Server 資料庫。 在探索期間的下列項目會在背景進行：

    - Azure 備份來備份工作負載在保存庫註冊 VM。 已註冊的 VM 上的所有資料庫可以都備份到只在此保存庫。
    - Azure 備份會在 VM 上安裝 AzureBackupWindowsWorkload 延伸模組。 在 SQL database 上不安裝任何代理程式。
    - Azure 備份會在 VM 上建立的服務帳戶 NT Service\AzureWLBackupPluginSvc。
      - 所有備份和還原作業都會使用此服務帳戶。
      - NT Service\AzureWLBackupPluginSvc 需要 SQL 系統管理員權限。 在 Marketplace 中建立的所有 SQL Server Vm 都隨附安裝 SqlIaaSExtension。 AzureBackupWindowsWorkload 延伸模組會使用 SQLIaaSExtension，自動取得必要的權限。
    - 如果您未從 Marketplace 建立 VM，VM 將不會有 SqlIaaSExtension 安裝，並探索作業會失敗並出現錯誤訊息 UserErrorSQLNoSysAdminMembership。 若要修正此問題，請遵循[指示](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)。

        ![選取 VM 和資料庫](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>設定備份  

1. 在 **備份目標** > **步驟 2:設定備份**，選取**設定備份**。

   ![選取 [設定備份]](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. 在 **選取要備份項目**，您會看到所有已註冊的可用性群組和獨立 SQL Server 執行個體。 選取左側的所有未受保護的資料庫清單展開該執行個體或 Alwayson 可用性群組中的資料列的箭號。  

    ![顯示所有具備獨立資料庫的 SQL Server 執行個體](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 選擇您想要保護，然後選取的所有資料庫**確定**。

   ![保護資料庫](./media/backup-azure-sql-database/select-database-to-protect.png)

   為了將備份負載最佳化，Azure 備份會將每個備份作業的資料庫數目上限設為 50。

     * 若要保護 50 個以上的資料庫，請設定多個備份。
     * 若要啟用[](#enable-auto-protection)整個執行個體或 Alwayson 可用性群組。 在  **AUTOPROTECT**下拉式清單中，選取**ON**，然後選取**確定**。
     
    > [!NOTE]
    > [自動保護](#enable-auto-protection)功能不僅可讓所有現有的資料庫保護，但也會自動保護任何新的資料庫新增至該執行個體或可用性群組。  

4. 選取  **確定**來開啟**備份原則**。

    ![啟用 Always On 可用性群組的自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

5. 在 **備份原則**，選擇原則，然後選取 **確定**。

   - 選取的預設原則做為 HourlyLogBackup。
   - 選擇之前為 SQL 建立的現有備份原則。
   - 定義新的原則，根據您的 RPO 和保留範圍。

     ![選取備份原則](./media/backup-azure-sql-database/select-backup-policy.png)

6. 在  **備份**，選取 **啟用備份**。

    ![啟用所選備份原則](./media/backup-azure-sql-database/enable-backup-button.png)

7. 組態中追蹤進度 **通知** 入口網站的區域。

    ![[通知] 區域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。

- 原則會建立於保存庫層級上。
- 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。
- 建立備份原則時，預設值是每日完整備份。
- 您可以新增差異備份，但僅限於將完整備份設定為每週進行時。
- 深入了解[不同類型的備份原則](backup-architecture.md#sql-server-backup-types)。

若要建立備份原則：

1. 在 保存庫中，選取**備份原則** > **新增**。
2. 在 **新增**，選取**Azure VM 中的 SQL Server**來定義的原則類型。

   ![針對新備份原則選擇原則類型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在 [原則名稱] 中，為新原則輸入名稱。
4. 在 **完整的備份原則**，選取**備份頻率**。 選擇 **每天**或是**每週**。

   - 若選擇 [每日]，請選取備份作業的開始時間和時區。
   - 若選擇 [每週]，請選取備份作業的開始日期 (星期幾)、時間和時區。
   - 執行完整備份，因為您無法關閉**完整備份**選項。
   - 選取 **完整備份**檢視原則。
   - 您無法為每日完整備份建立差異備份。

     ![新的備份原則欄位](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 在 **保留範圍**，預設會選取所有選項。 清除任何保留範圍會限制您不想，，，然後設定 若要使用的間隔。

    - 任何類型 （完整、 差異及記錄檔） 的最小保留期限是備份的七天。
    - 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    - 備份標記，保留特定日期根據每週的保留範圍和每週的保留設定。
    - 每月和每年的保留範圍的行為類似的方式。

       ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. 在 [完整備份原則] 功能表中，選取 [確定] 接受設定。
7. 若要新增差異備份原則，請選取 [差異備份]。

   ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![開啟差異備份原則功能表](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。

    - 您可以觸發每天只能有一個差異備份。
    - 差異備份最多可以保留 180 天。 來延長保留時間，使用完整備份。

9. 選取 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。

10. 若要新增交易記錄備份原則，請選取 [記錄備份]。
11. 在 [記錄備份] 中選取 [啟用]，然後設定頻率和保留控制項。 記錄備份可以發生的頻率也以每隔 15 分鐘，而且可以保留最多 35 天。
12. 選取 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。

    ![編輯記錄備份原則](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在 [備份原則] 功能表上，選擇是否要啟用 [SQL 備份壓縮]。
    - 預設會停用壓縮。
    - Azure 備份在後端使用 SQL 原生備份壓縮。

14. 完成備份原則的編輯之後，請選取 [確定]。


### <a name="modify-policy"></a>修改原則
修改原則來變更備份頻率] 或 [保留範圍。

> [!NOTE]
> 在保留期限內的任何變更將反而要套用至所有除了新的較舊復原點。

在保存庫儀表板中，移至**管理** > **備份原則**，然後選擇您想要編輯的原則。

  ![管理備份原則](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>啟用自動保護  

您可以啟用自動保護的獨立 SQL Server 執行個體或 Alwayson 可用性群組，自動備份所有現有和未來的資料庫。

- 您可以一次選取的自動保護的資料庫數目沒有限制。
- 您無法選擇性地保護，或排除保護執行個體中啟用自動保護的資料庫。
- 如果您的執行個體已經包含一些受保護的資料庫，它們會持續受到保護，在其各自的原則下的情況，即使在您開啟自動保護。 所有未受保護的資料庫，稍後再加入必須只有單一原則，將您在啟用自動保護，底下所列的時間會定義 **設定備份**。 不過，您可以變更自動保護的資料庫與更新版本相關聯的原則。  

若要啟用自動保護：

  1. 在 [要備份的項目] 中，選取要啟用自動保護的執行個體。
  2. 選取下拉式清單底下**AUTOPROTECT**，選擇**ON**，然後選取**確定**。

      ![啟用可用性群組的自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. 所有資料庫會一起設定備份，且您可在 [備份作業] 中加以追蹤。

如果您要停用自動保護，請選取該執行個體名稱**設定備份**，然後選取**停用 Autoprotect**執行個體。 所有資料庫會都繼續備份，但未來的資料庫不會自動受到保護。

![停用該執行個體上自動保護](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>後續步驟

了解如何：

- [還原備份 SQL Server 資料庫](restore-sql-database-azure-vm.md)
- [管理備份 SQL Server 資料庫](manage-monitor-sql-database-backup.md)
