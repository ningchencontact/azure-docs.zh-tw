---
title: Azure AD 密碼保護 (預覽) 中的疑難排解和記錄
description: 了解 Azure AD 密碼保護 (預覽) 的記錄和一般疑難排解
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: d9f3ba642a5d00594aa6bdef597d5db43c2fc121
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188975"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>預覽：Azure AD 密碼保護的監視、報告和疑難排解

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

在部署 Azure AD 密碼保護之後，監視和報告將是必要工作。 本文將深入探討以協助您了解各項服務將資訊記錄於何處，以及如何報告 Azure AD 密碼保護的使用情形。

## <a name="on-premises-logs-and-events"></a>內部部署記錄和事件

### <a name="dc-agent-admin-log"></a>DC 代理程式系統管理記錄

在每個網域控制站上，DC 代理程式服務軟體會將其密碼驗證結果 (和其他狀態) 寫入至本機事件記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

事件會由各種 DC 代理程式元件使用下列範圍進行記錄：

|元件 |事件識別碼範圍|
| --- | --- |
|DC 代理程式密碼篩選 dll| 10000-19999|
|DC 應用程式服務裝載處理序| 20000-29999|
|DC 代理程式服務原則驗證邏輯| 30000-39999|

就成功的密碼驗證作業而言，通常會有一個從 DC 代理程式密碼篩選 dll 記錄的事件。 如果密碼驗證作業失敗，則通常會記錄兩個事件，一個來自 DC 代理程式服務，另一個來自 DC 代理程式密碼篩選 dll。

系統會根據下列因素，記錄會擷取這些情況的離散事件：

* 指定的密碼是否設定或變更。
* 指定密碼的驗證是通過還是失敗。
* 驗證是否因為 Microsoft 全域原則與組織原則有所衝突而失敗。
* 目前的密碼原則目前是否開啟僅限稽核模式。

主要的密碼驗證相關事件如下：

|   |密碼變更 |密碼設定|
| --- | :---: | :---: |
|通過 |10014 |10015|
|失敗 (未通過客戶密碼原則)| 10016、30002| 10017、30003|
|失敗 (未通過 Microsoft 密碼原則)| 10016、30004| 10017、30005|
|僅限稽核通過 (將不會通過客戶密碼原則)| 10024、30008| 10025、30007|
|僅限稽核通過 (將不會通過 Microsoft 密碼原則)| 10024、30010| 10025、30009|

> [!TIP]
> 傳入的密碼會先根據 Microsoft 全域密碼進行驗證，如果驗證失敗，則不會執行進一步的處理。 這和對 Azure 中的密碼變更所執行的是相同的行為。

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>事件識別碼 10014 (成功密碼設定) 的範例事件記錄訊息

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>事件識別碼 10017 和 30003 (失敗密碼設定) 的範例事件記錄訊息

10017：

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003：

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>事件識別碼 30001 (因為沒有可用原則而接受的密碼) 的範例事件記錄訊息

```
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>事件識別碼 30006 (強制執行的新原則) 的範例事件記錄訊息

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>DC 代理程式運作記錄

DC 代理程式服務也會將與運作有關的事件記錄至以下記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>DC 代理程式追蹤記錄

DC 代理程式服務也可以將偵錯層級追蹤事件的詳細資訊記錄至以下記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

依預設會停用追蹤記錄。

> [!WARNING]
>  啟用時，追蹤記錄會接收大量事件，而可能會影響網域控制站的效能。 因此，只有在問題需要更深入的調查時才應啟用此增強型記錄，且應盡可能縮短啟用時間。

#### <a name="dc-agent-text-logging"></a>DC 代理程式文字記錄

DC 代理程式服務可以透過設定以下登錄值，設定為寫入至文字記錄檔：

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD 值)

依預設會停用文字記錄。 變更此值時需重新啟動 DC 代理程式服務，此變更才會生效。 啟用 DC 代理程式服務時，將會寫入位於以下位置的記錄檔：

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> 文字記錄會收到與可記錄至追蹤記錄的相同偵錯層級項目，但其格式通常是較容易檢閱和分析的格式。

> [!WARNING]
> 啟用時，此記錄會接收大量事件，而可能會影響網域控制站的效能。 因此，只有在問題需要更深入的調查時才應啟用此增強型記錄，且應盡可能縮短啟用時間。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密碼保護 Proxy 服務

#### <a name="proxy-service-event-logs"></a>Proxy 服務事件記錄檔

Proxy 服務會發出最小集合的事件至下列事件記錄檔：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

Proxy 服務也可以將偵錯層級追蹤事件的詳細資訊記錄至以下記錄：

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

依預設會停用追蹤記錄。

> [!WARNING]
> 啟用時，追蹤記錄會接收大量事件，而可能會影響 Proxy 主機的效能。 因此，只有在問題需要更深入的調查時才應啟用此記錄，且應盡可能縮短啟用時間。

#### <a name="proxy-service-text-logging"></a>Proxy 服務文字記錄

Proxy 服務可以透過設定以下登錄值，設定為寫入至文字記錄檔：

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD 值)

依預設會停用文字記錄。 變更此值時需重新啟動 Proxy 服務，此變更才會生效。 啟用 Proxy 服務時，將會寫入位於以下位置的記錄檔：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> 文字記錄會收到與可記錄至追蹤記錄的相同偵錯層級項目，但其格式通常是較容易檢閱和分析的格式。

> [!WARNING]
> 啟用時，此記錄會接收大量事件，而可能會影響網域控制站的效能。 因此，只有在問題需要更深入的調查時才應啟用此增強型記錄，且應盡可能縮短啟用時間。

#### <a name="powershell-cmdlet-logging"></a>PowerShell Cmdlet 記錄

大部分的 Azure AD 密碼保護 Powershell Cmdlet 會寫入至位於以下位置的文字記錄檔：

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

如果發生 Cmdlet 錯誤，且原因和/或解決方式不明顯，可能也會參考這些文字記錄檔。

### <a name="emergency-remediation"></a>緊急補救

如果發生 DC 代理程式服務造成問題的情況，可以立即將 DC 代理程式服務關閉。 DC 代理程式密碼篩選 dll 仍會嘗試呼叫非執行中服務，且會記錄警告事件 (10012、10013)，但在這段期間傳入的所有密碼都會被接受。 隨後如有需要，也可以透過 Windows 服務控制管理員，在啟動類型設為 [已停用] 的情況下設定 DC 代理程式服務。

### <a name="performance-monitoring"></a>效能監視

DC 代理程式服務軟體會安裝名為 **Azure AD 密碼保護**的效能計數器物件。 以下是目前可用的效能計數器：

|效能計數器名稱 | 說明|
| --- | --- |
|已處理的密碼 |此計數器會顯示自上次重新啟動後已處理 (已接受或拒絕) 的密碼總數。|
|已接受的密碼 |此計數器會顯示自上次重新啟動後已接受的密碼總數。|
|已拒絕的密碼 |此計數器會顯示自上次重新啟動後已拒絕的密碼總數。|
|進行中的密碼篩選要求 |此計數器會顯示目前正在進行中的密碼篩選要求數目。|
|尖峰密碼篩選要求 |此計數器會顯示自上次重新啟動後的並行密碼篩選要求的尖峰數目。|
|密碼篩選要求錯誤 |此計數器會顯示自上次重新啟動後因錯誤而失敗的密碼篩選要求總數。 當 Azure AD 密碼保護 DC 代理程式服務未執行時，就可能發生錯誤。|
|密碼篩選要求/秒 |此計數器會顯示密碼的處理速率。|
|密碼篩選要求處理時間 |此計數器會顯示處理密碼篩選要求所需的平均時間。|
|尖峰密碼篩選要求處理時間 |此計數器會顯示自上次重新啟動後的尖峰密碼篩選要求處理時間。|
|因稽核模式而接受的密碼 |此計數器會顯示自上次重新啟動後，在正常情況下會遭到拒絕、但因密碼原則設定為稽核模式而被接受的密碼總數。|

## <a name="directory-services-repair-mode"></a>目錄服務修復模式

如果網域控制站在開機後進入目錄服務修復模式，則 DC 代理程式服務會偵測到此狀況將導致所有密碼驗證或強制活動停用，無論目前作用中的原則組態為何。

## <a name="domain-controller-demotion"></a>網域控制站降階

目前支援將仍在執行 DC 代理程式軟體的網域控制站降階。 不過，系統管理員應了解 DC 代理程式軟體在降階程序中會繼續執行，並且繼續強制執行目前的密碼原則。 新的本機系統管理員帳戶密碼 (指定為降階作業的一部分) 會像任何其他密碼一樣受到驗證。 Microsoft 建議在 DC 降階程序中應為本機系統管理員帳戶選擇安全的密碼；但 DC 代理程式軟體對新的本機系統管理員帳戶密碼所做的驗證，可能會干擾到既有的降階作業程序。

在降階成功後，網域控制站即已重新啟動，並重新以一般成員伺服器的形式執行，且 DC 代理程式軟體會回復為以被動模式執行。 其後您可以隨時將其解除安裝。

## <a name="removal"></a>移除

如果決定要解除安裝公用預覽版軟體，並從網域和樹系清除所有相關的狀態，您可以使用下列步驟來完成這項工作：

> [!IMPORTANT]
> 這些步驟務必要依序執行。 如果有任何 Proxy 服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件。 如果有任何 DC 代理程式服務的執行個體仍繼續執行，它將會定期重新建立其 serviceConnectionPoint 物件與 sysvol 狀態。

1. 從所有機器解除安裝密碼保護 Proxy 軟體。 此步驟**不需要**重新開機。
2. 從所有網域控制站解除安裝 DC 代理程式軟體。 此步驟**需要**重新開機。
3. 手動移除每個網域命名內容中的所有 Proxy 服務連接點。 您可以使用下列 Active Directory Powershell 命令找出這些物件的位置：

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   請勿省略 $keywords 變數值結尾處的星號 ("*")。

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。 

4. 手動移除在每個網域命名內容中所有的 DC 代理程式連接點。 樹系中的每個網域控制站可能各有一個此類物件，視先前部署公用預覽版軟體的範圍而定。 您可以使用下列 Active Directory Powershell 命令找出該物件的位置：

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   透過 `Get-ADObject` 命令找到的產生物件隨後可使用管線傳送到 `Remove-ADObject`，或以手動方式刪除。

5. 手動移除樹系層級的組態狀態。 樹系組態狀態會保存在 Active Directory 組態命名內容中的容器內。 您可以依照下列方式加以探索和刪除：

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectonConfigContainer
   ```

6. 手動刪除下列資料夾及其所有內容，以手動移除所有 sysvol 相關狀態：

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   如有必要，也可在指定的網域控制站上從本機存取此路徑；預設位置會類似於下列路徑：

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   如果已將 sysvol 共用設定於非預設位置中，此路徑將會不同。

## <a name="next-steps"></a>後續步驟

如需關於全域和自訂禁用密碼清單的詳細資訊，請參閱[禁用不當密碼](concept-password-ban-bad.md)一文
