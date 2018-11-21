---
title: Azure 檔案同步代理程式的版本資訊 | Microsoft Docs
description: Azure 檔案同步代理程式的版本資訊。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 11/13/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 85746bb964b401a33355dda57546e0809de30f59
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624160"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 檔案同步代理程式的版本資訊
Azure 檔案同步可讓您將組織的檔案共用集中在「Azure 檔案服務」中，而不需要犧牲內部部署檔案伺服器的靈活度、效能及相容性。 您的 Windows Server 安裝會轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料 (包括 SMB、NFS 和 FTPS)。 您可以視需要存取多個散佈於世界各地的快取。

本文提供 Azure 檔案同步代理程式支援版本的版本資訊。

## <a name="supported-versions"></a>支援的版本
Azure 檔案同步代理程式支援下列版本：

| 里程碑 | 代理程式版本號碼 | 發行日期 | 狀態 |
|----|----------------------|--------------|------------------|
| V4 版本 | 4.0.1.0 | 2018 年 11 月 13 日 | 支援 (建議的版本) |
| 九月更新彙總套件 | 3.3.0.0 | 2018 年 9 月 24 日 | 支援 |
| 八月更新彙總套件 | 3.2.0.0 | 2018 年 8 月 15 日 | 支援 |
| 正式運作 | 3.1.0.0 | 2018 年 7 月 19 日 | 支援 |
| 六月更新彙總套件 | 3.0.13.0 | 2018 年 6 月 29 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 重新整理 2 | 3.0.12.0 | 2018 年 5 月 22 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 四月更新彙總套件 | 2.3.0.0 | 2018 年 5 月 8 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 三月更新彙總套件 | 2.2.0.0 | 2018 年 3 月 12 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 二月更新彙總套件 | 2.1.0.0 | 2018 年 2 月 28 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 重新整理 1 | 2.0.11.0 | 2018 年 2 月 8 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 一月更新彙總套件 | 1.4.0.0 | 2018 年 1 月 8 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 十一月更新彙總套件 | 1.3.0.0 | 2017 年 11 月 30 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 十二月更新彙總套件 | 1.2.0.0 | 2017 年 10 月 31 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |
| 初始預覽版本 | 1.1.0.0 | 2017 年 11 月 26 日 | 不支援 - 代理程式版本已於 2018 年 10 月1 日過期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-4010"></a>代理程式版本 4.0.1.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 4.0.1.0 (在 2018 年 11 月 13 日發行)。

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 AzureRM PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或不支援的作業系統版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Windows Server Core 或 Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2016 和 Windows Server 2012 R2 支援此代理程式。
- 代理程式需要至少 2 GiB 的記憶體。 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 如果檔案因為檔案檢測而遭到封鎖，檔案伺服器資源管理員 (FSRM) 檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 在部署伺服器映像並完成 sysprep 迷你設定之後，請安裝 Azure 檔案同步代理程式。
- 在相同的磁碟區上不支援重複資料刪除和雲端階層處理。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 具有不受支援字元的檔案。 如需不受支援字元的清單，請參閱[疑難排解指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句點結尾的檔案或目錄。
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法存取的狀態。 若要還原檔案的存取性，請重新建立伺服器端點。 如果在刪除伺服器端點後過了 30 天，或如果雲端端點已刪除，將會無法使用未重新呼叫的階層式檔案。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點位置內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。 若要更新入口網站中的伺服器名稱，請取消註冊並重新註冊伺服器。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 以日期為基礎的雲端階層處理原則設定可用來指定應該快取在指定天數內存取的檔案。 若要深入了解，請參閱[雲端階層處理概觀](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering)。
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 使用 robocopy 複製檔案時，請使用 /MIR 選項來保留檔案的時間戳記。 這可確保較舊的檔案會比最近存取的檔案更快進行階層處理。
- 從 SMB 用戶端檢視檔案屬性時，因為 SMB 快取檔案中繼資料，所以離線屬性可能會顯示為不正確設定。

## <a name="agent-version-3300"></a>代理程式版本 3.3.0.0
下列版本資訊適用於 2018 年 9 月 24 日發行的 Azure 檔案同步代理程式 3.3.0.0 版。 這些是針對版本 3.1.0.0 所列的版本資訊以外的版本資訊。

此版本包含下列修正︰
- Azure 檔案同步代理程式升級至 3.1 或 3.2 版後，註冊的伺服器狀態為「顯示離線」。
- 儲存體同步代理程式 (FileSyncSvc) 服務因檔案路徑太長而損毀。
- 伺服器註冊失敗，錯誤：無法載入檔案或組件 Kailani.Afs.StorageSyncProtocol.V3。

## <a name="agent-version-3200"></a>代理程式版本 3.2.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 3.2.0.0 (在 2018 年 8 月 15 日發行)。 這些是針對版本 3.1.0.0 所列的版本資訊以外的版本資訊。

此版本包含下列修正︰
- 同步處理失敗，由於記憶體流失造成記憶體不足錯誤 (0x8007000e)

## <a name="agent-version-3100"></a>代理程式版本 3.1.0.0
下列版本資訊適用於 Azure 檔案同步代理程式版本 3.1.0.0 (在 2018 年 6 月 17 日發行)。

### <a name="evaluation-tool"></a>評估工具
在部署 Azure 檔案同步之前，您應該使用 Azure 檔案同步評估工具來評估其是否與您的系統相容。 此工具是 AzureRM PowerShell Cmdlet，可檢查檔案系統和資料集的潛在問題，例如不支援的字元或不支援的作業系統版本。 關於安裝和使用方式指示，請參閱規劃指南中[評估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool)小節。 

### <a name="agent-installation-and-server-configuration"></a>代理程式安裝和伺服器設定
如需如何使用 Windows Server 安裝及設定 Azure 檔案同步代理程式的詳細資訊，請參閱[規劃 Azure 檔案同步部署](storage-sync-files-planning.md)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

- 必須以提升的 (系統管理員) 權限安裝代理程式安裝套件。
- Windows Server Core 或 Nano Server 部署選項不支援此代理程式。
- 只有 Windows Server 2016 和 Windows Server 2012 R2 支援此代理程式。
- 代理程式需要至少 2 GB 的實體記憶體。
- 在已將系統磁碟區資訊 (SVI) 目錄壓縮的磁碟區上，儲存體同步代理程式 (FileSyncSvc) 服務不支援其中的伺服器端點。 此設定會導致非預期的結果。

### <a name="interoperability"></a>互通性
- 防毒程式、備份及其他存取階層式檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致不想要的重新叫用。 如需詳細資訊，請參閱[針對 Azure 檔案同步進行疑難排解](storage-sync-files-troubleshoot.md)。
- 請勿使用檔案伺服器資源管理員 (FSRM) 或其他檔案檢測。 如果檔案因為檔案檢測而遭到封鎖，檔案檢測會造成無止盡的同步處理失敗。
- 不支援在已安裝 Azure 檔案同步代理程式的伺服器上執行 sysprep，而且此舉可能會導致非預期的結果。 請在部署伺服器映像和完成 sysprep 迷你設定之後，再進行代理程式安裝與伺服器註冊。
- 在相同的磁碟區上不支援重複資料刪除和雲端階層處理。

### <a name="sync-limitations"></a>同步限制
下列項目不會同步，但是系統的其餘部分會繼續正常運作：
- 長度超過 2048 個字元的路徑。
- 安全性描述元的判別存取控制清單 (DACL) 部分 (如果大於 2 KB)。 (這個問題僅適用於單一項目上有超過 40 個存取控制項目 (ACE) 時。)
- 用於稽核之安全性描述元的系統存取控制清單 (SACL) 部分。
- 擴充屬性。
- 替代資料流。
- 重新分析點。
- 永久連結。
- 如果變更從其他端點同步至該檔案，則不會保存壓縮 (如果設定於伺服器檔案上)。
- 任何使用 EFS (或其他使用者模式加密) 加密的檔案，會阻止服務讀取資料。

    > [!Note]  
    > Azure 檔案同步永遠會加密傳輸中的資料。 資料待用時一律會在 Azure 中加密。
 
### <a name="server-endpoint"></a>伺服器端點
- 只能在 NTFS 磁碟區上建立伺服器端點。 Azure 檔案同步目前不支援 ReFS、FAT、FAT32 及其他檔案系統。
- 如果未在刪除伺服器端點之前回收階層式檔案，階層式檔案會變成無法使用的狀態。
- 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。
- 只有叢集磁碟才支援容錯移轉叢集，但叢集共用磁碟區 (CSV) 不提供支援。
- 伺服器端點無法為巢狀。 它可與其他端點平行並存於相同的磁碟區上。
- 請勿在伺服器端點內儲存 OS 或應用程式分頁檔。
- 如果將伺服器重新命名，並不會更新入口網站中的伺服器名稱。 若要更新入口網站中的伺服器名稱，請取消註冊並重新註冊伺服器。

### <a name="cloud-endpoint"></a>雲端端點
- Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。
- 可將儲存體同步服務及 (或) 儲存體帳戶移至現有 Azure AD 租用戶中的不同資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。

### <a name="cloud-tiering"></a>雲端階層處理
- 如果使用 Robocopy 將階層式檔案複製到另一個位置，則所產生的檔案不會進行階層處理。 因為 Robocopy 未正確地在複製作業中包含該屬性，所以可能設定離線屬性。
- 從 SMB 用戶端檢視檔案屬性時，因為 SMB 快取檔案中繼資料，所以離線屬性可能會顯示為不正確設定。
