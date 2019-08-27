---
title: 使用 Azure 備份將 SAP Hana 資料庫備份至 Azure |Microsoft Docs
description: 本教學課程說明如何使用 Azure 備份服務將 SAP Hana 資料庫備份至 Azure。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dacurwin
ms.openlocfilehash: a11d454feb965907f3bd4e994c0916eeb7236fa7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034558"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>將 SAP Hana 資料庫備份至 Azure

[Azure 備份](backup-overview.md)支援將 SAP Hana 資料庫備份至 Azure。

> [!NOTE]
> 此功能目前為公開預覽狀態。 目前尚未備妥生產環境, 而且不保證 SLA。 

## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的地區** | 澳大利亞東部、澳大利亞東部 <br> 巴西南部 <br> 加拿大中部, 加拿大東部 <br> 南部東亞, 東亞 <br> 美國東部、美國東部2、美國中西部、美國西部、美國西部2、美國中北部、美國中部、美國中南部<br> 印度中部、印度南部 <br> 日本東部、日本西部<br> 南韓中部、南韓南部 <br> 北歐、西歐 <br> 英國南部, 英國西部
**支援的 VM 作業系統** | SLES 12 SP2 或 SP3。
**支援的 HANA 版本** | Hana 1.x 上的 SDC, HANA 2.x 上的 MDC < = SPS03

### <a name="current-limitations"></a>目前的限制

- 您只能備份在 Azure Vm 上執行的 SAP Hana 資料庫。
- 您只能在 Azure 入口網站中設定 SAP Hana 備份。 無法使用 PowerShell、CLI 或 REST API 來設定此功能。
- 您只能以相應增加模式來備份資料庫。
- 您可以每隔15分鐘備份一次資料庫記錄。 記錄備份只會在資料庫的成功完整備份完成後開始流動。
- 您可以進行完整和差異備份。 目前不支援增量備份。
- 將備份原則套用至 SAP Hana 備份之後, 即無法修改。 如果您想要使用不同的設定進行備份, 請建立新的原則, 或指派不同的原則。
  - 若要建立新原則, 請在保存庫中按一下 [**原則** > ] [**備份原則** > ] **+**  > [**在 Azure VM 中新增 SAP Hana**], 然後指定原則設定。
  - 若要指派不同的原則, 請在執行資料庫之 VM 的屬性中, 按一下目前的原則名稱。 然後在 [**備份原則**] 頁面上, 您可以選取要用於備份的不同原則。

## <a name="prerequisites"></a>必要條件

設定備份之前, 請務必執行下列動作:

1. 在執行 SAP Hana 資料庫的 VM 上, 安裝官方的 Microsoft [.Net Core Runtime 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current)套件。 請注意：
    - 您只需要**dotnet-runtime-2.1**套件。 您不需要**aspnetcore-runtime-2.1**。
    - 如果 VM 沒有網際網路存取, 請從頁面中指定的 Microsoft 封裝摘要, 鏡像或提供 dotnet-runtime-2.1 (以及所有相依的 Rpm) 的離線快取。
    - 在安裝套件期間, 系統可能會要求您指定一個選項。 若是如此, 請指定 [**方案 2**]。

        ![封裝安裝選項](./media/backup-azure-sap-hana-database/hana-package.png)

2. 在 VM 上, 使用 zypper 從官方 SLES 封裝/媒體安裝並啟用 ODBC 驅動程式套件, 如下所示:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

3. 允許從 VM 連線到網際網路, 使其可以觸達 Azure, 如[以下](#set-up-network-connectivity)程式所述。

4. 在以 root 使用者身分安裝 HANA 的虛擬機器中, 執行預先註冊腳本。 腳本會在流程的[入口網站](#discover-the-databases)中提供, 而且必須設定[正確的許可權](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions)。

### <a name="set-up-network-connectivity"></a>設定網路連線能力

針對所有作業, SAP Hana VM 需要連線到 Azure 公用 IP 位址。 VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 在沒有連線能力的情況下無法運作。 藉由允許存取 Azure 資料中心 IP 範圍來建立連線能力: 

- 您可以下載 Azure 資料中心的[ip 位址範圍](https://www.microsoft.com/download/details.aspx?id=41653), 然後允許存取這些 ip 位址。
- 如果您使用網路安全性群組 (Nsg), 您可以使用 AzureCloud[服務](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)標籤來允許所有的 AZURE 公用 IP 位址。 您可以使用[set-azurenetworksecurityrule Cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0)來修改 NSG 規則。

## <a name="onboard-to-the-public-preview"></a>上架至公開預覽

上線至公開預覽, 如下所示:

- 在入口網站中，[依照本文所述](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)，向復原服務服務提供商註冊您的訂用帳戶識別碼。 
- 針對 PowerShell, 請執行此 Cmdlet。 它應該會完成為「已註冊」。

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>探索資料庫

1. 在保存庫的**消費者入門**中, 按一下 [**備份**]。 在 [**您的工作負載在何處執行？** ] 中, 選取 [**在 Azure VM 中 SAP Hana**]。
2. 按一下 [**開始探索**]。 這會起始探索保存庫區域中未受保護的 Linux Vm。

   - 探索之後, 未受保護的 Vm 會出現在入口網站中, 並依名稱和資源群組列出。
   - 如果 VM 未如預期般列出, 請檢查它是否已在保存庫中備份。
   - 多個 Vm 可以有相同的名稱, 但它們屬於不同的資源群組。

3. 在 [**選取虛擬機器**] 中, 按一下連結以下載提供 Azure 備份服務許可權的腳本, 以存取適用于資料庫探索的 SAP Hana vm
4. 在您要備份的每個 SAP Hana 資料庫的 VM 上執行腳本。
5. 在 Vm 上執行腳本之後, 請在 [**選取虛擬機器**] 中選取 vm。 然後按一下 [**探索 db**]。
6. Azure 備份會探索 VM 上的所有 SAP Hana 資料庫。 在探索期間, Azure 備份會向保存庫註冊 VM, 並在 VM 上安裝擴充功能。 資料庫上未安裝任何代理程式。

    ![探索 SAP Hana 資料庫](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>設定備份  

現在啟用備份。

1. 在步驟2中, 按一下 [**設定備份**]。
2. 在 [**選取要備份的專案**] 中, 選取您想要保護的所有資料庫, > **[確定]** 。
3. 在 [**備份原則** > ] 中,**選擇 [備份原則**], 並根據下列指示建立資料庫的新備份原則。
4. 建立原則之後, 請在 [**備份**] 功能表上, 按一下 [**啟用備份**]。
5. 在入口網站的 [**通知**] 區域中, 追蹤備份設定的進度。

### <a name="create-a-backup-policy"></a>建立備份原則

備份原則會定義何時執行備份, 以及保留的時間長度。

- 原則會建立於保存庫層級上。
- 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

指定原則設定, 如下所示:

1. 在 [原則名稱] 中，為新原則輸入名稱。
2. 在 [完整備份原則] 中選取 [備份頻率]，然後選擇 [每日] 或 [每週]。
   - **每日**：選取備份作業開始的小時和時區。
   
       - 您必須執行完整備份。 您無法關閉此選項。
       - 按一下 [完整備份] 以檢視原則。
       - 您無法為每日完整備份建立差異備份。
       
   - **每週**:選取備份作業執行所在的星期幾、小時和時區。
3. 在 [**保留範圍**] 中, 設定完整備份的保留設定。
    - 預設會選取所有選項。 清除您不想要使用的任何保留範圍限制, 並設定您要執行的任何限制。
    - 任何備份類型的最小保留期限 (完整/差異/記錄) 為7天。
    - 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    - 會根據每週的保留範圍和設定來標記和保留特定日期的備份。
    - 每月和每年保留範圍會以類似方式運作。

4. 在 [**完整備份原則**] 功能表中, 按一下 **[確定]** 以接受設定。
5. 選取 [**差異備份**] 以新增差異原則。
6. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。
    - 您每天最多可以觸發一次差異備份。
    - 差異備份最多可以保留 180 天。 如果您需要保留更久，則必須使用完整備份。

    > [!NOTE]
    > 目前不支援增量備份。 

7. 按一下 **[確定]** 以儲存原則, 然後返回主要 [**備份原則**] 功能表。
8. 選取 [**記錄備份**] 以新增異動複寫記錄備份原則,
    - 在 [**記錄備份**] 中, 選取 [**啟用**]。
    - 設定頻率和保留控制項。

    > [!NOTE]
    > 只有在成功完成完整備份之後, 記錄備份才會開始流動。

9. 按一下 **[確定]** 以儲存原則, 然後返回主要 [**備份原則**] 功能表。
10. 完成定義備份原則之後, 請按一下 **[確定]** 。


## <a name="run-an-on-demand-backup"></a>執行隨選備份

備份會根據原則排程執行。 您可以視需要執行備份, 如下所示:


1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 [**備份專案**] 中, 選取執行 SAP Hana 資料庫的 VM, 然後按一下 [**立即備份**]。
3. 在 [**立即備份**] 中, 使用行事曆控制項選取復原點應保留的最後一天。 然後按一下 [確定]。
4. 監視入口網站通知。 您可以在 保存庫 儀表板中監視作業進度, >**備份作業**> 進行中。 視資料庫的大小而定, 建立初始備份可能需要一段時間。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>在已啟用 Azure 備份的資料庫上執行 SAP Hana Studio 備份

如果您想要使用 Azure 備份進行備份之資料庫的本機備份 (使用 HANA Studio), 請執行下列動作:

1. 等待資料庫的完整或記錄備份完成。 檢查 SAP Hana Studio 中的狀態。
2. 停用記錄備份, 並將備份類別目錄設定為相關資料庫的檔案系統。
3. 若要這麼做, 請  > 按兩下 [ **systemdb**  > 設定] [**選取資料庫** > **篩選 (記錄)** ]。
4. 將 [ **enable_auto_log_backup** ] 設定為 [**否**]。
5. 將**log_backup_using_backint**設定為**False**。
6. 進行資料庫的臨機操作完整備份。
7. 等待完整備份和目錄備份完成。
8. 將先前的設定還原回 Azure:
    - 將 [ **Enable_auto_log_backup** ] 設定為 **[是]** 。
    - 將**log_backup_using_backint**設定為**True**。



## <a name="next-steps"></a>後續步驟

[瞭解如何針對](backup-azure-sap-hana-database-troubleshoot.md)在 Azure vm 中使用 SAP Hana 備份時的常見錯誤進行疑難排解。
[瞭解如何](backup-azure-arm-vms-prepare.md)備份 Azure vm。
