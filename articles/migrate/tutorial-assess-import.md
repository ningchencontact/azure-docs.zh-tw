---
title: 透過 Azure Migrate 伺服器評量使用匯入的伺服器資料來評估伺服器
description: 說明如何透過 Azure Migrate 伺服器評量使用匯入的資料來評估內部部署伺服器是否可移轉至 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 86d61dbd75363f68ee4651bfb36f940810909c00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509942"
---
# <a name="assess-servers-using-imported-data"></a>使用匯入的資料評估伺服器

本文說明如何使用 [Azure Migrate：伺服器評量](migrate-services-overview.md#azure-migrate-server-assessment-tool)，藉由使用 CSV 匯入伺服器中繼資料來評估內部部署伺服器。 使用此評量方法時，您無須設定 Azure Migrate 設備來建立評量。 此功能在下列情況下相當有用： 

- 您想要在部署設備之前建立快速初始評量。
- 您無法在組織中部署 Azure Migrate 設備。
- 您無法共用允許存取內部部署伺服器的認證。
- 有安全性條件約束使您無法收集設備所收集到的資料，並將其傳送至 Azure。 透過匯入的檔案，您可以控制您所共用的資料，而許多資料 (例如提供 IP 位址) 是選擇性的。


## <a name="before-you-start"></a>開始之前

請注意：

- 您最多可以在單一 CSV 檔案中新增 20000 個伺服器。
- 您可以使用 CSV 在 Azure Migrate 專案中新增最多 20000 個伺服器。
- 您可以多次使用 CSV 將伺服器資訊上傳至 Azure Migrate 伺服器評量。
- 雖然收集應用程式資訊在評估內部部署環境是否可進行移轉時很有用處，但 Azure Migrate 伺服器評量目前並不會執行應用程式層級的評量，且在建立評量時不會將應用程式納入考量。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 設定 Azure Migrate 專案。
> * 在 CSV 檔案中填入伺服器資訊。
> * 匯入檔案，將伺服器資訊新增至 Azure Migrate 伺服器評量。
> * 建立並檢閱評量。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程會在情況允許時都使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱操作說明文章。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="set-azure-permissions-for-azure-migrate"></a>設定 Azure Migrate 的 Azure 權限 

您的 Azure 帳戶必須有建立 Azure Migrate 專案的權限。

1. 在 Azure 入口網站中開啟訂用帳戶，然後選取 [存取控制 (IAM)]  。
2. 在 [檢查存取權]  中，尋找相關的帳戶，然後按一下以查看權限。
3. 您應該會具有「參與者」  或「擁有者」  權限。
    - 如果您剛建立免費的 Azure 帳戶，您就是訂用帳戶的擁有者。
    - 如果您不是訂用帳戶擁有者，請與擁有者合作以指派角色。


## <a name="set-up-an-azure-migrate-project"></a>設定 Azure Migrate 專案

設定新的 Azure Migrate 專案，如下所示。

1. 在 Azure 入口網站 > [所有服務]  中搜尋 **Azure Migrate**。
2. 在 [服務]  下，選取 [Azure Migrate]  。
3. 在 [概觀]  的 [探索、評估和遷移伺服器]  底下，按一下 [評估和遷移伺服器]  。

    ![探索和評估伺服器](./media/tutorial-assess-import/assess-migrate.png)

4. 在 [開始使用]  中，按一下 [新增工具]  。
5. 在 [遷移專案]  中，選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有資源群組的話)。     
6. 在 [專案詳細資料]  中，指定專案名稱以及要在其中建立專案的地理位置。

    - [檢閱](migrate-support-matrix.md#supported-geographies)支援的地理位置。 專案地理區域只會用來儲存從內部部署 VM 收集到的中繼資料。
    - 當您執行移轉時，可以選取任何目的地區域。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-import/migrate-project.png)


7. 按 [下一步]  。
8. 在 [選取評量工具]  中，選取 **[Azure Migrate：伺服器評量]**  > [下一步]  。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-import/assessment-tool.png)

9. 在 [選取移轉工具]  中，選取 [暫時跳過新增移轉工具]   > [下一步]  。
10. 在 [檢閱 + 新增工具]  中檢閱設定，然後按一下 [新增工具]  。
11. 等候幾分鐘讓 Azure Migrate 專案完成部署。 您會進入專案頁面。 如果您沒有看到專案，則可以從 Azure Migrate 儀表板中的 [伺服器]  來存取。


## <a name="prepare-the-csv"></a>準備 CSV

下載 CSV 範本，並在其中新增伺服器資訊。


### <a name="download-the-template"></a>下載範本

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在 [探索機器]  中，選取 [使用 .CSV 匯入]  。
3. 按一下 [下載]  以下載 .CSV 範本。 或者，您也可以[直接下載](https://go.microsoft.com/fwlink/?linkid=2108404)。

    ![下載 .CSV 範本](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>新增伺服器資訊

收集伺服器資料，並將其新增至 CSV 檔案。

- 若要收集資料，您可以從用於內部部署伺服器管理的工具 (例如 VMware vSphere 或您的設定管理資料庫 (CMDB)) 將資料匯出。
- 若要檢閱範例資料，請下載我們的[範例檔案](https://go.microsoft.com/fwlink/?linkid=2108405)。


下表摘要說明要填入的檔案欄位。

**欄位名稱** | **必要** | **詳細資料**
--- | --- | ---
**伺服器名稱** | yes | 建議您指定 FQDN。 
**IP 位址** | 否 | 伺服器位址。
**核心數目** | yes | 配置給伺服器的處理器核心數目。
**記憶體** | yes | 配置給伺服器的 RAM 總計 (MB)。
**作業系統名稱** | yes | 伺服器作業系統。
**作業系統版本** | 否 | 伺服器作業系統版本。
**磁碟數量** | 否 | 如果提供了個別磁碟詳細資料，則不需要。
**磁碟 1 大小**  | 否 | 磁碟的大小上限 (GB)<br/> 您可以藉由在範本中[新增資料行](#add-multiple-disks)，來新增更多磁碟的詳細資料。 您最多可以新增八個磁碟。
**磁碟 1 讀取作業數** | 否 | 每秒的磁碟讀取作業數。
**磁碟 1 寫入作業數** | 否 | 每秒的磁碟寫入作業數。
**磁碟 1 讀取輸送量** | 否 | 每秒從磁碟讀取的資料量 (以每秒 MB 數為單位)。
**磁碟 1 寫入輸送量** | 否 | 每秒寫入至磁碟的資料量 (以每秒 MB 數為單位)。
**CPU 使用率百分比** | 否 | CPU 的使用率百分比。
**記憶體使用率百分比** | 否 | RAM 的使用率百分比。
**磁碟讀取作業數總計** | 否 | 每秒的磁碟讀取作業數。
**磁碟寫入作業數總計** | 否 | 每秒的磁碟寫入作業數。
**磁碟讀取輸送量總計** | 否 | 從磁碟讀取的資料量 (以每秒 MB 數為單位)。
**磁碟寫入輸送量總計** | 否 | 寫入至磁碟的資料量 (以每秒 MB 數為單位)。
**網路輸入輸送量** | 否 | 伺服器接收到的資料量 (以每秒 MB 數為單位)。
**網路輸出輸送量** | 否 | 伺服器傳輸的資料量 (以每秒 MB 數為單位)。
**韌體類型** | 否 | 伺服器韌體。 值可以是 "BIOS" 或 "UEFI"
**伺服器類型** | 否 | 值可以是「實體」或「虛擬」。
**Hypervisor** | 否 | 機器執行所在的 Hypervisor。 <br/> 值可以是 "VMware"、"Hyper-V"、"Xen"、"AWS"、"GCP" 或「其他」。
**Hypervisor 版本號碼** | 否 | Hypervisor 版本。
**虛擬機器識別碼** | 否 | VM 識別碼。 這是 VMware vCenter VM 的 **InstanceUUid**，或 Hyper-V 的 **Hyper-V VM 識別碼**。
**Virtual Machine Manager 識別碼** | 否 | 這是 VMware vCenter 的 **InstanceUUid**。 Hyper-V 不需要此項目。
**MAC 位址**| 否 | 伺服器 MAC 位址。
**BIOS 識別碼** | 否 | 伺服器 BIOS 識別碼。
**自訂伺服器識別碼**| 否 | 本機唯一的內部部署伺服器識別碼。 <br/> 可用來依本機識別碼追蹤匯入的伺服器。 
**應用程式 1 名稱** | 否 | 在伺服器上執行的工作負載名稱。<br/> 您可以藉由在範本中[新增資料行](#add-multiple-applications)，來新增更多應用程式的詳細資料。 您最多可以新增五個應用程式。
**應用程式 1 類型** | 否 | 在伺服器中執行的工作負載類型
**應用程式 1 版本** | 否 | 在伺服器上執行的工作負載版本。
**應用程式 1 授權到期日** | 否 | 工作負載的授權到期日 (如果適用)。
**業務單位** | 否 | 伺服器所屬的業務單位。
**公司擁有者** | 否 | 業務單位擁有者。
**商務應用程式名稱** | 否 | 應用程式所屬的應用程式名稱。
**位置** | 否 | 伺服器所在的資料中心。
**伺服器解除委任日期** | 否 | 實體伺服器或虛擬伺服器的基礎實體伺服器的解除委任日期

### <a name="add-operating-systems"></a>新增作業系統

評量可識別特定作業系統名稱。 您指定的任何作業系統名稱都必須符合[支援的名稱](#supported-operating-system-names)清單中的選項之一。


### <a name="add-multiple-disks"></a>新增多個磁碟

範本會提供第一個磁碟的預設欄位。  您最多可為 8 個磁碟新增類似的資料行。 

例如，若要指定第二個磁碟的所有欄位，請新增資料行：

Disk 2 size Disk 2 read ops Disk 2 write ops Disk 2 read throughput Disk 2 write throughput

您可以選擇只為一個磁碟新增特定欄位。


### <a name="add-multiple-applications"></a>新增多個應用程式

範本會提供單一應用程式的欄位。 您最多可為五個應用程式新增類似的資料行。  

例如，若要指定第二個應用程式的所有欄位，請新增資料行：

Application 2 name Application 2 type Application 2 version Application 2 license expiry


您可以選擇只為一個應用程式新增特定欄位。

> [!NOTE]
> 當您評估內部部署環境是否可進行移轉時，應用程式資訊會很有用處。 不過，Azure Migrate 伺服器評量目前不會執行應用層級評量，且在建立評量時不會將應用程式納入考量。


## <a name="upload-the-server-information"></a>上傳伺服器資訊

將資訊新增至 CSV 範本之後，請將伺服器匯入至 Azure Migrate：。

1. 在 Azure Migrate > [探索機器]  中，瀏覽至已填入的範本。
2. 按一下 [匯入]  。
3. 匯入狀態隨即顯示。 
    - 如果狀態中出現警告，您可以修正相關狀況，或繼續作業而不加以處理。
    - 依照警告中的建議改善伺服器資訊，可提高評量的正確性。
    - 若要檢視並修正出現的警告，請按一下 [下載警告詳細資料 .CSV]  。 這會下載 CSV，並新增警告。 您可以檢閱警告，並視需要修正問題。 
    如果狀態中出現錯誤 (匯入狀態為「失敗」  )，您必須先修正這些錯誤，才能繼續匯入。 若要這麼做，請下載此時已新增錯誤詳細資料的 CSV。 如有需要，請檢閱並解決錯誤。 然後，再次上傳修改過的檔案。
4. 當匯入狀態「完成」  時，就會匯入伺服器資訊。


> [!NOTE]
> 若要更新上傳至 Azure Migrate 的伺服器資訊，請使用相同的**伺服器名稱**再次上傳伺服器的資料。 請注意，匯入範本後，即無法修改 [伺服器名稱]  欄位。 目前不支援刪除伺服器。

## <a name="updating-server-information"></a>更新伺服器資訊

您可以使用相同的**伺服器名稱**再次上傳伺服器的資料，以更新伺服器資訊。 您無法修改 [伺服器名稱]  欄位。 

目前不支援刪除伺服器。

### <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成後，便可以驗證伺服器是否出現在 Azure 入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器   >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器  計數。
3. 按一下 [以匯入為基礎]  索引標籤。

## <a name="set-up-an-assessment"></a>設定評估

您可以使用 Azure Migrate：伺服器評量來建立的評量有兩種。

**評量** | **詳細資料** | **資料**
--- | --- | ---
**以效能為基礎** | 以指定的效能資料值為基礎的評量 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁碟的 IOPS 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以指定的伺服器大小為基礎<br/><br> **建議的磁碟類型**：以您為評估選取的儲存體類型設定為基礎。


### <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器]  索引標籤的 **[Azure Migrate：伺服器評量]** 磚中，按一下 [評估]  。

    ![評估](./media/tutorial-assess-physical/assess.png)

2. 在 [評估伺服器]  中，指定評估的名稱。
3. 在 [探索來源]  中，選取 [透過匯入至 Azure Migrate 新增的機器] 
3. 按一下 [檢視全部]  來檢閱評估屬性。

    ![評量屬性](./media/tutorial-assess-physical/view-all.png)

3. 在 [選取或建立群組]  中，選取 [新建]  ，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
4. 在 [將電腦新增至群組]  中，選取要新增至群組的伺服器。
5. 按一下 [建立評估]  以建立群組，然後執行評估。

    ![建立評估](./media/tutorial-assess-physical/assessment-create.png)

6. 評量建立好之後，可在 [伺服器]   >  **[Azure Migrate：伺服器評量]**  > [評量]  中加以檢視。
7. 按一下 [匯出評估]  ，將其下載為 Excel 檔案。



## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：伺服器是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行伺服器的預估每月計算與儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。

### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標]   >  [伺服器]  中，按一下 [Azure Migrate：伺服器評量]  中的 [評量]  。
2. 在 [評量]  中，按一下評量來加以開啟。

    ![評量摘要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度]  中，確認伺服器是否已準備好移轉至 Azure。
2. 檢閱狀態：
    - **可供 Azure 使用**：Azure Migrate 會針對評估中的 VM 建議 VM大小和成本估計值。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時，便會使用此狀態。

2. 按一下 [Azure 移轉整備程度]  狀態。 您可以檢視伺服器的整備程度詳細資料，並向下切入以查看伺服器詳細資料，包括計算、儲存體與網路設定。

### <a name="review-cost-details"></a>檢閱成本詳細資料

此檢視會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。

1. 檢閱每月的計算和儲存體成本。 系統會針對評估群組中的所有伺服器彙總成本。

    - 系統會根據機器的大小建議和其磁碟與屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 成本估計方式是以 IaaS VM 形式執行內部部署伺服器。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。
3. 您可以向下切入以查看特定 VM 的詳細資料。

> [!NOTE]
> 信賴評等不會指派給使用 CSV 匯入至 Azure Migrate 伺服器評量的伺服器評量。


## <a name="supported-operating-system-names"></a>支援的作業系統名稱

名稱 | 名稱
--- | ---
**A - H** | 
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux 
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD 
**I - R** | 
IBM OS/2 | MS-DOS |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11 
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | 
**S-T** | 
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** | 
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional
    

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 已使用 CSV 將伺服器匯入至 Azure Migrate：伺服器評量。
> * 已建立和檢閱評估

現在，請[部署設備](./migrate-appliance.md)以進行更精確的評量，並將伺服器一起收集到群組中，以使用[相依性分析](./concepts-dependency-visualization.md)進行更深入的評量。
