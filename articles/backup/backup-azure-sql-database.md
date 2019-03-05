---
title: 將 SQL Server 資料庫備份至 Azure | Microsoft Docs
description: 本教學課程說明如何將 SQL Server 備份至 Azure。 本文也將說明 SQL Server 復原。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 61219fc4e1fc329708a7e58ee6a293e4e25cca31
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887806"
---
# <a name="back-up-sql-server-databases-on-azure-vms"></a>將 SQL Server 資料庫備份到 Azure VM 

SQL Server 資料庫是需要低復原點目標 (RPO) 和長期保留的重要工作負載。 您可以使用 [Azure 備份](backup-overview.md)來備份在 Azure VM 上執行的 SQL Server 資料庫。 

本文說明如何將執行於 Azure VM 上的 SQL Server 資料庫備份至 Azure 備份復原服務保存庫。 在本文中，您將了解：

> [!div class="checklist"]
> * 確認備份 SQL Server 執行個體的必要條件。
> * 建立和設定保存庫。
> * 探索資料庫，並設定備份。
> * 為資料庫設定自動保護。

> [!NOTE]
> 此功能目前為公開預覽狀態。

## <a name="before-you-start"></a>開始之前

開始之前，請確認下列事項：

1. 請確定您具有在 Azure 中執行的 SQL Server 執行個體。 您可以在 Marketplace 中[快速建立 SQL Server 執行個體](../sql-database/sql-database-get-started-portal.md)。
2. 檢閱下列公開預覽限制。
3. 檢閱案例支援。
4. [檢閱關於此案例的常見問題](faq-backup-sql-server.md)。


## <a name="preview-limitations"></a>預覽限制

此公開預覽有一些限制。

- 執行 SQL Server 的 VM 需要網際網路連線才能存取 Azure 公用 IP 位址。 
- 您最多可在保存庫中備份 2000 個 SQL Server 資料庫。 如果您有更多資料庫，請建立另一個保存庫。 
- [分散式可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017)的備份無法完整運作。
- 不支援備份 SQL Server Always On 容錯移轉叢集執行個體 (FCI)。
- SQL Server 備份應在入口網站中設定。 目前無法使用 Azure PowerShell、CLI 或 REST API 來設定備份。
- 不支援 FCI 鏡像資料庫、資料庫快照集及資料庫的備份和還原作業。
- 無法保護含有大量檔案的資料庫。 支援的檔案數目上限不確定。 此數目取決於檔案數量和檔案的路徑長度。 

檢閱關於備份 SQL Server 資料庫的[常見問題](faq-backup-sql-server.md)。
## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的部署** | 支援 SQL Marketplace Azure VM 和非 Marketplace (手動安裝 SQL Server) VM。
**支援的地區** | 澳大利亞東南部 (ASE)；巴西南部 (BRS)；加拿大中部 (CNC)；加拿大東部 (CE)；美國中部 (CUS)；東亞 (EA)；澳大利亞東部 (AE)；美國東部 (EUS)；美國東部 2 (EUS2)；印度中部 (INC)；印度南部 (INS)；日本東部 (JPE)；日本西部 (JPW)；南韓中部 (KRC)；南韓南部 (KRS)；美國中北部 (NCUS)；北歐 (NE)；美國中南部 (SCUS)；東南亞 (SEA)；英國南部 (UKS)；英國西部 (UKW)；美國中西部 (WCUS)；歐洲西部 (WE)；美國西部 (WUS)；美國西部 2 (WUS 2)
**受支援的作業系統** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012<br/><br/> 目前不支援 Linux。
**支援的 SQL Server 版本** | SQL Server 2017、SQL Server 2016、SQL Server 2014、SQL Server 2012。<br/><br/> Enterprise、Standard、Web、Developer、Express。


## <a name="prerequisites"></a>必要條件

備份 SQL Server 資料庫之前，請先檢查下列條件：

1. 在與裝載 SQL Server 執行個體的 VM 相同的區域或地區設定中識別或[建立](backup-azure-sql-database.md#create-a-recovery-services-vault)復原服務保存庫。
2. 檢查備份 SQL 資料庫所需的 [VM 權限](#fix-sql-sysadmin-permissions)。
3. 確認 VM 具有[網路連線](backup-azure-sql-database.md#establish-network-connectivity)。
4. 確認 SQL Server 資料庫的命名符合 Azure 備份的[命名方針](backup-azure-sql-database.md)。
5. 確認您未針對資料庫啟用任何其他備份解決方案。 在設定此案例前，請停用所有其他的 SQL Server 備份。 您可以同時為 Azure VM 以及在該 VM 上執行的 SQL Server 資料庫啟用 Azure 備份，而不會發生任何衝突。


### <a name="establish-network-connectivity"></a>建立網路連線

要執行任何作業時，SQL Server VM 虛擬機器都必須連線至 Azure 公用 IP 位址。 若未連線至公用 IP 位址，VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 將會失敗。 請透過下列其中一個選項建立連線：

- **允許 Azure 資料中心 IP 範圍**：允許下載中的 [IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 如需網路安全性群組 (NSG) 中的存取權，請使用 **Set-AzureNetworkSecurityRule** Cmdlet。
- **部署 HTTP Proxy 伺服器以路由流量**：當您在 Azure VM 上備份 SQL Server 資料庫時，VM 上的備份擴充功能會使用 HTTPS API 將管理命令傳送至 Azure 備份，並將資料傳送至 Azure 儲存體。 備份延伸模組也會使用 Azure Active Directory (Azure AD) 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 延伸模組是唯一為了要存取公用網際網路而設定的元件。

每個選項各有其優缺點

**選項** | **優點** | **缺點**
--- | --- | ---
允許 IP 範圍 | 沒有額外的成本。 | 由於 IP 位址範圍會隨著時間改變，因此難以管理。 <br/><br/> 提供整個 Azure 的存取權，而不只是 Azure 儲存體的存取權。
使用 HTTP Proxy   | 允許在 Proxy 中精確控制儲存體 URL。 <br/><br/> VM 的單一網際網路存取點。 <br/><br/> 不會隨著 Azure IP 位址變更。 | 使用 Proxy 軟體執行 VM 時的額外成本。 

### <a name="set-vm-permissions"></a>設定 VM 權限

當您為 SQL Server 資料庫設定備份時，Azure 備份會執行若干作業：

- 新增 **AzureBackupWindowsWorkload** 擴充功能。
- 為了探索虛擬機器上的資料庫，Azure 備份會建立帳戶 **NT SERVICE\AzureWLBackupPluginSvc**。 此帳戶用於備份和還原，且必須具有 SQL 系統管理員權限。
- Azure 備份會利用 **NT AUTHORITY\SYSTEM** 帳戶進行資料庫探索/查詢，因此該帳戶必須在 SQL 上公開登入。

如果您尚未從 Azure Marketplace 建立 SQL Server VM，您可能會收到 **UserErrorSQLNoSysadminMembership** 錯誤。 若發生此狀況，請[依照下列指示操作](#fix-sql-sysadmin-permissions)。

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>確認 Azure 備份的資料庫命名方針

請避免使用下列資料庫名稱：

  * 後置/前置空格
  * 後置 ‘!’
  * 右方括號 ‘]’

對於 Azure 表格不支援的字元，可以使用別名，但建議避免採用此做法。 [深入了解](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN)。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>探索 SQL Server 資料庫

探索在 VM 上執行的資料庫。 
1. 在 [Azure 入口網站](https://portal.azure.com)中，開啟您用來備份資料庫的復原服務保存庫。

2. 在 [復原服務保存庫] 儀表板上，選取 [備份]。 

   ![選取 [備份] 以開啟 [備份目標] 功能表](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在 [備份目標] 中，將 [工作負載的執行位置] 設為 [Azure] (預設值)。

4. 在 [您要備份什麼?] 中，選取 [Azure VM 中 SQL Server]。

    ![選取 Azure VM 中的 SQL Server 來進行備份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)


5. 在 [備份目標] > [探索 VM 中的 DB] 中，選取 [開始探索] 以搜尋訂用帳戶中未受保護的 VM。 搜尋可能需要一些時間，視訂用帳戶中未受保護的虛擬機器數目而定。

    ![在搜尋 VM 中的 DB 期間會將備份擱置](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. 在 VM 清單中，選取執行 SQL Server 資料庫的 VM，然後選取 [探索資料庫]。

7. 在 [通知] 區域中追蹤資料庫探索進度。 此作業可能需要一些時間才能完成，視 VM 上的資料庫數目而定。 探索到選取的資料庫之後，即會出現成功訊息。

    ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

    - 在探索之後，未受保護的 VM 應該會出現在清單中，並依照名稱和資源群組列出。
    - 如果 VM 未如預期列出，請檢查該 VM 是否已備份在保存庫中。
    - 多個 VM 可以有相同名稱，但會屬於不同的資源群組。 

9. 選取執行 SQL Server 資料庫的 VM，然後選取 [探索資料庫]。 
10. Azure 備份會探索 VM 上的所有 SQL Server 資料庫。 在探索期間，會在背景中執行下列作業：

    - Azure 備份向用來備份工作負載的保存庫註冊 VM。 已註冊 VM 上的所有資料庫都只能備份至此保存庫。
    - Azure Backup 在 VM 上安裝 **AzureBackupWindowsWorkload** 擴充功能。 SQL 資料庫上不會安裝代理程式。
    - Azure Backup 在 VM 上建立服務帳戶 **NT Service\AzureWLBackupPluginSvc**。
      - 所有備份和還原作業都會使用此服務帳戶。
      - **NT Service\AzureWLBackupPluginSvc** 需要 SQL 系統管理員權限。 在 Azure Marketplace 中建立的所有 SQL Server VM 都會預先安裝 **SqlIaaSExtension**。 **AzureBackupWindowsWorkload** 延伸模組會使用 **SQLIaaSExtension** 來自動取得必要的權限。
    - 如果您尚未從 Marketplace 建立 VM，則 VM 不會安裝 **SqlIaaSExtension**，因此探索作業將會失敗，並顯示錯誤訊息 **UserErrorSQLNoSysAdminMembership**。 請依照 [#fix-sql-sysadmin-permissions] 中的指示修正此問題。

        ![選取 VM 和資料庫](./media/backup-azure-sql-database/registration-errors.png)



## <a name="configure-a-backup-policy"></a>設定備份原則 

為了將備份負載最佳化，Azure 備份會將每個備份作業的資料庫數目上限設為 50。

- 若要保護 50 個以上的資料庫，請設定多個備份。
- 或者，您可以啟用自動保護。 自動保護會同時保護所有現有的資料庫，並自動保護新增至可用性群組執行個體的新資料庫。


請依照下列方式設定備份：

1. 在保存庫儀表板上，選取 [備份]。 

   ![選取 [備份] 以開啟 [備份目標] 功能表](./media/backup-azure-sql-database/open-backup-menu.png)

3. 在 [備份目標] 功能表中，將 [工作負載的執行位置] 設為 [Azure]。

4. 在 [您要備份什麼?] 中，選取 [Azure VM 中 SQL Server]。

    ![選取 Azure VM 中的 SQL Server 來進行備份](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    
5. 在 [備份目標] 中，選取 [設定備份]。

    ![選取 [設定備份]](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    ![顯示所有具備獨立資料庫的 SQL Server 執行個體](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. 選取您要保護的所有資料庫，然後選取 [確定]。

    ![保護資料庫](./media/backup-azure-sql-database/select-database-to-protect.png)

    - 所有 SQL Server 執行個體都會顯示 (獨立執行個體和可用性群組)。
    - 選取執行個體名稱/可用性群組左側的向下箭號，以進行篩選。

7. 在 [備份] 功能表上，選取 [備份原則]。 

    ![選取備份原則](./media/backup-azure-sql-database/select-backup-policy.png)

8. 在 [選擇備份原則] 中選取原則，然後按一下 [確定]。

    - 選取預設原則：**HourlyLogBackup**。
    - 選擇之前為 SQL 建立的現有備份原則。
    - 根據 RPO 和保留範圍[定義新的原則](backup-azure-sql-database.md#configure-a-backup-policy)。
    - 在預覽期間，您無法編輯現有的備份原則。
    
9. 在 [備份] 功能表上，選取 [啟用備份]。

    ![啟用所選備份原則](./media/backup-azure-sql-database/enable-backup-button.png)

10. 在入口網站的 [通知] 區域中，追蹤設定進度。

    ![[通知] 區域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。 

- 原則會建立於保存庫層級上。
- 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。
- 建立備份原則時，預設值是每日完整備份。
- 您可以新增差異備份，但僅限於將完整備份設定為每週進行時。
- [了解](backup-architecture.md#sql-server-backup-types)不同類型的備份原則。


若要建立備份原則：

1. 在保存庫中，按一下 [備份原則] > [新增]。
2. 在 [新增] 功能表中，按一下 [Azure VM 中的 SQL Server]。 這會定義原則類型。

   ![針對新備份原則選擇原則類型](./media/backup-azure-sql-database/policy-type-details.png)

3. 在 [原則名稱] 中，為新原則輸入名稱。 
4. 在 [完整備份原則] 中選取 [備份頻率]，然後選擇 [每日] 或 [每週]。

    - 若選擇 [每日]，請選取備份作業的開始時間和時區。
    - 您必須執行完整備份，因此無法關閉 [完整備份] 選項。
    - 按一下 [完整備份] 以檢視原則。 
    - 您無法為每日完整備份建立差異備份。
    - 若選擇 [每週]，請選取備份作業的開始日期 (星期幾)、時間和時區。

    ![新的備份原則欄位](./media/backup-azure-sql-database/full-backup-policy.png)  

5. 針對 [保留範圍]，依預設會選取所有選項。 請清除任何您不要使用的保留範圍限制，並設定要使用的間隔。 

    - 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    - 系統會根據每週保留範圍和每週保留設定，標記和保留特定日期的備份。
    - 每月和每年保留範圍會以類似方式運作。

   ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)

    

6. 在 [完整備份原則] 功能表中，選取 [確定] 接受設定。
7. 若要新增差異備份原則，請選取 [差異備份]。 

   ![保留範圍間隔設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![開啟差異備份原則功能表](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。 

    - 您每天最多可以觸發一次差異備份。
    - 差異備份最多可以保留 180 天。 如果您需要保留更久，則必須使用完整備份。

9. 選取 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。

10. 若要新增交易記錄備份原則，請選取 [記錄備份]。
11. 在 [記錄備份] 中選取 [啟用]，然後設定頻率和保留控制項。 記錄備份可以每隔 15 分鐘頻繁地出現，而且最多可以保留 35 天。
12. 選取 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。

   ![編輯記錄備份原則](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. 在 [備份原則] 功能表上，選擇是否要啟用 [SQL 備份壓縮]。
    - 預設會停用壓縮。
    - Azure 備份在後端使用 SQL 原生備份壓縮。

14. 完成備份原則的編輯之後，請選取 [確定]。



## <a name="enable-auto-protection"></a>啟用自動保護  

啟用自動保護可自動備份所有現有的資料庫，以及未來新增至獨立 SQL Server 執行個體或 SQL Server Always On 可用性群組的資料庫。 

- 開啟自動保護並選取備份原則後，原本已受到保護的資料庫會繼續使用先前的原則。
- 您可以同時選取不限數量的資料庫，使其受到自動保護。

請依照下列方式啟用自動保護：

1. 在 [要備份的項目] 中，選取要啟用自動保護的執行個體。
2. 選取 [自動保護] 下方的下拉式清單，並設定為 [開啟]。 然後按一下 [確定] 。

    ![在 Always On 可用性群組上啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

3. 所有資料庫會一起設定備份，且您可在 [備份作業] 中加以追蹤。


如果您要停用自動保護，請按一下 [設定備份] 下方的執行個體名稱，然後針對該執行個體選取 [停用自動保護]。 所有資料庫會都繼續備份。 不過，未來的資料庫將不會自動受到保護。

![在該執行個體上停用自動保護](./media/backup-azure-sql-database/disable-auto-protection.png)


## <a name="fix-sql-sysadmin-permissions"></a>修正 SQL 系統管理員權限

若因 **UserErrorSQLNoSysadminMembership** 錯誤而需要修正權限，請執行下列動作：

1. 使用具有 SQL Server 系統管理員權限的帳戶登入 SQL Server Management Studio (SSMS)。 除非您需要特殊權限，否則 Windows 驗證應該能運作。
2. 在 SQL Server 上，開啟 [安全性]/[登入] 資料夾。

    ![開啟 [安全性]/[登入] 資料夾來查看帳戶](./media/backup-azure-sql-database/security-login-list.png)

3. 以滑鼠右鍵按一下 [登入] 資料夾，然後選取 [新增登入]。 在 [登入 - 新增] 中，選取 [搜尋]。

    ![在 [登入 - 新增] 對話方塊中，選取 [搜尋]](./media/backup-azure-sql-database/new-login-search.png)

3. Windows 虛擬服務帳戶 **NT SERVICE\AzureWLBackupPluginSvc** 已於虛擬機器註冊期間和 SQL 探索階段建立。 請輸入 [輸入要選取的物件名稱] 中顯示的帳戶名稱。 選取 [檢查名稱] 以解析名稱。 按一下 [確定]。

    ![選取 [檢查名稱] 以解析未知的服務名稱](./media/backup-azure-sql-database/check-name.png)


4. 在 [伺服器角色] 中，確定已選取**系統管理員**角色。 按一下 [確定]。 現在應該存在必要權限。

    ![確定已選取系統管理員伺服器角色](./media/backup-azure-sql-database/sysadmin-server-role.png)

5. 現在，請建立資料庫與復原服務保存庫的關聯。 在 Azure 入口網站的 [受保護的伺服器] 清單中，以滑鼠右鍵按一下處於錯誤狀態的伺服器，然後選取 [重新探索資料庫]。

    ![確認伺服器具有適當的權限](./media/backup-azure-sql-database/check-erroneous-server.png)

6. 在 [通知] 區域中查看進度。 找到選取的資料庫之後，即會出現成功訊息。

    ![部署成功訊息](./media/backup-azure-sql-database/notifications-db-discovered.png)

或者，您可以藉由在 [自動保護] 欄的對應下拉式清單中選取 [開啟] 選項，在整個執行個體或 Always On 可用性群組上啟用[自動保護](backup-azure-sql-database.md#enable-auto-protection)。 [自動保護](backup-azure-sql-database.md#enable-auto-protection)功能不僅可一次性地在所有現有的資料庫上啟用保護，也會自動保護未來將新增至該執行個體或可用性群組的任何新資料庫。  

   ![在 Always On 可用性群組上啟用自動保護](./media/backup-azure-sql-database/enable-auto-protection.png)

## <a name="next-steps"></a>後續步驟

- [了解](restore-sql-database-azure-vm.md)如何還原已備份的 SQL Server 資料庫。
- [了解](manage-monitor-sql-database-backup.md)如何管理已備份的 SQL Server 資料庫。

