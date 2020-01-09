---
title: 使用 Azure 備份將 SAP Hana 資料庫備份至 Azure
description: 在本文中，您將瞭解如何使用 Azure 備份服務，將 SAP Hana 資料庫備份至 Azure 虛擬機器。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 3246f6cf8046e0a0c5795059ad3448b70130e7e1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496966"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>將 SAP Hana 資料庫備份到 Azure VM

SAP Hana 資料庫是需要低復原點目標（RPO）和長期保留的重要工作負載。 您可以使用[Azure 備份](backup-overview.md)來備份在 Azure 虛擬機器（vm）上執行的 SAP Hana 資料庫。

本文說明如何將在 Azure Vm 上執行的 SAP Hana 資料庫備份至 Azure 備份復原服務保存庫。

在本文中，您將了解如何：
> [!div class="checklist"]
>
> * 建立和設定保存庫
> * 探索資料庫
> * 設定備份
> * 執行隨選備份作業

## <a name="prerequisites"></a>必要條件

請參閱[必要條件](tutorial-backup-sap-hana-db.md#prerequisites)和[設定許可權](tutorial-backup-sap-hana-db.md#setting-up-permissions)區段，以設定備份的資料庫。

### <a name="set-up-network-connectivity"></a>設定網路連線

執行任何作業時，SAP HANA VM 都需要連線至 Azure 公用 IP 位址。 若未連線，VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 將無法運作。 請允許存取 Azure 資料中心的 IP 範圍，以建立連線：

* 您可以下載 Azure 資料中心的 [IP 位址範圍](https://www.microsoft.com/download/details.aspx?id=41653)，然後允許存取這些 IP 位址。
* 如果您使用網路安全性群組 (NSG)，您可以使用 AzureCloud [服務標籤](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)來允許所有的 Azure 公用 IP 位址。 您可以使用 [Set-AzureNetworkSecurityRule Cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) 來修改 NSG 規則。
* 由於傳輸是經由 HTTPS 進行的，連接埠 443 應新增至允許清單。

## <a name="onboard-to-the-public-preview"></a>上線至公開預覽

上線至公開預覽，如下所示：

* 在入口網站中，[依照本文的指示](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)將您的訂用帳戶識別碼註冊至「復原服務」服務提供者。
* 針對 PowerShell 中的 ' Az ' 模組，執行此 Cmdlet。 其結果應為「已註冊」。

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* 如果您在 PowerShell 中使用 ' AzureRM ' 模組，請執行此 Cmdlet。 其結果應為「已註冊」。

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>探索資料庫

1. 在保存庫的 [使用者入門] 中，按一下 [備份]。 在 [工作負載的執行位置？] 中，選取 [Azure VM 中的 SAP HANA]。
2. 按一下 [開始探索]。 這會對保存庫區域中未受保護的 Linux VM 起始探索。

   * 探索之後，未受保護的 Vm 會出現在入口網站中，並依名稱和資源群組列出。
   * 如果 VM 未如預期般列出，請檢查它是否已在保存庫中備份。
   * 多個 Vm 可以有相同的名稱，但它們屬於不同的資源群組。

3. 在 [選取虛擬機器] 中按一下連結，以下載提供權限讓 Azure 備份服務存取 SAP Hana VM 以進行資料庫探索的指令碼。
4. 在您要備份的每個 SAP Hana 資料庫的 VM 上執行腳本。
5. 在 Vm 上執行腳本之後，請在 [**選取虛擬機器**] 中選取 vm。 然後，按一下 [探索資料庫]。
6. Azure 備份會探索 VM 上的所有 SAP HANA 資料庫。 在探索期間，Azure 備份會向保存庫註冊 VM，並在 VM 上安裝延伸模組。 資料庫上不會安裝代理程式。

    ![探索 SAP Hana 資料庫](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>設定備份  

現在啟用備份。

1. 在步驟2中，按一下 [**設定備份**]。

    ![設定備份](./media/backup-azure-sap-hana-database/configure-backup.png)
2. 在 [**選取要備份的專案**] 中，選取您想要保護的所有資料庫，> **[確定]** 。

    ![選取要備份的項目](./media/backup-azure-sap-hana-database/select-items.png)
3. 在 [**備份原則**] 中 > **選擇 [備份原則**]，並根據下列指示建立資料庫的新備份原則。

    ![選擇備份原則](./media/backup-azure-sap-hana-database/backup-policy.png)
4. 建立原則之後，請在 [**備份**] 功能表上，按一下 [**啟用備份**]。

    ![啟用備份](./media/backup-azure-sap-hana-database/enable-backup.png)
5. 在入口網站的 [通知] 區域中，追蹤備份設定進度。

### <a name="create-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

指定原則設定，如下所示：

1. 在 [原則名稱] 中，為新原則輸入名稱。

   ![輸入原則名稱](./media/backup-azure-sap-hana-database/policy-name.png)
2. 在 [完整備份原則] 中選取 [備份頻率]，然後選擇 [每日] 或 [每週]。
   * **每日**：選取備份作業開始的小時和時區。
       * 您必須執行完整備份。 您無法關閉此選項。
       * 按一下 [完整備份] 以檢視原則。
       * 您無法為每日完整備份建立差異備份。
   * **每週**：選取備份作業執行所在的星期幾、小時和時區。

   ![選取備份頻率](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. 在 [保留範圍] 中，設定完整備份的保留期設定。
    * 預設會選取所有選項。 清除您不想要使用的任何保留範圍限制，並設定您要執行的任何限制。
    * 所有備份類型 (完整/差異/記錄) 的最小保留期間皆為七天。
    * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    * 系統會根據每週保留範圍和設定，標記和保留特定日期的備份。
    * 每月和每年保留範圍會以類似方式運作。

4. 在 [完整備份原則] 功能表中，按一下 [確定] 以接受設定。
5. 選取 [**差異備份**] 以新增差異原則。
6. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。
    * 您每天最多可以觸發一次差異備份。
    * 差異備份最多可以保留 180 天。 如果您需要保留更久，則必須使用完整備份。

    ![差異備份原則](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 目前不支援增量備份。

7. 按一下 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。
8. 選取 [記錄備份]，以新增交易記錄備份原則。
    * 在 [**記錄備份**] 中，選取 [**啟用**]。  此選項無法停用，因為 SAP HANA 管理所有的記錄備份。
    * 設定頻率和保留控制項。

    > [!NOTE]
    > 只有在成功完成完整備份之後，記錄備份才會開始流動。

9. 按一下 [確定] 以儲存原則，然後返回主要 [備份原則] 功能表。
10. 在完成備份原則的定義後，按一下 [確定]。

> [!NOTE]
> 每個記錄備份都會連結到先前的完整備份，以形成復原鏈。 系統會保留此完整備份，直到最後一個記錄備份的保留期過期為止。 這可能表示完整備份會保留一段額外的時間，以確保可以復原所有記錄。 假設使用者有每週的完整備份、每日差異和2小時記錄。 所有這些專案都會保留30天。 但是，只有在下一次完整備份（也就是 30 + 7 天之後）可以使用之後，才能真正清除/刪除每週的完整功能。 比方說，每週的完整備份會在11月16日執行。 根據保留原則，它應該保留到12月16日為止。 此完整的最後一個記錄備份會在11月22日的下一次排定的完整時間之前進行。 直到 Dec 22 日為止，才可使用此記錄檔，但無法刪除11月16日完整版。 因此，11月16日的完整版會保留到 Dec 22 日。

## <a name="run-an-on-demand-backup"></a>執行隨選備份

備份會根據原則排程執行。 您可以視需要執行備份，如下所示：

1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [**備份專案**] 中，選取執行 SAP Hana 資料庫的 VM，然後按一下 [**立即備份**]。
3. 在 [**立即備份**] 中，使用行事曆控制項選取復原點應保留的最後一天。 然後按一下 [確定]。
4. 監視入口網站通知。 您可以在保存庫儀表板中監視作業進度 > [備份作業] > [進行中]。 視資料庫的大小而定，建立初始備份可能需要一段時間。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在已啟用 Azure 備份的資料庫上執行 SAP Hana Studio 備份

如果您想要使用 Azure 備份進行備份之資料庫的本機備份（使用 HANA Studio），請執行下列動作：

1. 等待資料庫的完整或記錄備份完成。 檢查 SAP Hana Studio/駕駛環境中的狀態。
2. 停用記錄備份，並將備份類別目錄設定為相關資料庫的檔案系統。
3. 若要這麼做，請按兩下 [systemdb ** > 設定**] > **選取 [資料庫** > **篩選（記錄）** ]。
4. 將**enable_auto_log_backup**設定為 [**否**]。
5. 將**log_backup_using_backint**設定為**False**。
6. 依照需求進行資料庫的完整備份。
7. 等待完整備份和目錄備份完成。
8. 將先前的設定還原回 Azure：
    * 將**enable_auto_log_backup**設定為 **[是]** 。
    * 將**log_backup_using_backint**設定為**True**。

## <a name="next-steps"></a>後續步驟

* 了解如何[還原執行於 Azure VM 上的 SAP HANA 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 瞭解如何[管理使用 Azure 備份備份的 SAP Hana 資料庫](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
