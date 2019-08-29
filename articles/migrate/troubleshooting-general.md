---
title: 為 Azure Migrate 問題進行疑難排解 | Microsoft Docs
description: 提供 Azure Migrate 服務中已知問題的總覽, 以及常見錯誤的疑難排解秘訣。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142312"
---
# <a name="troubleshoot-azure-migrate"></a>為 Azure Migrate 疑難排解

[Azure Migrate](migrate-services-overview.md)提供評估和遷移工具的中樞, 以及協力廠商獨立軟體廠商 (ISV) 產品。 本文可協助您針對 Azure Migrate、Azure Migrate 伺服器評估和 Azure Migrate 伺服器遷移的錯誤進行疑難排解。

## <a name="azure-migrate-project-issues"></a>Azure Migrate 專案問題

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>我找不到現有的 Azure Migrate 專案。

Azure Migrate 有[兩個版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)。 視您建立專案的版本而定, 請使用下列其中一種方法:

* 如果您要尋找使用 Azure Migrate 的舊版 (舊體驗) 所建立的專案, 請遵循下列步驟來尋找專案:

    1. 移至 [ [Azure 入口網站](https://portal.azure.com)], 然後搜尋**Azure Migrate**。
    2. 在 [Azure Migrate] 儀表板上, 您會看到一則提及存取較舊專案的橫幅。 只有在您已建立具有舊體驗的專案時, 才會看到此橫幅。 選取橫幅。

       ![存取現有的專案](./media/troubleshooting-general/access-existing-projects.png)

    3. 您現在會看到使用舊版 Azure Migrate 建立的現有專案清單。

* 如果您要尋找使用目前版本 (新體驗) 所建立的專案, 請遵循下列步驟來尋找專案:

    1. 移至 [ [Azure 入口網站](https://portal.azure.com)], 然後搜尋**Azure Migrate**。
    2. 在 [Azure Migrate 儀表板] 上, 移至左窗格中的 [**伺服器**] 頁面, 然後選取右上角的 [**變更**]。

       ![切換至現有的 Azure Migrate 專案](./media/troubleshooting-general/switch-project.png)

    3. 選取適當的訂用帳戶, 並 Azure Migrate 專案。

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>如何? 建立第二個 Azure Migrate 專案？

請遵循下列步驟來建立新的 Azure Migrate 專案:

1. 移至 [ [Azure 入口網站](https://portal.azure.com)], 然後搜尋**Azure Migrate**。
2. 在 [Azure Migrate 儀表板] 上, 移至左窗格中的 [**伺服器**] 頁面, 然後選取右上角的 [**變更**]。

   ![變更 Azure Migrate 專案](./media/troubleshooting-general/switch-project.png)

3. 若要建立新的專案, 請選取 [**按一下這裡**]。

   ![建立第二個 Azure Migrate 專案](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Azure Migrate 支援哪些 Azure 地理位置？

請參閱適用于[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)的清單。

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>如何? 刪除 Azure Migrate 專案和相關聯的 Log Analytics 工作區？

當您刪除 Azure Migrate 專案時, 會刪除與探索到的機器相關的遷移專案*和*中繼資料。 不過, 如果您已將 Log Analytics 工作區附加至伺服器評估工具, 則不會自動刪除 Log Analytics 工作區。 (相同的 Log Analytics 工作區可以用於多個使用案例)。如果您也想要刪除 Log Analytics 工作區, 您必須手動執行此動作:

1. 瀏覽到附加到專案的 Log Analytics 工作區。
     * 如果您尚未刪除遷移專案, 您可以從 [基本功能] 區段中的 [伺服器評估總覽] 頁面找到工作區的連結。

       ![LA 工作區](./media/troubleshooting-general/loganalytics-workspace.png)

     * 如果您已刪除遷移專案, 請在 Azure 入口網站的左窗格中選取 [**資源群組**]。 移至工作區建立所在的資源群組, 然後流覽至其中。
2. 請依照[刪除具有 Azure 入口網站的 Azure Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)中的指示進行。

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>因為「要求必須包含使用者識別標頭」錯誤, 所以無法建立遷移專案。

若使用者無法存取組織的 Azure Active Directory (Azure AD) 租使用者, 則可能會發生此問題。 第一次將使用者新增至 Azure AD 租使用者時, 使用者會收到加入租使用者的電子郵件邀請。 使用者必須接受邀請, 才能成功新增至租使用者。 如果您看不到此電子郵件, 請洽詢已經有權存取租使用者的使用者, 並使用將[邀請重新傳送給來賓使用者](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)中指定的步驟要求他們重新傳送邀請給您。

收到邀請電子郵件之後, 您必須開啟電子郵件, 然後選取要接受邀請的連結。 然後, 您必須登出 Azure 入口網站並重新登入。 (重新整理瀏覽器將無法使用)。然後, 您就可以開始建立遷移專案。

## <a name="appliance-issues"></a>設備問題

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>部署 VMware 的 Azure Migrate 設備失敗, 並出現「提供的資訊清單檔案無效:不正確 OVF 資訊清單專案」錯誤。

1. 藉由檢查其雜湊值, 確認已正確下載 Azure Migrate 設備 OVA 檔案。 如需指導方針, 請參閱[準備 VMware vm 以進行評估和遷移至 Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)。 如果雜湊值不相符, 請再次下載 OVA 檔案, 然後重試部署。
2. 如果部署仍然失敗, 而且您使用 VMware vSphere 用戶端來部署 OVF 檔案, 請嘗試透過 vSphere web 用戶端進行部署。 如果部署仍然失敗, 請嘗試使用不同的網頁瀏覽器。
3. 如果您使用 vSphere web 用戶端, 並嘗試在 vCenter Server 6.5 或6.7 上部署它, 請嘗試依照下列步驟直接在 ESXi 主機上部署 OVA:
   - 使用 web 用戶端 (HTTPs://<*主機 IP 位址*>/ui) 直接連接到 ESXi 主機 (而不是 vCenter Server)。
   - 前往 [**首頁** > **清查**]。
   - 選取 [檔案] [**部署 OVF 範本**]。  >  流覽至 OVA 並完成部署。
4. 如果部署仍然失敗, 請洽詢 Azure Migrate 支援。

### <a name="the-appliance-cant-connect-to-the-internet"></a>設備無法連線到網際網路。

當您使用的電腦位於 proxy 後方時, 可能會發生此行為。 如果 proxy 需要授權認證, 請務必提供這些認證。
如果您使用任何以 URL 為基礎的防火牆 proxy 來控制輸出連線能力, 請務必將這些必要的 Url 新增至允許清單:

- [適用于 VMware 的伺服器評估](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Hyper-v 的伺服器評估](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [適用于 VMware 的伺服器遷移 (無代理程式)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [適用于 VMware 的伺服器遷移 (以代理程式為基礎)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Hyper-v 的伺服器遷移](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

如果您使用攔截 proxy 來連線到網際網路, 您必須將 proxy 憑證匯入至應用裝置 VM。 您可以遵循[Azure Migrate 設備](https://docs.microsoft.com/azure/migrate/concepts-collector)中的步驟來匯入 proxy 憑證。

### <a name="error-802-date-and-time-synchronization-error"></a>錯誤 802：日期和時間同步處理錯誤。

伺服器時鐘可能不會與目前時間同步超過五分鐘。 請變更收集器虛擬機器的時間，使 之與目前的時間相符，方法如下所示：

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 若要檢查時區, 請執行**w32tm/tz**。
3. 若要同步處理時間, 請執行**w32tm/resync**。


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>連接失敗。 Error:UnableToConnectToServer.

您可能無法連接到 vCenter Server *Servername*.com: 9443。 錯誤詳細資料指出沒有在 HTTPs://*servername*.com: 9443/sdk 接聽的端點可以接受該訊息。

在此情況下, 請檢查您是否正在執行最新版的收集器設備。 如果不是, 請將設備升級至[最新版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。

如果問題仍然發生在最新版本中, 收集器機器可能無法解析指定的 vCenter Server 名稱, 或指定的埠可能錯誤。 根據預設, 如果未指定埠, 收集器會嘗試連接到埠號碼443。

1. 從收集器機器 Ping *Servername*.com。
2. 如果步驟1失敗, 請嘗試使用 IP 位址連接到 vCenter server。
3. 識別連線至 vCenter 的正確連接埠號碼。
4. 確認 vCenter server 已啟動且正在執行。


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>設備可能未成功註冊至 Azure Migrate 專案 (錯誤識別碼:60052)。

如果用來註冊應用裝置的 Azure 帳戶許可權不足, 就會發生此錯誤。 請確定用來註冊應用裝置的 Azure 使用者帳戶至少具有訂用帳戶的參與者許可權。 [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)必要的 Azure 角色和許可權。

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>設備可能未成功註冊至 Azure Migrate 專案 (錯誤識別碼:60039)。

找不到選取要註冊設備的 Azure Migrate 專案。 在此情況下, 註冊會失敗。 移至 Azure 入口網站, 並檢查項目是否存在於您的資源群組中。 如果專案不存在, 請在您的資源群組中建立新的 Azure Migrate 專案, 然後重新註冊設備。 [深入瞭解](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)如何建立新的 Azure Migrate 專案。

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Azure Key Vault 管理操作失敗 (錯誤識別碼:60030、60031)。

請確定用來註冊應用裝置的 Azure 使用者帳戶至少擁有訂用帳戶的參與者許可權。 此外, 請確定帳戶可存取錯誤訊息中指定的金鑰保存庫, 然後再次嘗試操作。 如果問題持續發生， 請連絡 Microsoft 支援服務。 [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)必要的 Azure 角色和許可權。

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>因為發生錯誤, 所以無法起始探索。 指定的主機或叢集清單的操作失敗 (錯誤識別碼:60028)。

因為存取或抓取 VM 資訊時發生問題, 所以無法在錯誤中所列的主機上啟動探索。 已成功新增其餘的主機。 使用 [**新增主機**] 選項, 再次新增錯誤中所列的主機。 如果發生驗證錯誤, 請檢查補救指引以修正錯誤, 然後再次嘗試 [**儲存並啟動探索**] 選項。

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Azure AD 操作失敗。 建立或更新 Azure AD 應用程式時發生錯誤 (錯誤識別碼:60025)。

如果用來起始探索的 Azure 使用者帳戶不同於用來註冊設備的帳戶，就會發生此錯誤。 您可以執行下列其中一項：
1. 確定起始探索的使用者帳戶與用來註冊應用裝置的帳戶相同。
1. 為探索作業失敗的其他使用者帳戶提供 AAD 應用程式存取權限。
1. 刪除先前為 Azure Migrate 專案所建立的資源群組, 然後建立另一個資源群組以重新開機。

[深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)AAD 應用程式許可權。

## <a name="discovery-issues"></a>探索問題

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>我已開始探索, 但在 Azure 入口網站上看不到探索到的 Vm。 **伺服器評估**和**伺服器遷移**磚的狀態會顯示為「正在探索」。
從設備開始探索之後, 請等候一段時間讓探索到的機器顯示在 Azure 入口網站上。 VMware 探索大約需要15分鐘的時間, 而且每個新增的主機可進行 Hyper-v 探索約2分鐘的時間。 如果您在這些等待期間之後仍然看到 [探索進行中], 請選取 [**伺服器**] 索引標籤上的 [重新整理]。這應該會顯示伺服器評估和伺服器遷移磚中探索到的伺服器計數。


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>我使用的是持續探索內部部署環境的設備, 但在我的內部部署環境中刪除的 Vm 仍會顯示在入口網站中。

最多需要30分鐘的時間, 應用裝置所收集的探索資料才會反映在入口網站中。 如果您在30分鐘後仍看不到最新資訊, 請遵循下列步驟來重新整理資料:

1. 移至 **伺服器** > **Azure Migrate Server 評估**, 然後選取**總覽**。
2. 在 **管理** 底下, 選取 **代理程式健全狀況**
3. 選取 [重新整理**代理程式**]。 您會在代理程式清單下方看到此選項。
4. 等待重新整理作業完成。 您現在應該會看到 Vm 上的最新資訊。

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>我在入口網站上看不到內部部署 Vm 的最新資訊。

最多需要30分鐘的時間, 應用裝置所收集的探索資料才會反映在入口網站上。 如果您在30分鐘後仍看不到最新資訊, 請遵循下列步驟來重新整理資料:

1. 移至 **伺服器** > **Azure Migrate Server 評估**, 然後選取**總覽**。
2. 在 **管理** 底下, 選取 **代理程式健全狀況**
3. 選取 [重新整理**代理程式**]。 您會在代理程式清單下方看到此選項。
4. 等待重新整理作業完成。 您現在應該會看到 Vm 上的最新資訊。

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>無法連接到主機或叢集, 因為無法解析伺服器名稱。 WinRM 錯誤碼:0x803381B9 (錯誤識別碼:50004)。
如果設備的 Azure DNS 服務無法解析您提供的叢集或主機名稱, 就會發生此錯誤。 如果您在叢集上看到此錯誤, 請嘗試提供叢集的完整功能變數名稱 (FQDN)。

對於叢集中的主機, 您可能也會看到此錯誤。 在此情況下, 設備可以連接到叢集, 但叢集會傳回不是 Fqdn 的主機名稱。

若要解決此錯誤, 請新增 IP 位址和主機名稱的對應, 以更新設備上的 hosts 檔案:
1. 以系統管理員身分開啟 [記事本]。 然後, 開啟 C:\Windows\System32\Drivers\etc\hosts 檔案。
2. 在資料列中新增 IP 位址和主機名稱。 針對您看到此錯誤的每個主機或叢集重複此動作。
3. 儲存並關閉 hosts 檔案。
4. 您可以使用設備管理應用程式來檢查設備是否可連線至主機。 30分鐘後, 您應該就能在 Azure 入口網站上查看這些主機的最新資訊。


## <a name="assessment-issues"></a>評定問題

### <a name="azure-readiness-issues"></a>Azure 就緒問題

問題 | 修復
--- | ---
不支援的開機類型 | Azure 不支援具有 EFI 開機類型的 Vm。 建議您在執行遷移之前, 先將開機類型轉換成 BIOS。 <br/><br/>您可以使用 Azure Migrate 伺服器遷移來處理這類 Vm 的遷移。 在遷移期間, 它會將 VM 的開機類型轉換成 BIOS。
有條件地支援 Windows 作業系統 | 作業系統已通過其終止支援, 並需要自訂支援合約 (CSA) 以[在 Azure 中支援](https://aka.ms/WSosstatement)。 在遷移至 Azure 之前, 請考慮先升級作業系統。
不支援的 Windows OS | Azure 僅支援[選取的 WINDOWS OS 版本](https://aka.ms/WSosstatement)。 在遷移至 Azure 之前, 請考慮先升級電腦的作業系統。
有條件認同的 Linux OS | Azure 只會背書[選取的 LINUX 作業系統版本](../virtual-machines/linux/endorsed-distros.md)。 在遷移至 Azure 之前, 請考慮先升級電腦的作業系統。
不認同的 Linux OS | 電腦可能會在 Azure 中啟動, 但 Azure 不會提供作業系統支援。 在遷移至 Azure 之前, 請考慮將 OS 升級至[背書的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
作業系統不明 | VM 的作業系統在 vCenter Server 中指定為「其他」。 此行為會封鎖 Azure Migrate 確認 VM 的 Azure 是否就緒。 在您遷移機器之前, 請先確定 Azure[支援](https://aka.ms/azureoslist)機器的 OS。
不支援的 OS 位元 | 具有32位 OS 的 Vm 可能會在 Azure 中開機, 但建議您在遷移至 Azure 之前, 先將 VM 的 OS 升級至64位。
需要 Microsoft Visual Studio 訂用帳戶 | 機器執行的是 Windows 用戶端作業系統, 只有透過 Visual Studio 訂用帳戶才支援。
找不到所需儲存體效能的 VM | 機器所需的儲存體效能 (每秒輸入/輸出作業數 [IOPS] 和輸送量) 超過 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到所需網路效能的 VM | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。
在指定的位置中找不到 VM | 在移轉之前，使用不同的目標位置。
一或多個不適用的磁碟 | 連接至 VM 的一或多個磁片不符合 Azure 需求。 Azure Migrate：伺服器評估目前不支援 Ultra SSD 磁片, 而且會根據 premium 受控磁片的磁片限制 (32 TB) 來評估磁片。  針對連接至 VM 的每個磁片, 請確定磁片的大小為 < 64 TB (Ultra SSD 磁片支援); 如果沒有, 請在遷移至 Azure 之前減少磁片大小, 或在 Azure 中使用多個磁片, 並將它們等量放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping), 以取得更高的儲存空間限制。 請確定 Azure[受管理的虛擬機器磁片](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)支援每個磁片所需的效能 (IOPS 和輸送量)。
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | Azure Migrate：伺服器評估目前不支援 Ultra SSD 磁片, 而且會根據 premium 磁片限制 (32 TB) 來評估磁片。 不過, Azure 支援的磁片大小上限為 64 TB (Ultra SSD 磁片支援)。 在遷移之前, 將磁片壓縮到小於 64 TB, 或在 Azure 中使用多個磁片, 並將[其](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)等量分割以取得更高的儲存體限制。
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
因為發生內部錯誤, 所以無法判斷磁片適用性 | 嘗試建立群組的新評估。
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到適合的 VM 類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。
因為發生內部錯誤, 所以無法判斷 VM 適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤, 所以無法判斷一或多個磁片的適用性 | 嘗試建立群組的新評估。
因為發生內部錯誤, 所以無法判斷一或多個網路介面卡的適用性 | 嘗試建立群組的新評估。

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>我無法在評量屬性中將 Enterprise 合約 (EA) 指定為 Azure 供應專案。
Azure Migrate Server 評估目前不支援以 Enterprise 合約 (EA) 為基礎的定價。 若要解決這項限制, 請使用隨**用隨付**作為 Azure 供應專案, 並使用**折扣**屬性來指定您收到的任何自訂折扣。 [深入了解如何自訂評量](https://aka.ms/migrate/selfhelp/eapricing)。

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>為什麼伺服器評量會標示我的 Linux Vm 「有條件地準備好」？
伺服器評估中有一個已知的間隔, 可防止它偵測內部部署 Vm 上所安裝的 Linux OS 次要版本 (例如, 針對 RHEL 6.10, 目前伺服器評估只會偵測到 RHEL 6 做為作業系統版本)。 因為 Azure 只背書特定版本的 Linux, 所以 Linux Vm 目前在伺服器評估中會標示為有條件地準備就緒。 您可以查看[Azure Linux 支援檔](https://aka.ms/migrate/selfhost/azureendorseddistros), 以判斷在內部部署 VM 上執行的 Linux 作業系統是否已背書。 確認背書的散發套件之後, 您可以忽略此警告。

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>為什麼伺服器評估所建議的 VM SKU 擁有比內部部署所配置的更多核心和更多的記憶體？
伺服器評量中的 VM SKU 建議取決於評量屬性。 您可以在伺服器評估中建立兩種評量:以*效能為基礎*, 且*為內部部署*。 針對以效能為基礎的評量, 伺服器評估會考慮內部部署 Vm (CPU、記憶體、磁片和網路使用率) 的使用量資料, 以判斷內部部署 Vm 的正確目標 VM SKU。 此外, 針對以效能為基礎的大小調整, 在判斷有效的使用率時, 會考慮緩和因素。 若為內部部署調整大小, 則不會考慮效能資料, 而且會根據內部部署所配置的內容建議目標 SKU。

例如, 假設有一個內部部署 VM 具有4個核心和 8 GB 的記憶體, 並具有 50% 的 CPU 使用率和 50% 的記憶體使用率, 而且評量中指定了緩和因數1.3。 如果評估的調整大小準則是**作為內部部署**, 建議使用具有4個核心和 8 gb 記憶體的 AZURE VM SKU。

不過, 假設調整大小準則是以效能為基礎。 根據有效的 CPU 和記憶體使用率 (4 核心的 50% * 1.3 = 2.6 核心和 50% 的 8 GB 記憶體 * 1.3 = 5.3-GB 記憶體), 建議使用四個核心 (最接近支援的核心計數) 和 8 GB 記憶體 (最接近支援的記憶體大小) 的最便宜 VM SKU。 [深入了解伺服器評量執行大小調整的方式](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)。

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>為什麼伺服器評估所建議的磁片 SKU 大於內部部署所配置的？
伺服器評估中的磁片大小取決於兩個評估屬性: 調整大小準則和儲存體類型。 如果調整大小準則是以**效能為基礎**, 且儲存體類型設定為 [**自動**], 則會考慮磁片的 IOPS 和輸送量值, 以識別目標磁片類型 (標準 HDD、標準 SSD 或 Premium 磁片)。 然後建議使用磁片類型的磁片 SKU, 而此建議會考慮內部部署磁片的大小需求。 如果調整大小準則是以**效能為基礎**, 且儲存體類型為**Premium**, 則根據內部部署磁片的 IOPS、輸送量和大小需求, 建議 Azure 中的 Premium 磁片 SKU。 當調整大小準則是**作為內部部署**, 且儲存類型為**標準 HDD**、**標準 SSD**或**Premium**時, 會使用相同的邏輯來執行磁片大小調整。

例如, 如果您的內部部署磁片具有 32 GB 的記憶體, 但磁片的匯總讀取和寫入 IOPS 是 800 IOPS, 則伺服器評估會建議使用 premium 磁片類型 (因為 IOPS 需求較高), 然後建議可支援的磁片 SKU所需的 IOPS 和大小。 在此範例中最接近的相符項目為 P15 (256 GB、1100 IOPS)。 即使內部部署磁片所需的大小為 32 GB, 伺服器評估還是會建議較大的磁片, 因為內部部署磁片的高 IOPS 需求。

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>為什麼我的評量報告指出某些 Vm 的「PercentageOfCoresUtilizedMissing」或「PercentageOfMemoryUtilizedMissing」？
當 Azure Migrate 設備無法收集內部部署 Vm 的效能資料時, 就會回報這些問題。 當您建立評量 (最後一天/一周/一個月) 時, 如果 Vm 已關閉, 就可能發生這種行為。 應用裝置關閉時, 無法收集 VM 的效能資料。 如果只有記憶體計數器遺失, 而且您正嘗試評估 Hyper-v Vm, 請檢查您是否已在這些 Vm 上啟用動態記憶體。 有一個已知的問題, 就是 Azure Migrate 設備無法收集未啟用動態記憶體之 Vm 的記憶體使用量資料。 此問題只會影響 Hyper-v Vm, 而不會影響 VMware Vm。 如果遺失任何效能計數器, Azure Migrate Server 評估會降回已配置的核心和記憶體, 並建議對應的 VM 大小。

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>VM 的 OS 授權成本是否包含在伺服器評估所估計的計算成本中？
伺服器評估目前只考慮 Windows 機器的 OS 授權成本。 目前未考慮 Linux 機器的 OS 授權成本。

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>效能歷程記錄和百分位數使用率對大小建議有何影響？
這些屬性僅適用于以效能為基礎的大小調整。 伺服器評量會持續收集內部部署機器的效能資料，並用這些資料來建議 Azure 中的 VM SKU 和磁碟 SKU。 伺服器評估會收集此效能資料, 如下所示:
- Azure Migrate 設備會持續分析內部部署環境, 以收集 VMware Vm 每20秒的即時使用量資料, 以及每30秒的 Hyper-v Vm。
- 設備會匯總20秒和30秒的樣本, 以建立每10分鐘一次的單一資料點。 若要建立單一資料點, 設備會從所有20秒和30秒範例中選取尖峰值, 然後將其傳送至 Azure。
- 當您在伺服器評量中建立評量時，系統會根據效能持續時間和效能記錄的百分位數值，找出具代表性的使用量值。 例如, 如果效能歷程記錄是一周, 而百分位數使用率為第95個, Azure Migrate 會以遞增順序排序過去一周的所有10分鐘樣本點, 然後選取第95個百分位數做為代表值。
第95個百分位數值可確保您忽略任何極端值, 這可能會在您挑選第99個百分位數時包含。 如果您想要挑選週期的尖峰使用量, 而不想錯過任何極端值, 您應該選取第99個百分位數來表示百分位數的使用率。

## <a name="dependency-visualization-issues"></a>相依性視覺效果問題

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>我找不到 Azure Government 專案的相依性視覺效果功能。

Azure Migrate 取決於相依性視覺效果功能的服務對應。 因為服務對應目前無法在 Azure Government 中使用, 所以 Azure Government 不提供這項功能。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>我在內部部署 VM 上安裝了 Microsoft Monitoring Agent (MMA) 和相依性代理程式，但相依性未出現在 Azure Migrate 入口網站中。

安裝代理程式之後, Azure Migrate 通常需要15-30 分鐘的時間, 才能在入口網站中顯示相依性。 如果您已等候30分鐘以上, 請遵循下列步驟, 確定 MMA 可以與 OMS 工作區通訊。

針對 Windows VM：
1. 移至 [控制台], 然後啟動 Microsoft Monitoring Agent。
2. 在 [ **Microsoft Monitoring Agent 屬性**] 對話方塊的 [ **Azure LOG Analytics (OMS)** ] 索引標籤上, 確認工作區的**狀態**為綠色。
3. 如果狀態不是綠色，請嘗試移除該工作區，然後再次新增至 MMA。

      ![MMA 屬性對話方塊](./media/troubleshooting-general/mma-status.png)

針對 Linux VM, 請確定 MMA 和 dependency agent 的安裝命令已成功。

### <a name="what-operating-systems-does-mma-support"></a>MMA 支援哪些作業系統？

 [以下是 MMA 所支援的 Windows 作業系統清單](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)。
[以下是 MMA 所支援的 Linux 作業系統清單](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>相依性代理程式支援哪些作業系統？

[以下是[適用於 VM 的 Azure 監視器支援的 Windows 和 Linux 作業系統](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)清單。

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>我無法將 Azure Migrate 中的相依性視覺化超過一小時的持續時間。
在 Azure Migrate 中, 您可以將相依性視覺化到一小時的持續時間。 雖然 Azure Migrate 可讓您回到上個月的特定日期, 但您可將相依性視覺化的最大持續時間為一小時。

例如, 您可以使用相依性對應中的持續時間功能來查看昨天的相依性, 但您只能在一小時內查看它們。 不過, 您可以使用 Azure 監視器記錄, 在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>我無法將具有超過10個 Vm 的群組的相依性視覺化。
如果您的群組具有10部以上的 Vm, 您可以將群組的相依性[視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies), 如果您有一組以上的 vm, 建議您將該群組分割成較小的群組, 並將相依性視覺化。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>我已經安裝代理程式，並使用相依性視覺化建立群組。 現在, 容錯移轉後, 機器會顯示「安裝代理程式」動作, 而不是「查看相依性」。
* 在計畫或非計畫的容錯移轉之後, 內部部署機器會關閉, 而對等機器會在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。 根據使用者是否選擇保留內部部署 IP 位址, 他們可能會取得不同的 IP 位址。

  如果 MAC 和 IP 位址不同, Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯。 相反地, 它會要求使用者安裝代理程式, 而不是查看相依性。
* 測試後容錯移轉之後, 內部部署機器仍會如預期般開啟。 在 Azure 中啟動的對等電腦會取得不同的 MAC 位址, 而且可能會取得不同的 IP 位址。 除非使用者封鎖來自這些電腦的傳出 Azure 監視器記錄流量, 否則 Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯, 而會要求使用者安裝代理程式, 而不是查看相依性。

## <a name="collect-azure-portal-logs"></a>收集 Azure 入口網站記錄

**如何? 收集 Azure 入口網站的網路流量記錄檔嗎？**

1. 開啟瀏覽器, 移[至入口網站](https://portal.azure.com)並登入。
2. 按 F12 啟動開發人員工具。 如有需要, 請清除 [**清除導覽上的專案**] 設定。
3. 選取 [**網路**] 索引標籤, 然後開始捕獲網路流量:
   - 在 Chrome 中，選取 [保留記錄]。 記錄應該會自動啟動。 紅色圓圈表示正在捕捉流量。 如果未出現紅色圓圈, 請選取要啟動的黑色圓圈。
   - 在 Microsoft Edge 和 Internet Explorer 中, 記錄應該會自動啟動。 如果不是, 請選取綠色的 [播放] 按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
   - 在 Chrome 中, 以滑鼠右鍵按一下並選取 [**與內容一起另存為 HAR**]。 此動作會將記錄壓縮並匯出為 har 檔案。
   - 在 Microsoft Edge 或 Internet Explorer 中, 選取 [**匯出已捕捉的流量**] 選項。 此動作會壓縮並匯出記錄檔。
6. 選取 [**主控台**] 索引標籤, 以檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
   - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選取 [**另存**新檔], 匯出並壓縮記錄檔。
   - 在 Microsoft Edge 或 Internet Explorer 中, 以滑鼠右鍵按一下錯誤, 然後選取 [**全部複製**]。
7. 關閉 Developer Tools。
