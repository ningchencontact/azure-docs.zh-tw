---
title: 備份至 Azure 備份使用 Azure 的 SAP HANA 資料庫 |Microsoft Docs
description: 本教學課程說明如何使用 Azure 備份服務將 SAP HANA 資料庫備份至 Azure。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: raynew
ms.openlocfilehash: 5ed41013535e4591d88bff5c017c1fcf4c4053cc
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237803"
---
# <a name="back-up-an-sap-hana-database"></a>備份 SAP HANA 資料庫

[Azure 備份](backup-overview.md)支援備份至 Azure 的 SAP HANA 資料庫。

> [!NOTE]
> 此功能目前為公開預覽狀態。 它目前不適合在生產環境，並沒有保證的 SLA。 


## <a name="scenario-support"></a>案例支援

**支援** | **詳細資料**
--- | ---
**支援的地區** | 澳大利亞東南部、 澳大利亞東部 <br> 巴西南部 <br> 加拿大中部、 加拿大東部 <br> 東南亞、 東亞 <br> 美國東部、 美國東部 2、 美國中西部、 美國西部、 美國西部 2、 North Central US、 美國中部、 美國中南部<br> 印度中部、 印度南部 <br> 日本東部、日本西部<br> 南韓中部、南韓南部 <br> 北歐、西歐 <br> 英國南部、 英國西部
**支援的 VM 作業系統** | SLES 12 SP2 或 SP3。
**支援的 HANA 版本** | 在 HANA 上的 SSDC 1.x，MDC HANA 上 2.x < = SPS03


### <a name="current-limitations"></a>目前的限制

- 您可以只備份 Azure Vm 上執行的 SAP HANA 資料庫。
- 您只可以在 Azure 入口網站中設定 SAP HANA 備份。 使用 PowerShell、 CLI 或 REST API，就無法設定此功能。
- 您可以只備份資料庫以相應增加模式。
- 每隔 15 分鐘，您可以將資料庫記錄備份中。 記錄備份只會開始流向成功的完整資料庫備份完成後。
- 您可以建立完整和差異備份。 目前不支援增量備份。
- 適用於 SAP HANA 備份之後，您無法修改備份原則。 如果您想要使用不同的設定備份，建立新的原則，或將不同原則指派。 
    - 若要建立新的原則，請在保存庫中按一下**原則** > **備份原則** > **+ 新增** > **中的 SAP HANAAzure VM**，並指定原則設定。
    - 若要指派不同的原則，在執行資料庫的 vm 屬性中按一下目前的原則名稱。 接著，在**備份原則**頁面，您可以選取要用於備份不同的原則。




## <a name="prerequisites"></a>必要條件

請確定您在設定備份之前，執行下列動作：

1. 在 VM 上執行 SAP HANA 資料庫，安裝 Microsoft 的官方[.NET Core 執行階段 2.1](https://dotnet.microsoft.com/download/linux-package-manager/sles/runtime-current)封裝。 請注意：
    - 您只需要**dotnet 執行階段 2.1**封裝。 您不需要**aspnetcore 執行階段 2.1**。
    - 如果 VM 沒有網際網路存取、 鏡像或 dotnet 執行階段 2.1 （以及所有相依的 Rpm） 從 Microsoft 套件摘要指定提供離線快取中的頁面。
    - 在套件安裝期間可能會要求您指定要使用的選項。 如果是的話，指定**解決方案 2**。

        ![封裝的安裝選項](./media/backup-azure-sap-hana-database/hana-package.png)

2.  在 VM 上安裝並啟用從官方 SLES 套件/媒體，如下所示使用 zypper，ODBC 驅動程式套件：

    ``` 
    sudo zypper update
    sudo zypper install unixODBC
    ```
4.  允許連線從 VM 到網際網路，以便它可以連線到 Azure，如下列程序中所述。


### <a name="set-up-network-connectivity"></a>設定網路連線

SAP HANA VM 的所有作業，都必須連線到 Azure 的公用 IP 位址。 連線無法運作 （資料庫探索、 設定備份、 排程備份、 還原復原點等等） 的 VM 作業。 建立連線，藉由允許存取 Azure 資料中心 IP 範圍： 

- 您可以下載[IP 位址範圍](https://www.microsoft.com/download/details.aspx?id=41653)Azure 資料中心，然後讓這些 IP 位址的存取權。
- 如果您使用網路安全性群組 (Nsg)，您可以使用 AzureCloud[服務標籤](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)若要允許所有 Azure 公用 IP 位址。 您可以使用[Set-azurenetworksecurityrule cmdlet](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0)修改 NSG 規則。

## <a name="onboard-to-the-public-preview"></a>上架到公開預覽

上架到公開預覽版本中，如下所示：

- 在入口網站，註冊復原服務的服務提供者，藉由您訂用帳戶 ID[遵循這篇文章](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)。 
- 針對 PowerShell，請執行這個指令程式。 它應該為 「 已註冊 」 完成。

    ```
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```



[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>探索資料庫


1. 在保存庫中，在**快速入門**，按一下**備份**。 在 **地方執行您的工作負載？**，選取**Azure VM 中的 SAP HANA**。
2. 按一下 **開始探索**。 這會起始的未受保護的 Linux Vm，在保存庫區域中的探索。

   - 在探索之後，未受保護的 Vm 會出現在入口網站中，依照名稱和資源群組。
   - 如果沒有列出 VM，如預期般運作，請檢查是否已經備份保存庫中。
   - 多個 Vm 可以有相同的名稱，但它們屬於不同的資源群組。

3. 在 **選取虛擬機器**，按一下連結即可下載提供 Azure 備份服務來存取資料庫探索 SAP HANA Vm 的權限的指令碼
4. 在主控您想要備份的 SAP HANA 資料庫的每個 VM 上執行指令碼。
5. 在 Vm 上，執行指令碼時，也將在之後**選取虛擬機器**，選取的 Vm。 然後按一下**探索 Db**。
6. Azure 備份會探索所有在 VM 上的 SAP HANA 資料庫。 在探索期間 Azure 備份保存庫註冊 VM，並在 VM 上安裝擴充功能。 在資料庫上不安裝任何代理程式。

    ![探索 SAP HANA 資料庫](./media/backup-azure-sap-hana-database/hana-discover.png)
    
## <a name="configure-backup"></a>設定備份  

立即啟用備份。

1. 在步驟 2 中，按一下**設定備份**。
2. 在 **選取要備份項目**，選取您想要保護的所有資料庫 >**確定**。
3. 在  **備份原則** > **選擇備份原則**，建立新的備份原則，對於資料庫，請依照下列指示。
4. 在 建立原則之後,**備份**功能表上，按一下**啟用備份**。
5. 備份組態中追蹤進度 **通知** 入口網站的區域。

### <a name="create-a-backup-policy"></a>建立備份原則
備份原則會定義包含執行備份，以及要保留的時間長度。

- 原則會建立於保存庫層級上。
- 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

指定的原則設定，如下所示：

1. 在 [原則名稱] 中，為新原則輸入名稱。
2. 在 [完整備份原則] 中選取 [備份頻率]，然後選擇 [每日] 或 [每週]。
   - **每日**：選取小時和備份作業會開始的時區。
   
       - 您必須執行完整備份。 您無法關閉此選項。
       - 按一下 [完整備份] 以檢視原則。
       - 您無法為每日完整備份建立差異備份。
       
   - **每週**:選取星期幾、 小時與備份工作執行所在的時區的日期。
3. 在 **保留範圍**，進行完整備份的保留設定。
    - 依預設會選取所有選項。 清除您不打算使用，並設定您所執行的任何保留期範圍限制。
    - 任何類型 （完整/差異/記錄檔） 的最小保留期限是備份的七天。
    - 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
    - 備份會標記特定一天，並將其保留為基礎的每週的保留範圍和設定。
    - 每月和每年保留範圍會以類似方式運作。

4. 在 **完整的備份原則**功能表上，按一下**確定**接受設定。
5. 選取 **差異備份**新增差異原則。
6. 在 [差異備份原則] 中，選取 [啟用] 以開啟頻率和保留控制項。
    - 您每天最多可以觸發一次差異備份。
    - 差異備份最多可以保留 180 天。 如果您需要保留更久，則必須使用完整備份。

    > [!NOTE]
    > 目前不支援增量備份。 

7. 按一下  **確定**以儲存原則，並返回 主要**備份原則**功能表。
8. 選取 **記錄檔備份**將交易記錄備份原則
    - 在 **記錄檔備份**，選取**啟用**。
    - 設定的頻率和保留的控制項。

    > [!NOTE]
    > 只會記錄備份開始流程成功的完整備份完成之後。

9. 按一下  **確定**以儲存原則，並返回 主要**備份原則**功能表。
10. 在您完成定義備份原則之後，請按一下**確定**。


## <a name="run-an-on-demand-backup"></a>執行隨選備份

根據原則的排程執行備份。 您可以視需要執行備份，如下所示：


1. 在保存庫功能表中，按一下 [備份項目]。
2. 在 **備份項目**，選取 執行 SAP HANA 資料庫的 VM，然後按一下**立即備份**。
3. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然後按一下 [確定] 。
4. 監視入口網站通知。 您可以監視作業進度，在保存庫儀表板 >**備份作業**> 進行中。 根據您的資料庫大小，建立初始備份可能需要一段時間。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>使用 Azure 備份已啟用的資料庫上執行 SAP HANA Studio 備份

如果您想要進行本機備份 （使用 HANA Studio） 之資料庫的備份使用 Azure 備份，執行下列作業：

1. 等候任何完整或記錄備份的資料庫能夠完成。 檢查 SAP HANA Studio 中的狀態。
2. 停用記錄檔備份，並設定為相關資料庫的檔案系統的 備份類別目錄。
3. 若要這樣做，請按兩下**systemdb** > **組態** > **選取資料庫** > **篩選器 （記錄）**.
4. 設定**enable_auto_log_backup**要**No**。
5. 設定**log_backup_using_backint**要**False**。
6. 進行臨機操作的完整資料庫備份。
7. 等候類別目錄來完成的備份與完整備份。
8. 還原先前的設定，來與 Azure:
    - 設定**enable_auto_log_backup**要**是**。
    - 設定**log_backup_using_backint**要 **，則為 True**。



## <a name="next-steps"></a>後續步驟

[深入了解](backup-azure-arm-vms-prepare.md)備份 Azure Vm。


