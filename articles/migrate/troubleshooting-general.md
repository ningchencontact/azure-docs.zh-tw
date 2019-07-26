---
title: 為 Azure Migrate 問題進行疑難排解 | Microsoft Docs
description: 概括介紹 Azure Migrate 服務的已知問題以及常見錯誤的疑難排解訣竅。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 15d3809b9a028fd2495c504e9bf19251dd051520
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372594"
---
# <a name="troubleshoot-azure-migrate"></a>為 Azure Migrate 疑難排解

[Azure Migrate](migrate-services-overview.md)提供 Microsoft 工具的中樞, 以進行評估和遷移, 以及協力廠商獨立軟體廠商 (ISV) 產品。 本檔提供針對 Azure Migrate 的錯誤進行疑難排解的協助, Azure Migrate:伺服器評估和 Azure Migrate:伺服器移轉。

## <a name="azure-migrate-project-issues"></a>Azure Migrate 專案問題

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>我找不到現有的 Azure Migrate 專案。

Azure Migrate 有[兩個版本](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)。 視您建立專案的版本而定, 請遵循下列步驟來尋找專案:

1. 如果您要尋找使用 Azure Migrate 的舊版本 (舊體驗) 所建立的專案, 請遵循下列步驟來尋找專案。

    1. 移至[Azure 入口網站](https://portal.azure.com), 搜尋**Azure Migrate**。
    2. 在 [Azure Migrate] 儀表板上, 您會看到一則討論如何存取較舊專案的橫幅。 只有在您已建立具有舊體驗的專案時, 才會看到此橫幅。 按一下橫幅。

    ![存取現有的專案](./media/troubleshooting-general/access-existing-projects.png)

    3. 您現在會看到使用舊版 Azure Migrate 建立的現有專案清單。

2. 如果您要尋找使用目前版本 (新體驗) 所建立的專案, 請遵循下列步驟來尋找專案。

    1. 移至[Azure 入口網站](https://portal.azure.com), 搜尋**Azure Migrate**。
    2. 在 [Azure Migrate 儀表板] 上, 移至左窗格中的 [**伺服器**] 頁面, 然後選取右上角的 [**變更**]:

    ![切換至現有的 Azure Migrate 專案](./media/troubleshooting-general/switch-project.png)

    3. 選取適當的 [**訂**用帳戶] 和 [**遷移專案**]。

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>我無法建立第二個 Azure Migrate 專案。

請遵循下列步驟來建立新的 Azure Migrate 專案。

1. 移至[Azure 入口網站](https://portal.azure.com), 搜尋**Azure Migrate**。
2. 在 [Azure Migrate 儀表板] 上, 移至左窗格中的 [**伺服器**] 頁面, 然後選取右上角的 [**變更**]:

   ![變更 Azure Migrate 專案](./media/troubleshooting-general/switch-project.png)

3. 若要建立新的專案, 請選取 [**按一下這裡**], 如下列螢幕擷取畫面所示:

   ![建立第二個 Azure Migrate 專案](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate 支援哪些 Azure 地理區？

您可以在這裡找到適用于[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)的清單, 以及[這裡的 hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)。

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>刪除 Azure Migrate 專案和相關的 Log Analytics 工作區

當您刪除 Azure Migrate 專案時, 它會刪除遷移專案以及有關探索到之電腦的中繼資料。 不過, 如果您已將 Log Analytics 工作區附加至伺服器評估工具, 它不會自動刪除 Log Analytics 工作區。 這是因為相同的 Log Analytics 工作區可能已用於多個使用案例。 如果您也想要刪除 Log Analytics 工作區，便必須手動進行。

1. 瀏覽到附加到專案的 Log Analytics 工作區。
     1. 如果您尚未刪除遷移專案, 您可以從 [基本功能] 區段中的 [伺服器評估總覽] 頁面找到工作區的連結。

     ![LA 工作區](./media/troubleshooting-general/loganalytics-workspace.png)

     2. 如果您已刪除遷移專案, 請在 [Azure 入口網站] 的左窗格中選取 [**資源群組**]。 移至工作區建立所在的資源群組, 然後流覽至其中。
2. 遵循[這篇文章](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)中的指示來刪除工作區。

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>移轉專案建立失敗，發生「要求必須包含使用者識別標頭」錯誤。

使用者若無權存取組織的 Azure Active Directory (Azure AD) 租用戶，便會發生這種問題。 當使用者第一次被新增至 Azure AD 租用戶時，他/她會收到歡迎加入租用戶的電子郵件邀請函。 使用者必須打開電子郵件並接受邀請，才能成功加入租用戶。 如果您無法看到電子郵件，請與已有權存取該租用戶的使用者聯繫，並要求他們使用[這裡](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)指定的步驟，向您重新發送邀請。

收到電子郵件邀請函後，請打開電子郵件並按一下電子郵件中的連結來接受邀請。 完成後, 您必須登出 Azure 入口網站並再次登入, 重新整理瀏覽器將無法使用。 然後您可以試著建立移轉專案。

## <a name="appliance-issues"></a>設備問題

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>部署 VMware 的 Azure Migrate 設備失敗, 發生錯誤:提供的資訊清單檔案無效：OVF 資訊清單項目無效。

1. 藉由檢查其雜湊值, 確認是否已正確下載 Azure Migrate 設備 OVA 檔案。 請參閱[文章](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)以驗證雜湊值。 如果雜湊值不相符，請再次下載 OVA 檔案並重試部署。
2. 如果仍然失敗，且您使用 VMware vSphere 用戶端部署 OVF，請嘗試透過 vSphere Web 用戶端部署它。 如果仍然失敗，請嘗試使用不同的網頁瀏覽器。
3. 如果您是使用 vSphere Web 用戶端，並嘗試在 vCenter Server 6.5 或 6.7上部署它，請嘗試依照下列步驟直接在 ESXi 主機上部署 OVA：
   - 使用 web 用戶端 (HTTPs://<*主機 IP 位址*>/ui) 直接連接到 ESXi 主機 (而不是 vCenter Server)。
   - 前往 [**首頁** > **清查**]。
   - 按一下 [檔案] [**部署 OVF 範本**]。   >  流覽至 OVA 並完成部署。
4. 如果部署仍然失敗，請連絡 Azure Migrate 的支援。

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>設備無法連線到網際網路

當您使用的電腦位於 Proxy 後方時，可能會發生此問題。 如果 Proxy 需要授權認證，請務必提供授權認證。
如果您使用任何以 URL 為基礎的防火牆 proxy 來控制輸出連線能力, 請務必將這些必要的 Url 新增至允許清單:

狀況 | URL 清單
--- | ---
適用于 VMware 的伺服器評估 | [如下](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Hyper-v 的伺服器評估 | [如下](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
適用于 VMware 的伺服器遷移 (無代理程式) | [如下](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
適用于 VMware 的伺服器遷移 (以代理程式為基礎) | [如下](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Hyper-v 的伺服器遷移 | [如下](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

如果您使用攔截 proxy 來連線到網際網路, 則需要將 proxy 憑證匯入至應用裝置 VM。 您可以使用[這裡](https://docs.microsoft.com/azure/migrate/concepts-collector)詳述的步驟來匯入 Proxy 憑證。

### <a name="error-802-date-and-time-synchronization-error"></a>錯誤 802：日期和時間同步處理發生錯誤

伺服器時鐘可能與目前的時間不同步，相差到五分鐘以上。 請變更收集器虛擬機器的時間，使 之與目前的時間相符，方法如下所示：

1. 在虛擬機器上開啟系統管理命令提示字元。
2. 若要檢查時區，請執行 w32tm /tz。
3. 若要同步時間，請執行 w32tm /resync。


### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

發生下列錯誤，無法連線至 vCenter Server "Servername.com:9443"：沒有任何在 https://Servername.com:9443/sdk 上進行接聽的端點可以接受該訊息。

檢查您是否正在執行最新版的收集器設備，如果不是，請將設備升級至[最新的版本](https://docs.microsoft.com/azure/migrate/concepts-collector)。

如果最新的版本仍然會發生相同的問題，有可能是收集器機器無法解析指定的 vCenter Server 名稱，或指定的連接埠錯誤。 根據預設，如果未指定連接埠，則收集器會試著連線至連接埠編號 443。

1. 試著從收集器機器偵測 Servername.com。
2. 如果步驟 1 失敗，請嘗試透過 IP 位址連線到 vCenter Server。
3. 識別連線至 vCenter 的正確連接埠號碼。
4. 最後，請檢查 vCenter 伺服器是否啟動且正在執行。


### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>設備無法成功註冊至 Azure Migrate 專案 (錯誤識別碼:60052)

此錯誤是因為用來註冊應用裝置的 Azure 帳戶許可權不足。 請確定用來註冊應用裝置的 Azure 使用者帳戶至少具有訂用帳戶的「參與者」存取權。 [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)必要的 Azure 角色和許可權。

### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>設備無法成功註冊至 Azure Migrate 專案 (錯誤識別碼:60039)

找不到您註冊設備所選取的 Azure Migrate 專案, 導致註冊失敗。 移至 Azure 入口網站, 並檢查項目是否存在於您的資源群組中。 如果專案不存在, 請在您的資源群組中建立新的 Azure Migrate 專案, 然後重新註冊設備。 [深入瞭解](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool)如何建立新的 Azure Migrate 專案。

### <a name="azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Azure 金鑰保存庫管理操作失敗 (錯誤識別碼:60030、60031)

請確定用來註冊應用裝置的 Azure 使用者帳戶至少具有訂用帳戶的「參與者」存取權。 此外, 請檢查帳戶是否能夠存取錯誤訊息中指定的 Key Vault, 然後重試此作業。 如果問題持續發生， 請連絡 Microsoft 支援服務。 [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)必要的 Azure 角色和許可權。

### <a name="discovery-could-not-be-initiated-due-to-the-error-the-operation-failed-for-the-given-list-of-hosts-or-clusters-error-id-60028"></a>因為發生錯誤, 所以無法起始探索。 指定的主機或叢集清單的操作失敗 (錯誤識別碼:60028)

因為存取或抓取 VM 資訊時發生問題, 所以無法在錯誤中所列的主機上啟動探索;已成功新增您新增的其餘主機。 使用 [**新增主機**] 選項, 再次新增錯誤中的主機。 如果發生驗證錯誤, 請參閱補救指引以修正錯誤, 然後再次嘗試**儲存並開始探索**。

### <a name="azure-active-directory-aad-operation-failed-the-error-occurred-while-creating-or-updating-the-aad-application-error-id-60025"></a>Azure Active Directory (AAD) 操作失敗。 建立或更新 AAD 應用程式時發生錯誤 (錯誤識別碼:60025)

用來註冊設備的 Azure 使用者帳戶無法存取錯誤訊息中指定的 AAD 應用程式。 檢查您是否為 AAD 應用程式的擁有者。 [深入瞭解](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements)AAD 應用程式許可權。


## <a name="discovery-issues"></a>探索問題

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>我啟動了探索, 但在 Azure 入口網站上看不到探索到的 Vm。 伺服器評估和伺服器遷移磚顯示「正在探索」狀態
從設備開始探索之後, 請等候一段時間讓探索到的機器顯示在 Azure 入口網站上。 VMware 探索大約需要15分鐘的時間, 而且每個新增的主機可進行 Hyper-v 探索約2分鐘的時間。 如果您在這段時間之後仍繼續看到 [正在探索], 請按一下 [**伺服器**] 索引標籤上的 [重新整理]。這應該會顯示伺服器評估和伺服器遷移磚中探索到的伺服器計數。


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>我使用的設備會持續探索我的內部部署環境, 但在我的內部部署環境中刪除的 Vm 仍會顯示在入口網站中。

最多需要30分鐘的時間, 應用裝置所收集的探索資料才會反映在入口網站中。 如果您在30分鐘後仍看不到最新資訊, 請使用下列步驟對資料發出重新整理:

1. 在 伺服器 > Azure Migrate:伺服器評估, 按一下**總覽**。
2. 在 **管理** 底下, 按一下 **代理程式健全狀況**
3. 按一下 [重新整理**代理程式**] 選項。 您會在代理程式清單下方看到下列選項。
4. 等待重新整理作業完成。 確認您可以看到您 Vm 上的最新資訊。

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>我在入口網站上沒有內部部署 Vm 的最新資訊

最多需要30分鐘的時間, 應用裝置所收集的探索資料才會反映在入口網站中。 如果您在30分鐘後仍看不到最新資訊, 請使用下列步驟對資料發出重新整理:

1. 在 伺服器 > Azure Migrate:伺服器評估, 按一下**總覽**。
2. 在 **管理** 底下, 按一下 **代理程式健全狀況**
3. 按一下 [重新整理**代理程式**] 選項。 您會在代理程式清單下方看到此選項。
4. 等待重新整理作業完成。 您現在應該會看到 Vm 上的最新資訊。

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>無法連接到主機或叢集, 因為無法解析伺服器名稱。 WinRM 錯誤碼:0x803381B9 (錯誤識別碼:50004)
如果為設備提供服務的 DNS 無法解析您提供的叢集或主機名稱, 就會發生此錯誤。 如果您在叢集上看到此錯誤, 請嘗試提供叢集的完整功能變數名稱。

對於叢集中的主機, 您可能也會看到此錯誤。 在此情況下, 設備可能會連接到叢集。 但是叢集傳回的主機名稱不是完整的功能變數名稱。

若要解決此錯誤, 請更新設備上的 hosts 檔案, 並新增 IP 位址和主機名稱的對應。
1. 以系統管理員使用者身分開啟 [記事本]。 開啟檔案 C:\Windows\System32\Drivers\etc\hosts。
2. 在資料列中新增 IP 位址和主機名稱。 針對您看到此錯誤的每個主機或叢集重複此動作。
3. 儲存並關閉 hosts 檔案。
4. 您可以使用設備管理應用程式來檢查設備是否可連線至主機。 30分鐘後, 您應該就可以在 Azure 入口網站上查看這些主機的最新資訊。


## <a name="assessment-issues"></a>評定問題

### <a name="azure-readiness-issues"></a>Azure 就緒問題

問題 | 修復
--- | ---
不支援的開機類型 | Azure 不支援具有 EFI 開機類型的 VM。 建議您在執行遷移之前, 先將開機類型轉換成 BIOS。 <br/><br/>您可以使用 Azure Migrate Server 遷移來進行這類 Vm 的遷移, 因為它會在遷移期間將 VM 的開機類型轉換成 BIOS。
有條件地支援 Windows 作業系統 | 作業系統已超過結束支援日期，且需要 [Azure 中的支援](https://aka.ms/WSosstatement)的自訂支援合約 (CSA)，請考慮在移轉至 Azure 之前升級作業系統。
不支援的 Windows 作業系統 | Azure 僅支援[選取的 Windows 作業系統版本](https://aka.ms/WSosstatement)，請考慮在移轉至 Azure 之前升級電腦作業系統。
有條件地背書 Linux 作業系統 | Azure 僅支援[選取的 Linux 作業系統版本](../virtual-machines/linux/endorsed-distros.md)，請考慮在移轉至 Azure 之前升級電腦作業系統。
未背書的 Linux 作業系統 | 電腦可能會在 Azure 中開機，但是 Azure 未提供作業系統支援，請考慮在移轉至 Azure 之前，將作業系統升級至[背書的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)
作業系統不明 | 虛擬機器的作業系統在 vCenter Server 中指定為「其他」，因此，Azure Migrate 無法識別虛擬機器的 Azure 移轉整備程度。 請在移轉電腦之前，先確認 Azure [支援](https://aka.ms/azureoslist)該電腦內執行的作業系統。
不支援的 OS 位元 | 32 位元作業系統的虛擬機器可能會在 Azure 中開機，但建議在移轉至 Azure 之前，將虛擬機器的作業系統從 32 位元升級到 64 位元。
需要 Visual Studio 訂用帳戶。 | 電腦具有在其中執行的 Windows 用戶端作業系統, 只有 Visual Studio 訂用帳戶才支援。
找不到所需儲存體效能的虛擬機器。 | 機器需要的儲存體效能 (IOPS/輸送量) 超出 Azure VM 支援。 在移轉之前，降低機器的儲存體需求。
找不到所需網路效能的虛擬機器。 | 機器需要的網路效能 (傳入/傳出) 超出 Azure VM 支援。 減少機器的網路需求。
找不到指定位置的虛擬機器。 | 在移轉之前，使用不同的目標位置。
一個或多個不適合的磁碟。 | 一或多個連結至 VM 的磁碟不符合 Azure 需求。 針對每個連結至 VM 的磁碟，請確保每個磁碟的大小小於 4 TB，如果不是，請先縮減磁碟大小，再移轉至 Azure。 請確保 Azure [受控虛擬機器磁碟](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)支援每個磁碟所需的效能 (IOPS/輸送量)。   
一個或多個不適用的網路介面卡。 | 在移轉之前，從機器移除未使用的網路介面卡。
磁碟計數超過限制 | 移轉之前從機器移除未使用的磁碟。
磁碟大小超過限制 | Azure 支援大小最大 4 TB 的磁碟。 在移轉前將磁碟壓縮為小於 4 TB。
指定的位置沒有磁碟可用 | 在移轉之前，請確定磁碟已在目標位置。
沒有磁碟可當作指定的備援 | 磁碟應該使用評估設定 (預設為 LRS) 中定義的備援儲存體類型。
由於發生內部錯誤，因此無法判斷磁碟適合性 | 嘗試建立群組的新評估。
找不到具備所需核心和記憶體的虛擬機器 | Azure 找不到適當的虛擬機器類型。 在移轉之前，請減少內部部署電腦的記憶體和核心數目。
由於發生內部錯誤，無法判斷虛擬機器的適合性。 | 嘗試建立群組的新評估。
由於發生內部錯誤，因此無法判斷一個或多個磁碟的適用性。 | 嘗試建立群組的新評估。
由於發生內部錯誤，因此無法判斷一個或多個網路介面卡的適用性。 | 嘗試建立群組的新評估。

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>我無法在評量屬性中將 Enterprise 合約 (EA) 指定為 Azure 供應專案嗎？
Azure Migrate：伺服器評量目前不支援以 Enterprise 合約 (EA) 為基礎的定價。 因應措施是使用「隨用隨付」作為 Azure 供應項目，並使用 'Discount' 屬性來指定您獲得的任何自訂折扣。 [深入了解如何自訂評量](https://aka.ms/migrate/selfhelp/eapricing)。

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>為什麼伺服器評量會將我的 Linux Vm 標示為有條件地準備就緒。 我需要做什麼來修正這個問題嗎？
伺服器評量中存在已知缺陷，即無法偵測到安裝在內部部署 VM 上的次要 Linux OS 版本 (以 RHEL 6.10 為例，伺服器評量目前只能偵測到 RHEL 6 這個 OS 版本)。 由於 Azure 僅支援特定版本的 Linux，因此 Linux VM 目前在伺服器評量中會標示為「有條件地就緒」。 您可以藉由查看[Azure Linux 支援檔](https://aka.ms/migrate/selfhost/azureendorseddistros), 以手動方式確保在內部部署 VM 上執行的 Linux OS 已背書于 azure 中。 已確認是支援的散發版本後，即可忽略此警告。

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>伺服器評估所建議的 VM SKU 擁有更多的核心數目, 而且記憶體大小比內部部署所配置的還要大。 為什麼這麼做呢？
伺服器評量中的 VM SKU 建議取決於評量屬性。 您可以在伺服器評量中建立兩種評量：「以效能為基礎的」評量和「內部部署形式」評量。 針對以效能為基礎的評量, 伺服器評估會考慮內部部署 Vm (CPU、記憶體、磁片和網路使用率) 的使用量資料, 以判斷內部部署 Vm 的正確目標 VM SKU。 此外，對於以效能為基礎的大小調整，會將緩和因數納入考量以識別有效的使用量。 對於內部部署形式的大小調整，則不會考量效能資料，而是根據內部部署配置提供建議的目標 SKU。

例如, 假設有一個內部部署 VM 具有4個核心和 8 GB 的記憶體, 具有 50% 的 CPU 使用率和 50% 的記憶體使用率, 而評量中指定了緩和因數1.3。 如果評估的調整大小準則是「作為內部部署」, 建議使用具有4個核心和 8 GB 記憶體的 Azure VM SKU, 不過, 如果調整大小準則是以效能為基礎, 則根據有效的 CPU 和記憶體使用率 (4 核心的 50% * 1.3 = 2.6 核心和 8 GB 的 50%)記憶體 * 1.3 = 5.3 GB 記憶體), 建議使用四個核心的最低 VM SKU (最接近支援的核心計數) 和 8 GB 的記憶體大小 (最接近支援的記憶體大小)。 [深入了解伺服器評量執行大小調整的方式](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)。

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>伺服器評估所建議的磁片 SKU, 其大小比內部部署所配置的還大。 為什麼這麼做呢？
伺服器評量中的磁碟大小調整取決於兩個評量屬性：大小調整準則和儲存體類型。 如果調整大小準則為「以效能為基礎」, 且儲存體類型設定為「自動」, 則會將磁片的 IOPS 和輸送量值視為識別目標磁片類型 (標準 HDD、標準 SSD 或 Premium 磁片)。 接著會考量內部部署磁碟的大小需求，而建議磁碟類型內的磁碟 SKU。 如果調整大小準則為「以效能為基礎」, 而儲存體類型為「高階」, 則根據內部部署磁片的 IOPS、輸送量和大小需求, 建議 Azure 中的 Premium 磁片 SKU。 當大小調整準則是「內部部署形式」的大小調整，儲存體類型為「標準 HDD」、「標準 SSD」或「進階」時，也會使用相同的邏輯來執行磁碟的大小調整。

例如, 如果您有一個具有 32 GB 記憶體的內部部署磁片, 但磁片的匯總讀取和寫入 IOPS 是 800 IOPS, 則伺服器評估會建議使用 premium 磁片類型 (因為 IOPS 需求較高), 然後建議磁片 SKU, 這可支援所需的 IOPS 和大小。 在此範例中最接近的相符項目為 P15 (256 GB、1100 IOPS)。 因此，即使內部部署磁碟所需的大小為 32 GB，伺服器評量仍會建議使用較大的磁碟，因為內部部署磁碟的 IOPS 需求較高。

### <a name="why-does-my-assessment-report-say-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>為什麼我的評量報告會針對某些 Vm 說出「PercentageOfCoresUtilizedMissing」或「PercentageOfMemoryUtilizedMissing」？
當 Azure Migrate 設備無法收集內部部署 Vm 的效能資料時, 會列出上述問題。 如果 Vm 在您建立評量的持續期間 (最後一天/一周/一個月) 電源已關閉, 則可能會發生這種情況, 因為設備在關閉電源時, 無法收集 VM 的效能資料。 如果只有記憶體計數器遺失, 而您想要評估 Hyper-v Vm, 請檢查您是否已在這些 Vm 上啟用動態記憶體。 目前有一個已知問題, 就是 Azure Migrate 設備無法收集未啟用動態記憶體之 Vm 的記憶體使用量。 請注意, 此問題僅適用于 Hyper-v Vm, 而不適用於 VMware Vm。 如果遺漏任何效能計數器, 請 Azure Migrate:伺服器評估會回到已配置的核心/記憶體, 並據此建議 VM 大小。

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>VM 的 OS 授權成本是否包含在伺服器評估所估計的計算成本中？
伺服器評量目前只會考量 Windows 機器的 OS 授權成本，而不考量 Linux 機器的 OS 授權成本。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>效能歷程記錄和百分位數使用率對大小建議有何影響？
這些屬性僅適用於「以效能為基礎」的大小調整。 伺服器評量會持續收集內部部署機器的效能資料，並用這些資料來建議 Azure 中的 VM SKU 和磁碟 SKU。 以下說明伺服器評量收集效能資料的方式：
- Azure Migrate 設備會持續分析內部部署環境, 以收集 VMware Vm 每20秒的即時使用量資料, 以及每30秒的 Hyper-v Vm。
- 設備會彙總 20/30 秒的樣本，並且每 10 分鐘建立一個單一資料點。 為了建立單一資料點，設備會從所有 20/30 秒的樣本中選取尖峰值，並將其傳送至 Azure。
- 當您在伺服器評量中建立評量時，系統會根據效能持續時間和效能記錄的百分位數值，找出具代表性的使用量值。 例如, 如果效能歷程記錄是一周, 而百分位數的使用率是第95個, Azure Migrate 將會以遞增順序排序過去一周的所有10分鐘取樣點, 然後選取第95個百分位數做為代表值。
第 95 個百分位數值可確保您會忽略任何極端值；若您選擇第 99 個百分位數就有可能納入這些值。 如果您想選擇該期間的尖峰使用量，但不想遺漏任何極端值，則應選取第 99 個百分位數作為百分位數使用量。

## <a name="dependency-visualization-issues"></a>相依性視覺效果問題

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>我找不到 Azure Government 專案的相依性視覺效果功能。

Azure Migrate 相依於相依性視覺效果功能的服務對應，由於服務對應目前在 Azure Government 中無法使用，因此這項功能不適用於 Azure Government。

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>我在內部部署 VM 上安裝了 Microsoft Monitoring Agent (MMA) 和相依性代理程式，但相依性未出現在 Azure Migrate 入口網站中。

在您安裝代理程式之後，Azure Migrate 通常需要 15-30 分鐘的時間，才會在入口網站中顯示相依性。 如果您等候的時間超過 30 分鐘，請依照下列步驟進行操作，以確定 MMA 代理程式能夠與 OMS 工作區溝通：

針對 Windows VM：
1. 移至 [**控制台**], 然後啟動**Microsoft Monitoring Agent**。
2. 在 [MMA 屬性] 快顯視窗中, 移至 [ **Azure Log Analytics (OMS)** ] 索引標籤。
3. 確定工作區的 [狀態] 為綠色。
4. 如果狀態不是綠色，請嘗試移除該工作區，然後再次新增至 MMA。
        ![MMA 狀態](./media/troubleshooting-general/mma-status.png)

針對 Linux VM，請確定 MMA 和相依性代理程式的安裝命令已成功。

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA 支援哪些作業系統？

MMA 所支援的 Windows 作業系統清單在[這裡](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)。
MMA 所支援的 Linux 作業系統清單在[這裡](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>相依性代理程式支援哪些作業系統？

相依性代理程式所支援的 Windows 作業系統清單在[這裡](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)。
相依性代理程式所支援的 Linux 作業系統清單在[這裡](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)。

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>在 Azure Migrate 中將相依性視覺化的持續時間是否無法超過一小時？
Azure Migrate 可讓您將相依性視覺化的持續時間最多為一小時。 雖然 Azure Migrate 最多可讓您返回歷程記錄中過去一個月的特定日期，但您可將相依性視覺化的持續時間最多為 1 小時。 例如，您可以使用相依性對應中的持續時間功能來檢視昨天的相依性，但只能檢視它一小時。 不過, 您可以使用 Azure 監視器記錄, 在較長的持續期間內查詢相依性[資料](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>如果群組所含的 VM 超過 10 個，是否無法將其相依性視覺化？
針對所含 VM 在 10 個以內的群組，您可以[將群組的相依性視覺化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)，如果您的群組所含 VM 超過 10 個，則建議您先將群組分割成較小的群組，再將相依性視覺化。

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>我已經安裝代理程式，並使用相依性視覺化建立群組。 現在，在容錯移轉後，機器會顯示「安裝代理程式」動作，而不是「檢視相依性」。
* 在已規劃或未規劃的容錯移轉後，內部部署機器都會關閉，而且對等的機器會在 Azure 中啟動。 這些機器會取得不同的 MAC 位址。 根據使用者是否選擇保留內部部署 IP 位址，這些機器可能會取得不同的 IP 位址。 如果 MAC 及 IP 位址不同，Azure Migrate 不會使內部部署機器與任何服務對應相依性資料產生關聯，而會要求使用者安裝代理程式，而不是檢視相依性。
* 在測試容錯移轉後，內部部署機器如預期保持開啟。 在 Azure 中啟動的對等機器會取得不同的 MAC 位址，而且可能會取得不同的 IP 位址。 除非使用者封鎖傳出 Azure 監視器記錄來自這些電腦的流量, 否則 Azure Migrate 不會將內部部署機器與任何服務對應相依性資料產生關聯, 而會要求使用者安裝代理程式, 而不是查看相依性。

## <a name="collect-azure-portal-logs"></a>收集 Azure 入口網站記錄

**如何? 收集 Azure 入口網站的網路流量記錄檔嗎？**

1. 開啟瀏覽器, 並流覽並登入[入口網站](https://portal.azure.com)。
2. 按 F12 開啟 Developer Tools。 如有需要，請清除 [清除瀏覽的項目] 設定。
3. 按一下 [網路] 索引標籤，並開始擷取網路流量：
   - 在 Chrome 中，選取 [保留記錄]。 記錄應該會自動啟動。 紅色圓圈表示正在擷取流量。 如果未出現, 請按一下黑色圓圈以開始。
   - 在 Microsoft Edge/IE 中，記錄應該會自動啟動。 如果未啟動，請按一下綠色播放按鈕。
4. 嘗試重現錯誤。
5. 您在記錄時發生錯誤之後，請停止錄製，並儲存一份記錄的活動：
   - 在 Chrome 中，以滑鼠右鍵按一下 [內容另存為 HAR]。 這會壓縮記錄檔, 並將其匯出為 har 檔案。
   - 在 Microsoft Edge/IE 中，按一下 [匯出擷取流量] 圖示。 這會壓縮並匯出記錄檔。
6. 瀏覽至 [主控台] 索引標籤，檢查是否有任何警告或錯誤。 若要儲存主控台記錄：
   - 在 Chrome 中，以滑鼠右鍵按一下主控台記錄的任何位置。 選取 [**另存**新檔], 匯出並壓縮記錄檔。
   - 在 Microsoft Edge/IE 中，以滑鼠右鍵按一下錯誤，然後選取 [全部複製]。
7. 關閉 Developer Tools。
