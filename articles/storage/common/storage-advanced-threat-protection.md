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
ms.openlocfilehash: 7d4f36be51591d6be2b4c42eb8a8950ab52a0258
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782571"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Azure 儲存體的進階威脅防護

Azure 儲存體的進階威脅防護可多提供一道安全智慧，偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 這個防護層讓您不需是安全性專家或管理安全性監視系統，就能解決威脅。 

當活動發生異常時, 就會觸發安全性警示。  這些安全性警示會與[Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)整合, 並透過電子郵件傳送給訂用帳戶系統管理員, 並提供可疑活動的詳細資料, 以及如何調查和修復威脅的建議。

> [!NOTE]
> * Azure 儲存體的先進威脅防護目前僅適用于 Blob 儲存體。
> * 如需定價詳細資料, 包括免費的30天試用版, 請參閱[Azure 資訊安全中心定價頁面]( https://azure.microsoft.com/pricing/details/security-center/)。
> * Azure 儲存體功能的 ATP 目前無法在 Azure 政府和主權雲端區域中使用。

適用于 Azure 儲存體的先進威脅防護, 可內嵌對 Blob 儲存體之讀取、寫入和刪除要求的診斷記錄, 以進行威脅偵測。 若要調查 [Advanced 威脅防護] 中的警示, 您可以使用儲存體分析記錄來查看相關的儲存體活動。 如需詳細資訊, 請參閱如何[設定儲存體分析記錄](storage-monitor-storage-account.md#configure-logging)。

## <a name="set-up-advanced-threat-protection"></a>設定先進的威脅防護 

### <a name="using-the-portal"></a>使用入口網站

1. 在 [https://portal.azure.com](https://portal.azure.com/) 上啟動 Azure 入口網站。

2. 瀏覽至您要保護之 Azure 儲存體帳戶的設定頁面。 在 [設定] 頁面中，選取 [進階威脅防護]。

3. 在 [進階威脅防護] 設定刀鋒視窗中
    * **開啟**進階「威脅防護」
    * 按一下 [儲存]，以儲存新的或更新的進階威脅防護原則。 (映射中的價格僅供範例之用)。

![開啟 Azure 儲存體進階威脅防護](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>使用 Azure 資訊安全中心

當您訂閱 Azure 資訊安全中心中的標準層時, 系統會自動在您所有的儲存體帳戶上設定 Advanced 威脅防護。 您可以在特定訂用帳戶下, 針對儲存體帳戶啟用或停用「先進的威脅防護」, 如下所示:

1. 啟動[Azure 入口網站](https://portal.azure.com)中的**Azure 資訊安全中心**。
1. 從主功能表中, 按一下 [定價] [ **& 設定**]。
1. 按一下您要啟用或停用其儲存體帳戶的 [威脅防護] 的訂用帳戶。

    ![選取訂用帳戶](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. 按一下 [定價層]。
1. 在 [**依資源類型選取定價層**] 區段的 [**儲存體帳戶**] 資料列中, 按一下 [**已啟用**] 或 [**已停用**]。

    ![在資訊安全中心中啟用 ATP](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. 按一下 [儲存]。

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

使用 Azure Resource Manager 範本來部署已啟用「先進的威脅防護」的 Azure 儲存體帳戶。 如需詳細資訊, 請參閱[具有 Advanced 威脅防護的儲存體帳戶](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)。

### <a name="using-azure-policy"></a>使用 Azure 原則

使用 Azure 原則, 在特定訂用帳戶或資源群組下的儲存體帳戶之間啟用先進的威脅防護。

1. 啟動 [Azure**原則-定義**] 頁面。

1. 搜尋在**儲存體帳戶上部署先進的威脅防護**原則。

     ![搜尋原則](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. 選取 Azure 訂用帳戶或資源群組。

    ![選取訂用帳戶或群組](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. 指派原則。

    ![原則定義頁面](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>使用 REST API
使用 Rest API 命令來建立、更新或取得特定儲存體帳戶的「先進的威脅防護」設定。

* [先進的威脅防護-建立](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [先進的威脅防護-取得](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用下列 PowerShell Cmdlet:

  * [啟用先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [取得先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [停用先進的威脅防護](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>探索安全性異常

發生儲存體活動異常狀況時，您會收到電子郵件通知，其中包含可疑安全性事件的相關資訊。 事件的詳細資料包括：

* 異常的本質
* 儲存體帳戶名稱
* 事件時間
* 儲存類型
* 可能的原因 
* 調查步驟
* 補救步驟


該電子郵件還會提供關於可能原因和建議動作的詳細資料，以協助您調查並減輕潛在威脅。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

您可以從 Azure 資訊安全中心的[安全性警示圖格](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)，檢閱與管理目前的安全性警示。 按一下特定警示，即會提供適合用來調查目前威脅及解決未來威脅的詳細資料與動作。

![Azure 儲存體進階威脅防護警示電子郵件](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>防護警示

警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索儲存體帳戶而產生的。 如需這些警示的清單, 請參閱[Azure 儲存體](../../security-center/security-center-alerts-data-services.md#azure-storage)警示

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 儲存體帳戶中的記錄](/rest/api/storageservices/About-Storage-Analytics-Logging)

* 深入了解 [Azure 資訊安全中心](../../security-center/security-center-intro.md)
