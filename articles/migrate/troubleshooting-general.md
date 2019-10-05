---
title: 為 Azure Migrate 問題進行疑難排解 | Microsoft Docs
description: 提供 Azure Migrate 服務中已知問題的總覽，以及常見錯誤的疑難排解秘訣。
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: hamusa
ms.openlocfilehash: 468c87e176cc61c48ba4caabd1c5a26f94d5fb5b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970643"
---
# <a name="troubleshoot-azure-migrate"></a>為 Azure Migrate 疑難排解

[Azure Migrate](migrate-services-overview.md)提供評估和遷移工具的中樞，以及協力廠商獨立軟體廠商（ISV）產品。 本文可協助您針對 Azure Migrate、Azure Migrate 伺服器評估和 Azure Migrate 伺服器遷移的問題進行疑難排解。


## <a name="find-a-project"></a>尋找專案

Azure Migrate 有[兩個版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)。


如果您已在目前的 Azure Migrate 版本中建立 Azure Migrate 專案，請執行下列動作：

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋**Azure Migrate**。
2. 在 [Azure Migrate 儀表板 >**伺服器**] 中，選取右上角的 [**變更**]。

    ![切換至現有的 Azure Migrate 專案](./media/troubleshooting-general/switch-project.png)

3. 選取適當的訂用帳戶，並 Azure Migrate 專案。


如果您已在舊版 Azure Migrate 中建立專案，請執行下列動作：

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋**Azure Migrate**。
2. 在 [Azure Migrate] 儀表板中，如果您已在舊版中建立專案，則會顯示參考較舊專案的橫幅。 選取橫幅。

    ![存取現有的專案](./media/troubleshooting-general/access-existing-projects.png)

3. 檢查舊專案的清單。


## <a name="create-additional-projects"></a>建立其他專案

建立新的 Azure Migrate 專案，如下所示：

1. 在  [Azure 入口網站](https://portal.azure.com)中，搜尋**Azure Migrate**。
2. 在 Azure Migrate 儀表板 >**伺服器**上，選取右上角的 **變更**。

   ![變更 Azure Migrate 專案](./media/troubleshooting-general/switch-project.png)

3. 若要建立新的專案，請選取 [**按一下這裡**]。

   ![建立第二個 Azure Migrate 專案](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>審查支援的地理位置

審查[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)的支援地理位置。

## <a name="delete-projectsworkspaces"></a>刪除專案/工作區

刪除 Azure Migrate 專案和 Log Analytics 工作區時，請注意：

- 當您刪除 Azure Migrate 專案時，會刪除專案*和*有關探索到之電腦的中繼資料。
- 如果您已將 Log Analytics 工作區附加至伺服器評估工具，則不會自動刪除工作區。
- 相同的 Log Analytics 工作區可以用於多個案例。
- 如果您想要刪除 Log Analytics 工作區，您必須手動執行此動作。


### <a name="delete-a-project"></a>刪除專案

若要刪除目前版本 Azure Migrate 中的專案：

1. 開啟專案建立所在的 Azure 資源群組。
2. 在 [資源群組] 頁面中，選取 [**顯示隱藏的類型**]。
3. 選取您要刪除的 [遷移] 專案。 資源類型為 [Microsoft]、[遷移/migrateprojects]，並將其刪除。

若要刪除舊版 Azure Migrate 中的專案：

1. 開啟專案建立所在的 Azure 資源群組。
2. 選取您要刪除的 [遷移] 專案。 資源類型為 [遷移專案]，並將其刪除。


### <a name="delete-a-workspace"></a>刪除工作區

瀏覽到附加到專案的 Log Analytics 工作區。
* 如果您尚未刪除 Azure Migrate 專案，您可以在**Essentials** > **伺服器評估**中找到工作區的連結。
       @no__t 0LA 工作區 @ no__t-1

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>錯誤「要求必須包含使用者識別標頭」

建立專案時，此錯誤可能表示您無法存取組織的 Azure Active Directory （Azure AD）租使用者。

- 當您第一次新增至 Azure AD 租使用者時，您會收到加入租使用者的電子郵件邀請。
- 接受邀請以成功新增至租使用者。
    - 如果您看不到電子郵件，請洽詢具有租使用者存取權的使用者，並要求他們[重新傳送邀請](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)給您。
    - 收到邀請電子郵件之後，請開啟它，然後選取要接受邀請的連結。 然後，登出 Azure 入口網站並重新登入。 （重新整理瀏覽器將無法正常執行）。然後，您就可以開始建立遷移專案。


## <a name="error-invalid-ovf-manifest-entry"></a>錯誤「OVF 資訊清單專案無效」

如果您收到錯誤「提供的資訊清單檔案無效：不正確 OVF 資訊清單專案」，請執行下列動作：

1. 藉由檢查其雜湊值，確認已正確下載 Azure Migrate 設備 OVA 檔案。 [深入了解](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果雜湊值不相符，請再次下載 OVA 檔案，然後重試部署。
2. 如果部署仍然失敗，而且您使用 VMware vSphere 用戶端來部署 OVF 檔案，請嘗試透過 vSphere web 用戶端進行部署。 如果部署仍然失敗，請嘗試使用不同的網頁瀏覽器。
3. 如果您使用 vSphere web 用戶端，並嘗試在 vCenter Server 6.5 或6.7 上部署它，請嘗試直接在 ESXi 主機上部署 OVA：
   - 使用 web 用戶端（HTTPs：//<*主機 IP 位址*>/ui）直接連接到 ESXi 主機（而不是 vCenter Server）。
   - 在**Home** > **清查**中，選取 [ **FILE** > **部署 OVF 範本**]。 流覽至 OVA 並完成部署。
4. 如果部署仍然失敗，請連絡 Azure Migrate 的支援。

## <a name="appliance-cant-connect-to-the-internet"></a>設備無法連線到網際網路

如果設備機器位於 proxy 後方，就會發生這種情況。

- 如果 proxy 需要授權認證，請務必提供這些認證。
- 如果您使用以 URL 為基礎的防火牆 proxy 來控制輸出連線能力，請將下列 Url 新增至允許清單：

    - [適用于 VMware 評估的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Hyper-v 評估的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [VMware 無代理程式遷移的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [以 VMware 代理程式為基礎之遷移的 URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Hyper-v 遷移的 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- 如果您使用攔截 proxy 來連線到網際網路，請使用[這些步驟](https://docs.microsoft.com/azure/migrate/concepts-collector)將 proxy 憑證匯入至應用裝置 VM。

## <a name="errordatetime-synchronization"></a>錯誤：日期/時間同步處理

有關日期和時間同步處理（802）的錯誤指出伺服器時鐘可能未與目前時間同步超過五分鐘。 變更收集器 VM 上的時鐘時間以符合目前的時間：

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 若要檢查時區，請執行**w32tm/tz**。
3. 若要同步處理時間，請執行**w32tm/resync**。


## <a name="error-unabletoconnecttoserver"></a>Error:UnableToConnectToServer

如果您收到此連接錯誤，您可能無法連接到 vCenter Server *Servername*.com：9443。 錯誤詳細資料指出沒有在 HTTPs://*servername*.com： 9443/sdk 接聽可接受訊息的端點。

- 檢查您是否正在執行最新版本的設備。 如果不是，請將設備升級至[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。
- 如果問題仍然發生在最新版本中，設備可能無法解析指定的 vCenter Server 名稱，或指定的埠可能錯誤。 根據預設，如果未指定埠，收集器會嘗試連接到埠號碼443。

    1. 從設備 Ping *Servername*.com。
    2. 如果步驟1失敗，請嘗試使用 IP 位址連接到 vCenter server。
    3. 識別要連接到 vCenter Server 的正確埠號碼。
    4. 確認 vCenter Server 已啟動且正在執行。


## <a name="error-appliance-might-not-be-registered"></a>Error:設備可能未註冊

- 如果用來註冊應用裝置的 Azure 帳戶許可權不足，就會發生錯誤60052：「設備可能未成功註冊至 Azure Migrate 專案」。
    - 請確定用來註冊應用裝置的 Azure 使用者帳戶至少具有訂用帳戶的參與者許可權。
    - [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)必要的 Azure 角色和許可權。
- 如果註冊失敗，因為找不到用來註冊應用裝置的 Azure Migrate 專案，錯誤60039「可能無法成功地將設備註冊到 Azure Migrate 專案」。
    - 在 Azure 入口網站中，檢查項目是否存在於資源群組中。
    - 如果專案不存在，請在您的資源群組中建立新的 Azure Migrate 專案，然後重新註冊設備。 [瞭解如何](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)建立新的專案。

## <a name="error-key-vault-management-operation-failed"></a>Error:Key Vault 管理操作失敗

如果您收到錯誤60030或60031「Azure Key Vault 管理作業失敗」，請執行下列動作：
- 請確定用來註冊應用裝置的 Azure 使用者帳戶至少擁有訂用帳戶的參與者許可權。
- 請確定帳戶可存取錯誤訊息中指定的金鑰保存庫，然後再次嘗試操作。
- 如果問題持續發生， 請連絡 Microsoft 支援服務。
- [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)必要的 Azure 角色和許可權。

## <a name="fix-discovery-couldnt-be-initiated"></a>修正：無法起始探索

錯誤60028：「無法起始探索，因為發生錯誤。 指定的主機或叢集清單的操作失敗，表示因為存取或抓取 VM 資訊時發生問題，所以無法在錯誤中所列的主機上啟動探索。 已成功新增其餘的主機。

- 使用 [**新增主機**] 選項，再次新增錯誤中所列的主機。
- 如果發生驗證錯誤，請檢查補救指引以修正錯誤，然後再次嘗試 [**儲存並啟動探索**] 選項。

## <a name="fix-azure-ad-operation-failed-60025"></a>修正：Azure AD 操作失敗（60025）

錯誤60025：「Azure AD 操作失敗。 當用來起始探索的 Azure 使用者帳戶不同于用來註冊應用裝置的帳戶時，建立或更新 Azure AD 應用程式時發生錯誤。 執行下列其中一項：

- 確定起始探索的使用者帳戶與用來註冊應用裝置的帳戶相同。
- 針對探索作業失敗的使用者帳戶，提供 Azure Active Directory 的應用程式存取權限。
- 刪除先前為 Azure Migrate 專案所建立的資源群組。 建立另一個資源群組以重新開機。
- [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)Azure Active Directory 應用程式許可權。


## <a name="discovered-vms-not-in-portal"></a>探索到的 Vm 不在入口網站中

如果您啟動探索，讓**伺服器評估**和**伺服器遷移**顯示**探索正在進行**中，但在入口網站中還沒有看到 vm，請注意下列事項：

- 從設備開始探索之後，會需要大約15分鐘的時間來進行 VMware VM 探索，而每個新增的主機會大約兩分鐘，以進行 Hyper-v VM 探索。
- 如果您在這些等待期間之後仍然看到 [**正在進行探索**]，**請選取**[**伺服器**] 索引標籤上的 [重新整理]。這應該會顯示**伺服器評估**和**伺服器遷移**中探索到的伺服器計數。


## <a name="deleted-vms-in-the-portal"></a>已在入口網站中刪除 Vm

如果您已部署會持續探索內部部署環境的設備，但已刪除的 Vm 仍會顯示在入口網站中，請注意下列事項：  

- 最多需要30分鐘的時間，應用裝置所收集的探索資料才會反映在入口網站中。
- 如果您在30分鐘後看不到最新資訊，請遵循下列步驟來重新整理資料：

    1. 在 [**伺服器** > **Azure Migrate 伺服器評估**] 中，選取 **[總覽**]。
    2. 在 **管理** 底下，選取 **代理程式健全狀況**
    3. 選取 [重新整理**代理程式**]。
    1. 等待重新整理作業完成。 您現在應該會看到最新的資訊。

## <a name="vm-information-isnt-in-the-portal"></a>VM 資訊不在入口網站中

- 最多需要30分鐘的時間，應用裝置所收集的探索資料才會反映在入口網站中。
- 如果您在30分鐘後看不到最新資訊，請遵循下列步驟來重新整理資料：

    1. 在 [**伺服器** > **Azure Migrate 伺服器評估**] 中，選取 **[總覽**]。
    2. 在 **管理** 底下，選取 **代理程式健全狀況**
    3. 選取 [重新整理**代理程式**]。
    1. 等待重新整理作業完成。 您現在應該會看到最新的資訊。


## <a name="fix-cant-connect-to-host-or-cluster"></a>修正：無法連接到主機或叢集

錯誤50004：「無法連接到主機或叢集，因為無法解析伺服器名稱。 WinRM 錯誤碼：如果設備的 Azure DNS 服務無法解析您提供的叢集或主機名稱，就可能會發生0x803381B9。

- 如果您在叢集上看到此錯誤，請叢集 FQDN。
- 對於叢集中的主機，您可能也會看到此錯誤。 這表示設備可以連接到叢集，但叢集會傳回不是 Fqdn 的主機名稱。 若要解決此錯誤，請新增 IP 位址和主機名稱的對應，以更新設備上的 hosts 檔案：
    1. 以系統管理員身分開啟 [記事本]。
    2. 開啟 C:\Windows\System32\Drivers\etc\hosts 檔案。
    3. 在資料列中新增 IP 位址和主機名稱。 針對您看到此錯誤的每個主機或叢集重複此動作。
    4. 儲存並關閉 hosts 檔案。
    5. 使用設備管理應用程式，檢查設備是否可連線至主機。 30分鐘後，您應該會在 Azure 入口網站中看到這些主機的最新資訊。



## <a name="fix-assessment-readiness"></a>修正評估準備就緒

修正評估就緒問題，如下所示：

**問題** | 修正
--- | ---
不支援的開機類型 | Azure 不支援具有 EFI 開機類型的 Vm。 建議您在執行遷移之前，先將開機類型轉換成 BIOS。 <br/><br/>您可以使用 Azure Migrate 伺服器遷移來處理這類 Vm 的遷移。 在遷移期間，它會將 VM 的開機類型轉換成 BIOS。
有條件支援的 Windows 作業系統 | 作業系統已通過其終止支援的日期，且需要自訂支援合約（CSA）以[在 Azure 中支援](https://aka.ms/WSosstatement)。 在遷移至 Azure 之前，請考慮升級。
不支援的 Windows 作業系統 | Azure 僅支援[選取的 WINDOWS OS 版本](https://aka.ms/WSosstatement)。 在遷移至 Azure 之前，請考慮先升級電腦。
有條件認同的 Linux OS | Azure 只會背書[選取的 LINUX 作業系統版本](../virtual-machines/linux/endorsed-distros.md)。 在遷移至 Azure 之前，請考慮先升級電腦。
不認同的 Linux OS | 電腦可能會在 Azure 中啟動，但 Azure 不會提供作業系統支援。 在遷移至 Azure 之前，請考慮升級至已[背書的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
作業系統不明 | VM 的作業系統在 vCenter Server 中指定為「其他」。 此行為會封鎖 Azure Migrate 確認 VM 的 Azure 是否就緒。 在您遷移機器之前，請確定 Azure[支援](https://aka.ms/azureoslist)此作業系統。
不支援的位版本 | 具有32位作業系統的 Vm 可能會在 Azure 中開機，但建議您先升級至64位，然後再遷移至 Azure。
需要 Microsoft Visual Studio 訂用帳戶 | 機器執行的是 Windows 用戶端作業系統，只有透過 Visual Studio 訂用帳戶才支援。
找不到所需儲存體效能的 VM | 機器所需的儲存體效能（每秒輸入/輸出作業數 [IOPS] 和輸送量）超過 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到所需網路效能的 VM | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。
在指定的位置中找不到 VM | 在移轉之前，使用不同的目標位置。
一或多個不適用的磁碟 | 連接至 VM 的一或多個磁片不符合 Azure 需求。為<br/><br/> Azure Migrate：伺服器評估目前不支援 Ultra SSD 磁片，並會根據 premium 受控磁片的磁片限制（32 TB）來評估磁片。<br/><br/> 針對連接至 VM 的每個磁片，請確定磁片的大小為 < 64 TB （Ultra SSD 磁片支援）。<br/><br/> 如果不是，請在遷移至 Azure 之前先減少磁片大小，或在 Azure 中使用多個磁片，並將它們等量放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以取得更高的儲存空間限制。 請確定 Azure[受管理的虛擬機器磁片](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)支援每個磁片所需的效能（IOPS 和輸送量）。
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | Azure Migrate：伺服器評估目前不支援 Ultra SSD 磁片，並會根據 premium 磁片限制（32 TB）來評估磁片。<br/><br/> 不過，Azure 支援的磁片大小上限為 64 TB （Ultra SSD 磁片支援）。 在遷移之前，將磁片壓縮成小於 64 TB，或在 Azure 中使用多個磁片，並將它們等量放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以取得更高的儲存體限制
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
因為發生內部錯誤，所以無法判斷磁片適用性 | 嘗試建立群組的新評估。
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到適合的 VM 類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。
因為發生內部錯誤，所以無法判斷 VM 適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤，所以無法判斷一或多個磁片的適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤，所以無法判斷一或多個網路介面卡的適用性 | 嘗試建立群組的新評估。

## <a name="linux-vms-are-conditionally-ready"></a>Linux Vm 已「有條件地準備就緒」

伺服器評估會將 Linux Vm 標示為「有條件地準備好」，因為伺服器評估有已知的差距。

- 此缺口會使其無法偵測內部部署 Vm 上所安裝之 Linux OS 的次要版本。
- 例如，針對 RHEL 6.10，目前的伺服器評估只會偵測 RHEL 6 做為作業系統版本。
-  因為 Azure 只背書特定版本的 Linux，所以 Linux Vm 目前在伺服器評估中會標示為有條件地準備就緒。
- 您可以藉由查看[Azure Linux 支援](https://aka.ms/migrate/selfhost/azureendorseddistros)，判斷在內部部署 VM 上執行的 Linux 作業系統是否已背書 azure。
-  確認背書的散發套件之後，您可以忽略此警告。

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure Sku 超過內部部署大小

根據評量類型，Azure Migrate Server 評估可能會建議具有更多核心和記憶體的 Azure VM Sku，而不是目前的內部部署配置：


- VM SKU 建議取決於評量屬性。
- 這會受到您在伺服器評估中執行的評量類型所影響：以*效能為基礎*，或*作為內部部署*。
- 針對以效能為基礎的評量，伺服器評估會考慮內部部署 Vm （CPU、記憶體、磁片和網路使用率）的使用量資料，以判斷內部部署 Vm 的正確目標 VM SKU。 在決定有效的使用率時，也會增加緩和因素。
- 若為內部部署調整大小，則不會考慮效能資料，而且建議您根據內部部署配置來使用目標 SKU。

為了說明這會如何影響建議，讓我們來看一個範例：

我們有一個內部部署 VM 具有四個核心和 8 GB 的記憶體，具有 50% CPU 使用率和 50% 的記憶體使用率，以及指定的緩和因數1.3。

-  如果評估為**內部部署**，則建議使用具有4個核心和 8 GB 記憶體的 AZURE VM SKU。
- 如果評量是以效能為基礎，根據有效的 CPU 和記憶體使用率（4個核心的 50% * 1.3 = 2.6 核心和 50% 的 8 GB 記憶體 * 1.3 = 5.3-GB 記憶體），四個核心的最低 VM SKU （最接近支援的核心計數）和 8 GB 的記憶體（最接近支援的）記憶體大小）建議。
- [深入瞭解](concepts-assessment-calculation.md#sizing)評量大小。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>大於內部部署的 Azure 磁片 Sku

Azure Migrate Server 評估可能會根據評量的類型，建議較大的磁片。
- 伺服器評估中的磁片大小取決於兩個評估屬性：調整大小準則和儲存體類型。
- 如果調整大小準則是以**效能為基礎**，且儲存體類型設定為 [**自動**]，則識別目標磁片類型（標準 HDD、標準 SSD 或高階）時，會考慮磁片的 IOPS 和輸送量值。 然後建議使用磁片類型的磁片 SKU，而建議會考慮內部部署磁片的大小需求。
- 如果調整大小準則是以**效能為基礎**，且儲存體類型為**Premium**，則根據內部部署磁片的 IOPS、輸送量和大小需求，建議 Azure 中的 Premium 磁片 SKU。 當調整大小準則是**作為內部部署**，且儲存類型為**標準 HDD**、**標準 SSD**或**Premium**時，會使用相同的邏輯來執行磁片大小調整。

例如，如果您的內部部署磁片具有 32 GB 的記憶體，但磁片的匯總讀取和寫入 IOPS 是 800 IOPS，伺服器評估會建議 premium 磁片（因為 IOPS 需求較高），然後建議可支援 r 的磁片 SKU必備 IOPS 和大小。 在此範例中最接近的相符項目為 P15 (256 GB、1100 IOPS)。 即使內部部署磁片所需的大小為 32 GB，伺服器評估還是會建議較大的磁片，因為內部部署磁片的高 IOPS 需求。

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>修正：已使用的核心或遺失的記憶體百分比

當 Azure Migrate 設備無法收集相關內部部署 Vm 的效能資料時，伺服器評估會回報 "PercentageOfCoresUtilizedMissing" 或 "PercentageOfMemoryUtilizedMissing"。

- 如果在評估期間關閉 Vm，就會發生這種情況。 應用裝置關閉時，無法收集 VM 的效能資料。
- 如果只有記憶體計數器遺失，而且您正嘗試評估 Hyper-v Vm，請檢查您是否已在這些 Vm 上啟用動態記憶體。 只有 Hyper-v Vm 的已知問題，在此情況下，Azure Migrate 設備無法收集未啟用動態記憶體之 Vm 的記憶體使用量資料。
- 如果遺失任何效能計數器，Azure Migrate Server 評估會降回已配置的核心和記憶體，並建議對應的 VM 大小。

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>成本評估包含 VM OS 授權成本嗎？

Azure Migrate Server 評估目前只考慮 Windows 機器的作業系統授權成本。 目前未考慮到 Linux 機器的授權成本。

## <a name="performance-history-and-percentile-use"></a>效能歷程記錄和百分位數使用

這些屬性僅適用于以效能為基礎的大小調整，Azure Migrate Server 評估。

伺服器評量會持續收集內部部署機器的效能資料，並用這些資料來建議 Azure 中的 VM SKU 和磁碟 SKU。 伺服器評估會收集此效能資料，如下所示：
- Azure Migrate 設備會持續分析內部部署環境，以收集 VMware Vm 每20秒的即時使用量資料，以及每30秒的 Hyper-v Vm。
- 設備會匯總20或30秒的樣本，以建立每10分鐘的單一資料點。 若要建立單一資料點，設備會從所有20秒和30秒範例中選取尖峰值，然後將其傳送至 Azure。
- 當您在伺服器評量中建立評量時，系統會根據效能持續時間和效能記錄的百分位數值，找出具代表性的使用量值。 例如，如果效能歷程記錄是一周，而百分位數使用率為第95個，Azure Migrate 會以遞增順序排序過去一周的所有10分鐘樣本點，然後選取第95個百分位數做為代表值。
- 第95個百分位數值可確保您忽略任何極端值，這可能會在您挑選第99個百分位數時包含。
- 如果您想要挑選週期的尖峰使用量，而不想錯過任何極端值，您應該選取第99個百分位數來表示百分位數的使用率。



## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>找不到 Azure Government 的相依性視覺效果

Azure Migrate 取決於相依性視覺效果功能的服務對應。 因為服務對應目前無法在 Azure Government 中使用，所以 Azure Government 不提供這項功能。

## <a name="dependencies-dont-show-after-installing-agents"></a>安裝代理程式之後不會顯示相依性


在內部部署 Vm 上安裝相依性視覺化代理程式之後，Azure Migrate 通常需要15-30 分鐘的時間，才能在入口網站中顯示相依性。 如果您已等候超過30分鐘，請確定 Microsoft Monitoring Agent （MMA）可以連線到 Log Analytics 工作區。

若為 Windows VM：
1. 在 [控制台] 中，啟動 MMA。
2. 在**Microsoft Monitoring Agent 屬性** > **Azure LOG Analytics （OMS）** 中，請確定工作區的**狀態**為綠色。
3. 如果狀態不是綠色，請嘗試移除工作區，然後再次將它新增至 MMA。

      ![MMA 屬性對話方塊](./media/troubleshooting-general/mma-status.png)

針對 Linux Vm，請確定 MMA 和 dependency agent 的安裝命令已成功。

## <a name="supported-mma-os"></a>支援的 MMA 作業系統

 檢查支援的[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)和[Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)作業系統。

## <a name="supported-dependency-agent-os"></a>支援的相依性代理程式作業系統

檢查支援的[Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)作業系統。

## <a name="dependencies-for-more-than-an-hour"></a>相依性超過一小時

雖然 Azure Migrate 可讓您回到上個月的特定日期，但您可將相依性視覺化的最大持續時間為一小時。

例如，您可以使用相依性對應中的 [持續時間] 功能來查看昨天的相依性，但只能在一小時的期間內進行查看。

不過，您可以使用 Azure 監視器記錄，在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>我無法將具有超過10個 Vm 的群組的相依性視覺化

在 Azure Migrate Server 評估中，您可以將最多10個 Vm 的群組相依性[視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 針對較大的群組，建議您將 Vm 分割成較小的群組，以將相依性視覺化。

## <a name="machines-show-install-agent-not-view-dependencies"></a>電腦顯示「安裝代理程式」而不是「查看相依性」

將已啟用相依性視覺效果的機器遷移至 Azure 之後，電腦可能會因為下列行為而顯示「安裝代理程式」動作，而不是「查看相依性」：


- 遷移至 Azure 之後，會關閉內部部署機器，而對等 Vm 會在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。
- 根據您是否已保留內部部署 IP 位址，機器可能也會有不同的 IP 位址。
- 如果 MAC 和 IP 位址都與內部部署不同，Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯。 在此情況下，它會顯示安裝代理程式的選項，而不是用來查看相依性。
- 將測試遷移至 Azure 之後，內部部署機器仍會如預期般開啟。 在 Azure 中啟動的對等電腦會取得不同的 MAC 位址，而且可能會取得不同的 IP 位址。 除非您封鎖來自這些電腦的傳出 Azure 監視器記錄流量，否則 Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯，因此會顯示安裝代理程式的選項，而不是查看相依性。


## <a name="collect-network-traffic-logs-in-portal"></a>在入口網站中收集網路流量記錄

收集記錄檔，如下所示：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按 F12 啟動開發人員工具。 如有需要，請清除 [**清除導覽上的專案**] 設定。
3. 選取 [**網路**] 索引標籤，然後開始捕獲網路流量：
   - 在 Chrome 中，選取 [保留記錄]。 記錄應該會自動啟動。 紅色圓圈表示正在捕捉流量。 如果未出現紅色圓圈，請選取要啟動的黑色圓圈。
   - 在 Microsoft Edge 和 Internet Explorer 中，記錄應該會自動啟動。 如果不是，請選取綠色的 [播放] 按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
   - 在 Chrome 中，以滑鼠右鍵按一下並選取 [**與內容一起另存為 HAR**]。 此動作會將記錄壓縮並匯出為 har 檔案。
   - 在 Microsoft Edge 或 Internet Explorer 中，選取 [**匯出已捕捉的流量**] 選項。 此動作會壓縮並匯出記錄檔。
6. 選取 [**主控台**] 索引標籤，以檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
   - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選取 [**另存**新檔]，匯出並壓縮記錄檔。
   - 在 Microsoft Edge 或 Internet Explorer 中，以滑鼠右鍵按一下錯誤，然後選取 [**全部複製**]。
7. 關閉 Developer Tools。
