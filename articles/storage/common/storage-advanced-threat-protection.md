---
title: Azure 儲存體的進階威脅防護
description: 設定 Azure 儲存體進階威脅防護來偵測帳戶活動中的異常狀況，並在對您帳戶所進行的嘗試存取可能有害時通知您。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: ed263fec271801ac9c46bbc3125c71ca15b9a330
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153979"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Azure 儲存體的進階威脅防護

Azure 儲存體的進階威脅防護可多提供一道安全智慧，偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 這個防護層讓您不需是安全性專家或管理安全性監視系統，就能解決威脅。 

在活動中的異常狀況發生時，便會觸發安全性警示。  這些安全性警示整合在一起[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)，並也會透過電子郵件傳送給訂用帳戶系統管理員，具有可疑活動和有關如何調查和修復威脅的建議事項的詳細資料。

> [!NOTE]
> * Azure 儲存體的進階的威脅防護，目前僅適用於 Blob 儲存體。
> * 如需定價詳細資料，包括免費的 30 天的試用版，請參閱[Azure 資訊安全中心定價頁面]( https://azure.microsoft.com/pricing/details/security-center/)。
> * Azure 儲存體功能的 ATP 目前不是適用於 Azure government 和主權雲端區域。

Azure 儲存體的進階的威脅防護內嵌讀取、 寫入和刪除要求，威脅偵測的 Blob 儲存體的診斷記錄的檔。 若要調查的警示，從進階威脅防護，您可以檢視相關的儲存體活動使用儲存體分析記錄。 如需詳細資訊，請參閱如何[設定儲存體分析記錄](storage-monitor-storage-account.md#configure-logging)。

## <a name="set-up-advanced-threat-protection"></a>設定進階威脅防護 

### <a name="using-the-portal"></a>使用入口網站

1. 在 [https://portal.azure.com](https://portal.azure.com/) 上啟動 Azure 入口網站。

2. 瀏覽至您要保護之 Azure 儲存體帳戶的設定頁面。 在 [設定]  頁面中，選取 [進階威脅防護]  。

3. 在 [進階威脅防護]  設定刀鋒視窗中
    * **開啟**進階「威脅防護」 
    * 按一下 [儲存]  ，以儲存新的或更新的進階威脅防護原則。 （在映像的價格，例如是僅限用途）。

![開啟 Azure 儲存體進階威脅防護](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>使用 Azure 資訊安全中心
當您訂閱的標準層中 Azure 資訊安全中心，進階威脅防護上已設定您的儲存體帳戶。 如需詳細資訊，請參閱[升級至資訊安全中心標準層以增強安全性](https://docs.microsoft.com/azure/security-center/security-center-pricing)。 （在映像的價格，例如是僅限用途）。

![在 ASC 的標準層](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

使用 Azure Resource Manager 範本來部署進階威脅防護的 Azure 儲存體帳戶已啟用。
如需詳細資訊，請參閱 <<c0> [ 儲存體帳戶與進階威脅防護](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="using-azure-policy"></a>使用 Azure 原則

您可以使用 Azure 原則，在特定的訂用帳戶或資源群組下的儲存體帳戶啟用進階威脅防護。

1. 啟動 Azure**原則-定義**頁面。

1. 搜尋**部署進階威脅防護儲存體帳戶上**原則。

     ![搜尋原則](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. 選取 Azure 訂用帳戶或資源群組。

    ![選取訂用帳戶或群組](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 將原則指派。

    ![原則定義 頁面](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>使用 REST API
使用 Rest API 命令來建立、 更新或取得特定的儲存體帳戶的進階威脅防護設定。

* [進階威脅防護-建立](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [進階威脅防護-取得](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用下列 PowerShell cmdlet:

  * [啟用進階的威脅防護](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [取得進階威脅防護](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [停用進階的威脅防護](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>探索安全性異常情況

發生儲存體活動異常狀況時，您會收到電子郵件通知，其中包含可疑安全性事件的相關資訊。 事件的詳細資料包括：

* 異常的本質
* 儲存體帳戶名稱
* 事件時間
* 儲存體類型
* 可能的原因 
* 調查步驟
* 補救步驟


該電子郵件還會提供關於可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

您可以從 Azure 資訊安全中心的[安全性警示圖格](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)，檢閱與管理目前的安全性警示。 按一下特定警示，即會提供適合用來調查目前威脅及解決未來威脅的詳細資料與動作。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>防護警示

警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索儲存體帳戶而產生的。 這些事件會觸發下列警示：

### <a name="anomalous-access-pattern-alerts"></a>異常存取模式的警示

* **從不尋常的位置存取**:儲存體帳戶的存取模式變更時，會觸發此警示。 例如，有人從不尋常的地理位置存取了儲存體帳戶時。
可能的原因：
   * 攻擊者存取您的儲存體帳戶
   * 合法使用者存取您的儲存體帳戶從新的位置
 
* **應用程式異常**:此警示表示異常的應用程式具有存取此儲存體帳戶。 可能的原因：
   * 攻擊者存取您使用新的應用程式的儲存體帳戶。
   * 合法的使用者用新的應用程式/瀏覽器來存取儲存體帳戶。

* **匿名存取**:此警示表示儲存體帳戶的存取模式中的變更。 例如，此帳戶已以匿名方式存取 （也就是說，沒有任何驗證），這是未預期的最新的存取模式，此帳戶上比較。
可能的原因：
   * 攻擊者程式碼利用到容器的公用讀取權限。
   * 合法的使用者或應用程式已使用容器的公用讀取權限。

### <a name="anomalous-extractupload-alerts"></a>異常的擷取/上傳的警示

* **資料外洩**:此警示表示已擷取相較於此儲存體容器上的最近活動的異常大量的資料。 可能的原因：
   * 攻擊者已從容器擷取大量資料。 (例如： 資料外洩/缺口、 未經授權的資料傳輸)
   * 合法的使用者或應用程式已從容器擷取不尋常的資料量。 (例如： 維護活動)

* **意外的刪除**:此警示表示一或多個未預期的刪除作業發生在儲存體帳戶中，相較於最近的活動，此帳戶。 可能的原因：
   * 攻擊者已刪除的資料，從儲存體帳戶。
   * 合法的使用者執行不尋常的刪除。

* **Azure 雲端服務套件上傳**:此警示表示，Azure 雲端服務封裝 （.cspkg 檔） 已上傳至儲存體帳戶以不尋常的方式，相較於最近的活動，此帳戶。 可能的原因： 
   * 攻擊者正在部署至 Azure 雲端服務在從儲存體帳戶的惡意程式碼的準備。
   * 合法的使用者有尚未準備適用於合法的服務部署。

### <a name="suspicious-storage-activities-alerts"></a>儲存體可疑活動警示

* **存取權限變更**:此警示表示此儲存體容器的存取權限，以不尋常的方式有所變更。 可能的原因： 
   * 攻擊者已變更容器權限以降低其安全性。
   * 合法使用者已變更容器的權限。

* **存取檢查**:此警示表示儲存體帳戶的存取權限都已經檢查過不尋常的方式，相較於最近的活動，此帳戶。 可能的原因： 
   * 未來的攻擊，攻擊者已執行探查。
   * 合法使用者已在儲存體帳戶上執行維護。

* **資料瀏覽**:此警示表示 blob 或容器儲存體帳戶中的都已列舉不尋常的方式，相較於最近的活動，此帳戶。 可能的原因： 
   * 未來的攻擊，攻擊者已執行探查。
   * 合法的使用者或應用程式邏輯探索了儲存體帳戶內的資料。






## <a name="next-steps"></a>後續步驟

* 深入了解[登入 Azure 儲存體帳戶](/rest/api/storageservices/About-Storage-Analytics-Logging)

* 深入了解 [Azure 資訊安全中心](../../security-center/security-center-intro.md)
