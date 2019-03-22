---
title: 找出問題的診斷功能 （預覽）-Azure
description: 說明 Windows 虛擬桌面診斷功能，以及如何使用它。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: f44f4d632fa1fc607f3933be8e15eb939e20a8ae
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318610"
---
# <a name="identify-issues-with-the-diagnostics-feature-preview"></a>找出問題的診斷功能 （預覽）

Windows 虛擬桌面 （預覽） 提供診斷功能 （預覽），可讓系統管理員識別問題，透過單一介面。 每當使用者與系統互動診斷活動記錄的 Windows 虛擬桌面的角色。 每個記錄檔包含例如參與交易、 錯誤訊息、 租用戶的詳細資訊，以及使用者資訊的 Windows 虛擬桌面角色的相關資訊。 診斷活動建立的使用者和系統管理動作，並可分類成三個主要貯體：

* 訂用帳戶活動的摘要： 使用者在嘗試連線至其透過 Microsoft 遠端桌面應用程式的摘要時就會觸發這些活動。
* 連接活動： 每當在嘗試連線到桌上型電腦或 RemoteApp 透過 Microsoft 遠端桌面應用程式，使用者會觸發這些活動。
* 管理活動： 每當執行在系統上，例如建立主應用程式集區、 將使用者指派給應用程式群組，以及建立角色指派的管理作業，系統管理員會觸發這些活動。
  
不會送達 Windows 虛擬桌面的連線不會顯示在診斷結果因為 「 診斷 」 角色服務本身是 Windows 的虛擬桌上型電腦的一部分。 當使用者遇到網路連線問題時，可能會發生 Windows 虛擬桌面連線問題。

若要開始，[下載並匯入的 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)來使用您的 PowerShell 工作階段中，如果您還沒有這麼做。

## <a name="diagnose-issues-with-powershell"></a>診斷問題使用 PowerShell

Windows 虛擬桌面的診斷使用只在一個 PowerShell 指令程式，但包含許多可選的參數，以協助縮小，並找出問題。 下列各節列出您可以執行以診斷問題的 cmdlet。 大部分的篩選器可以一起套用。 列出括弧括住，例如值`<tenantName>`，用來取代適用於您情況的值。

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>擷取您租用戶中的診斷活動

您可以擷取診斷活動的輸入**Get RdsDiagnosticsActivities** cmdlet。 下列範例 cmdlet 會傳回最舊的診斷的活動，從最大排序清單。

```powershell
Get-RdsDiagnosticsActivities -TenantName <tenantName>
```

就像其他 Windows 虛擬桌面 PowerShell cmdlet，您必須使用 **-TenantName**參數來指定您想要用來查詢租用戶的名稱。 適用於幾乎所有的診斷活動查詢租用戶名稱。

### <a name="retrieve-detailed-diagnostic-activities"></a>擷取詳細的診斷活動

**-詳細**參數傳回每個診斷活動提供其他詳細資料。 每個活動的格式會根據其活動類型而有所不同。 **-詳細**參數可以加入至任何**Get RdsDiagnosticsActivities**查詢，如下列範例所示。

```powershell
Get-RdsDiagnosticsActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>擷取特定的診斷活動的活動識別碼

**-ActivityId**參數傳回的特定診斷的活動，如果它存在，如下列範例 cmdlet 中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>篩選使用者診斷活動

**-UserName**參數會傳回由指定使用者所起始的診斷活動的清單，如下列範例 cmdlet 中所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName**參數也可以結合其他選用的篩選參數。

### <a name="filter-diagnostic-activities-by-time"></a>依時間篩選診斷活動

您可以篩選傳回的診斷活動清單 **-StartTime**並 **-EndTime**參數。 **-StartTime**參數會傳回特定的日期，從開始診斷活動清單，如下列範例所示。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

**-EndTime**參數可以新增至 cmdlet，搭配 **-StartTime**參數來指定特定的一段時間，您想要收到的結果。 下列範例 cmdlet 會傳回介於 1 年 8 月和年 8 月 10 診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime**並 **-EndTime**參數也可以結合其他選用的篩選參數。

### <a name="filter-diagnostic-activities-by-activity-type"></a>依活動類型篩選診斷活動

您也可以篩選診斷活動所使用的活動型別 **-ActivityType**參數。 下列 cmdlet 會傳回一份使用者連接：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

下列 cmdlet 會傳回一份系統管理員管理工作：

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get RdsDiagnosticActivities** cmdlet 目前不支援指定為 ActivityType 的摘要。

### <a name="filter-diagnostic-activities-by-outcome"></a>依結果篩選診斷活動

您可以篩選結果，以傳回診斷的活動清單 **-結果**參數。 下列範例 cmdlet 會傳回成功的診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

下列範例 cmdlet 會傳回失敗的診斷活動的清單。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-結果**參數也可以結合其他選用的篩選參數。

## <a name="common-error-scenarios"></a>常見的錯誤狀況

錯誤狀況會分類為服務的內部和外部至 Windows 虛擬桌面。

* 內部問題： 指定不能降低租用戶系統管理員，且需要解析為支援問題的案例。 當提出票證提供活動識別碼、 租用戶名稱和大約的時間範圍內問題時發生。
* 外部的問題： 與相關可減輕系統管理員的案例。 這些都是外部至 Windows 虛擬桌面。

下表列出您的系統管理員可能會遇到的常見錯誤。

>[!NOTE]
>此預覽版不包含完整的錯誤分類，將會定期更新。 若要確保您有最新的資訊，請務必查看這篇文章每月至少一次。

### <a name="external-management-error-codes"></a>外部管理錯誤代碼

|數字代碼|錯誤碼|建議的解決方案|
|---|---|---|
|3|UnauthorizedAccess|嘗試執行系統管理 PowerShell 指令程式的使用者沒有執行這項操作的權限，或輸入其使用者名稱不正確。|
|1000|TenantNotFound|您輸入的租用戶名稱不符合任何現有的租用戶。 檢閱 租用戶名稱有錯字，並再試一次。|
|1006|TenantCannotBeRemovedHasSessionHostPools|您無法刪除租用戶，因為長時間它包含的物件。 首先，刪除工作階段主應用程式集區，然後再試一次。|
|2000|HostPoolNotFound|您輸入的主應用程式集區名稱不符合任何現有的主應用程式集區。 檢閱主應用程式集區名稱有錯字，並再試一次。|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|您無法刪除主應用程式集區，只要它包含的物件。 先在主應用程式集區中移除所有的應用程式群組。|
|2004|HostPoolCannotBeRemovedHasSessionHosts|移除第一次之前刪除的工作階段主應用程式集區的所有工作階段主機。|
|5001|SessionHostNotFound|您查詢的工作階段主機可能會離線。 檢查主應用程式集區的狀態。|
|5008|SessionHostUserSessionsExist |您必須登出工作階段主機上的所有使用者，然後再執行您預期的管理活動。|
|6000|AppGroupNotFound|您輸入的應用程式群組名稱不符合任何現有的應用程式群組。 檢閱應用程式群組名稱有錯字，並再試一次。|
|6022|RemoteAppNotFound|您輸入的 RemoteApp 名稱不符合任何 Remoteapp。 檢閱 RemoteApp 名稱有錯字，並再試一次。|
|6010|PublishedItemsExist|您想要發佈的資源名稱是已經存在的資源相同。 變更資源名稱，然後再試一次。|
|7002|NameNotValidWhiteSpace|請勿在名稱中使用空白字元。|
|8000|InvalidAuthorizationRoleScope|您輸入的角色名稱不符合任何現有的角色名稱。 檢閱角色名稱有錯字，並再試一次。 |
|8001|UserNotFound |您輸入的使用者名稱不符合任何現有的使用者名稱。 檢閱名稱有錯字，並再試一次。|
|8005|UserNotFoundInAAD |您輸入的使用者名稱不符合任何現有的使用者名稱。 檢閱名稱有錯字，並再試一次。|
|8008|TenantConsentRequired|請依照下列指示[此處](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-service)以供您的租用戶的同意。|

### <a name="external-connection-error-codes"></a>外部連線錯誤碼

|數字代碼|錯誤碼|建議的解決方案|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|工作階段主機未正確地加入至 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|連線失敗，因為工作階段主機無法使用。 檢查工作階段主機的健全狀況。|
|-2146233088|ConnectionFailedClientDisconnect|如果您經常看到此錯誤，請確定使用者的電腦連線到網路。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主控件使用者嘗試連線到工作階段狀況不良。 偵錯虛擬機器。|
|-2146233088|ConnectionFailedUserNotAuthorized|使用者不需要存取已發佈應用程式或桌面的權限。 系統管理員移除已發行的資源之後，可能會出現錯誤。 要求使用者重新整理遠端桌面應用程式中的摘要。|
|2|FileNotFound|使用者嘗試存取應用程式未正確安裝，或設定不正確的路徑。|
|3|InvalidCredentials|使用者名稱或使用者所輸入的密碼不符合任何現有的使用者名稱或密碼。 檢閱有錯字認證，並再試一次。|
|8|ConnectionBroken|中斷用戶端和閘道或伺服器之間的連線。 除非發生非預期地所需執行任何動作。|
|14|UnexpectedNetworkDisconnect|網路連線中斷。 要求使用者重新連線。|
|24|ReverseConnectFailed|虛擬機器主機有沒有直接的視野 RD 閘道。 請確定可以解析的閘道 IP 位址。|

## <a name="next-steps"></a>後續步驟

若要深入了解 Windows 虛擬桌面中的角色，請參閱[Windows 的虛擬桌面環境](environment-setup.md)。

若要查看 Windows 的虛擬桌面可以使用 PowerShell cmdlet 的清單，請參閱[PowerShell 參考](/powershell/windows-virtual-desktop/overview)。