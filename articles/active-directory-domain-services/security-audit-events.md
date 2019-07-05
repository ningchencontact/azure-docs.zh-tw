---
title: 啟用安全性稽核，對 Azure AD Domain Services |Microsoft Docs
description: 啟用 Azure AD 網域服務的安全性稽核
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566491"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>啟用安全性稽核，Azure AD 網域服務 （預覽）
Azure AD 網域服務安全性稽核可讓客戶能夠使用 Azure AD 網域服務入口網站，將安全性稽核事件串流至目標資源。 可以接收這些事件的資源包括 Azure 儲存體、 Azure Log Analytics 工作區或 Azure 事件中樞。 不久之後啟用安全性稽核事件，Azure AD 網域服務會傳送所選類別的所有稽核的事件，在目標資源。 安全性稽核事件會讓客戶封存稽核事件至 Azure 儲存體。 此外，客戶可以到安全性資訊及事件管理 (SIEM) 軟體 （或對等項目） 使用事件中樞串流事件，或執行他們自己的分析和 Azure 入口網站中使用 Azure Log Analytics 的深入解析。 

> [!IMPORTANT]
> Azure AD 網域服務安全性稽核是僅適用於 Azure Resource Manager 為基礎的 Azure AD Domain Services 的執行個體。
>
>

## <a name="auditing-event-categories"></a>稽核事件類別目錄
Azure AD 網域服務安全性稽核與傳統稽核隨附的 Active Directory 網域服務網域控制站。 重複使用現有的稽核模式可確保分析事件時，可能會使用相同的邏輯。 Azure AD 網域服務安全性稽核，包括下列的事件類別。

| 稽核類別目錄名稱 | 描述 |
|:---|:---|
| 帳戶登入|稽核嘗試驗證網域控制站上或在本機安全性帳戶管理員 (SAM) 的帳戶資料。</p>登入和登出原則設定和事件追蹤，嘗試存取特定的電腦。 設定與此類別中的事件著重於使用的帳戶資料庫。 此類別包含下列子類別：<ul><li>[稽核認證驗證](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerbero 驗證](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[稽核 Kerberos 服務票證作業](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[稽核其他登入/登出事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 帳戶管理|稽核變更為 使用者和電腦帳戶和群組。 此類別包含下列子類別：<ul><li>[稽核應用程式群組管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[稽核電腦帳戶管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[稽核通訊群組管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[稽核其他帳戶管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[稽核安全性群組管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[稽核使用者帳戶管理](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 追蹤的詳細資料|個別的應用程式和使用者在該電腦上，並了解如何使用電腦時的稽核活動。 此類別包含下列子類別：<ul><li>[稽核 DPAPI 活動](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[稽核 PNP 活動](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[稽核程序建立](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[稽核程序終止](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[稽核 RPC 事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 目錄服務存取|稽核嘗試存取和修改 Active Directory 網域服務 (AD DS) 中的物件。 這些事件會記錄只能在網域控制站的稽核。 此類別包含下列子類別：<ul><li>[稽核詳細的目錄服務複寫](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[稽核目錄服務存取](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[稽核目錄服務變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[稽核目錄服務複寫](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 登入-登出|稽核嘗試以互動方式或透過網路的電腦登入。 這些事件是用於追蹤使用者活動，並找出潛在的攻擊，網路資源。 此類別包含下列子類別：<ul><li>[稽核帳戶鎖定](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[稽核使用者/裝置宣告](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[稽核 IPsec 延伸模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[稽核的群組成員資格](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[稽核 IPsec 主要模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[稽核 IPsec 快速模式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[稽核登出](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[稽核登入](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[稽核網路原則伺服器](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[稽核其他登入/登出事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[稽核特殊登入](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|物件的存取| 稽核嘗試存取特定物件或類型的網路或電腦上的物件。 此類別包含下列子類別：<ul><li>[產生的稽核應用程式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[稽核憑證服務](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[稽核詳細的檔案共用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[稽核檔案共用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[稽核檔案系統](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[稽核篩選平台連線](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[稽核篩選平台封包丟棄](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[稽核控制代碼操作](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[稽核核心物件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[稽核其他物件存取事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[稽核登錄](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[稽核卸除式儲存體](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Audit SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[稽核集中存取原則暫存](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|原則變更|稽核變更為本機系統或網路上的重要安全性原則。 為了安全的網路資源的系統管理員通常會建立原則。 監視變更或嘗試變更這些原則可以是網路的安全性管理的重要層面。 此類別包含下列子類別：<ul><li>[稽核稽核原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[稽核驗證原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[稽核授權原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[稽核篩選平台原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[稽核 MPSSVC 規則層級原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[稽核其他原則變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|特殊權限使用| 稽核一個或多個系統的特定權限的使用。 此類別包含下列子類別：<ul><li>[稽核非機密特殊權限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[稽核機密特殊權限使用](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[稽核其他特殊權限使用事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|系統| 稽核系統層級變更不會納入其他類別且電腦有潛在的安全性影響。 此類別包含下列子類別：<ul><li>[稽核 IPsec 驅動程式](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[稽核其他系統事件](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[稽核安全性狀態變更](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[稽核安全性系統延伸模組](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[稽核系統整合性](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>每個類別的事件識別碼
 特定的動作觸發程序的可稽核的事件時，azure AD 網域服務安全性稽核記錄下列事件識別碼。

| 事件類別目錄名稱 | 事件識別碼 |
|:---|:---|
|帳戶登入安全性|4767, 4774, 4775, 4776, 4777|
|帳戶管理安全性|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|詳細說明追蹤安全性|None|
|DS 存取安全性|5136, 5137, 5138, 5139, 5141|
|登入-登出安全性|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|物件的存取安全性|None|
|原則變更安全性|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|使用安全性的權限|4985|
|系統安全性|4612, 4621|

## <a name="enable-security-audit-events"></a>啟用安全性稽核事件
下列指導方針可協助您成功訂閱 Azure AD 網域服務安全性稽核事件。

> [!IMPORTANT]
> 無法追溯 azure AD 網域服務安全性稽核。 您不從過去擷取事件，或在重新執行從過去的事件。 服務可以只傳送在啟用之後，就會發生的事件。
>

### <a name="choose-the-target-resource"></a>選擇 目標資源
您可以使用 Azure 儲存體、 Azure 事件中樞或 Azure Log Analytics 工作區的任何組合做為目標的資源，您的安全性稽核。 請考慮下列表格以取得您的使用案例的最佳資源。

> [!IMPORTANT]
> 您必須先建立目標資源，才能啟用 Azure AD 網域服務安全性稽核。
>

| 目標資源 | 案例 |
|:---|:---|
|Azure 儲存體|請考慮使用此目標，當您的主要需求是用來儲存為封存之用的安全性稽核事件。 其他目標可用於封存之用;不過，這些目標會提供超過主要的封存需求的功能。 若要建立 Azure 儲存體帳戶，請遵循[建立儲存體帳戶。](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure 事件中心|請考慮使用此目標，您主要的需求時將分享資料分析軟體或安全性資訊和事件管理 (SIEM) 軟體等其他軟體的安全性稽核事件。 若要建立事件中樞時，請遵循[快速入門：建立事件中樞並使用 Azure 入口網站。](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics 工作區|請考慮使用此目標，來分析及直接檢閱從 Azure 入口網站的安全稽核您主要的需求時。  若要建立 Log Analytics 工作區，請遵循[在 Azure 入口網站中建立 Log Analytics 工作區。](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>使用 Azure 入口網站中啟用安全性稽核事件 
1. 在 https://portal.azure.com 登入 Azure 入口網站。  在 Azure 入口網站中，按一下 [所有服務]。 在資源清單中，輸入**網域**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 按一下  **Azure AD Domain Services**。
2. 按一下從清單中的 Azure AD Domain Services 執行個體。
3. 按一下 **診斷設定 （預覽）** 從左邊的動作清單。</p>
![診斷設定動作](./media/security-audit-events/diagnostic-settings-action.png)
4. 輸入診斷組態的名稱 (**aadds 稽核**做為範例)。</p>
![診斷設定 頁面](./media/security-audit-events/diagnostic-settings-page.png)
5. 選取適當的核取方塊旁邊的目標資源，您將使用與安全性稽核事件。
    > [!NOTE]
    > 您無法從這個頁面建立目標資源。
    >
    
    **azure 儲存體：**</p>
    選取 **封存至儲存體帳戶**。 按一下**設定**。 選取 **訂用帳戶**並**儲存體帳戶**您想要封存的安全性稽核事件。 按一下 [確定]  。</p>
    
    ![診斷儲存體設定](./media/security-audit-events/diag-settings-storage.png)
    
    **Azure 事件中樞：**</p>
    選取 **到事件中樞的 Stream**。 按一下**設定**。 在 **選取 事件中樞 頁面**，選取**訂用帳戶**用來建立事件中樞。 接下來，選取**事件中樞命名空間**，**事件中樞名稱**，並**事件中樞原則名稱**。 按一下 [確定]  。</p>
    ![[診斷] 事件中樞設定](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure 記錄分析工作區：**</p>
    選取 [傳送至 Log Analytics]  。 選取 **訂用帳戶**並**Log Analytics 工作區**用來儲存安全性稽核事件。</p>
    ![診斷工作區設定](./media/security-audit-events/diag-settings-log-analytics.png)

6. 選取您想要包含特定的目標資源的記錄檔分類。 如果使用儲存體帳戶，您可以設定保留原則。

    > [!NOTE]
    > 您可以選取不同的記錄類別的單一設定中每個目標資源。 這可讓您選擇其中會記錄您想要保留的 Log Analytics 的記錄類別，因此您想来封存的類別。
    >

7. 按一下 **儲存**來認可變更。 目標資源會收到 Azure AD Domain Services 的安全性稽核事件不久後儲存您的組態。

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>使用 Azure PowerShell 來啟用安全性稽核事件
 
### <a name="prerequisites"></a>必要條件

請遵循文章中的指示[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="enable-security-audits"></a>啟用安全性稽核

1. 驗證至 Azure Resource Manager 的適當租用戶和訂用帳戶使用**Connect AzAccount** Azure PowerShell cmdlet。
2. 建立目標資源的安全性稽核事件。</p>
    **azure 儲存體：**</p>
    請遵循[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell)建立您的儲存體帳戶。</p>
    **Azure 事件中樞：**</p>
    請依照下列[快速入門：建立事件中樞使用 Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell)建立事件中樞。 您可能也需要使用[新增 AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) Azure PowerShell cmdlet 來建立授權規則允許至事件中樞的 Active Directory 的 AD 網域服務權限**命名空間**. 必須包含授權規則**管理**，**接聽**，並**傳送**權限。
    > [!IMPORTANT]
    > 請確定您在事件中樞命名空間和事件中樞上設定授權規則。
       
    </p>
    
    **Azure 記錄分析工作區：**</p>
    請遵循[使用 Azure PowerShell 建立 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh)來建立您的工作區。
3. 取得您的 Azure AD Domain Services 執行個體的資源識別碼。 在開啟、 已驗證 Windows PowerShell 主控台中，輸入下列命令。 使用 **$aadds。ResourceId**變數設定為未來的 cmdlet 的 Azure AD Domain Services 資源識別碼的參數。
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. 使用 **組 AzDiagnosticSetting** cmdlet 來設定要用於 Azure AD 網域服務安全性稽核事件的目標資源的 Azure 診斷設定。 下列變數 $aadds 範例。ResourceId 代表您的 Azure AD Domain Services 執行個體的資源識別碼 （請參閱步驟 3）。</p>
    **azure 儲存體：**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    取代*storageAccountId*與您的儲存體帳戶識別碼。</p>
    
    **Azure 事件中樞：**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    取代*eventHubName*與事件中樞的名稱。 取代*eventHubRuleId*以您先前建立的授權規則識別碼。</p>
    
    **Azure 記錄分析工作區：**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    取代*workspaceId*您先前建立的 Log Analytics 工作區的識別碼。 

## <a name="view-security-audit-events-using-azure-monitor"></a>使用 Azure 監視器檢視安全性稽核事件
記錄分析工作區可讓您檢視和分析使用 Azure 監視器] 和 [Kusto 查詢語言的安全性稽核事件。 查詢語言適合唯讀多電源以容易閱讀語法的分析功能的使用中。
以下是一些資源，幫助您開始使用 Kusto 查詢語言。
* [Azure 監視器文件](https://docs.microsoft.com/azure/azure-monitor/)
* [開始使用 Azure 監視器中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [開始使用 Azure 監視器中的記錄檔查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [建立和共用 Log Analytics 資料的儀表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>範例查詢

### <a name="sample-query-1"></a>範例查詢 1
帳戶鎖定的所有事件過去七天。
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>範例查詢 2
之間的所有帳戶鎖定事件 (4740) 2019 年 6 月 26 日的上午 9 點 並於 2019 年 7 月 1 日午夜，排序按遞增的日期和時間。
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>範例查詢 3
帳戶記錄檔事件前七天 （從現在開始） 具名使用者帳戶。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>範例查詢 4
帳戶登入事件現在是帳戶的前七天名為嘗試登入時使用的密碼不正確 (0xC0000006a) 的使用者。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>範例查詢 5
帳戶登入事件現在是帳戶的前七天名為嘗試登入時，雖然帳戶已鎖定 (0xC0000234) 的使用者。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>範例查詢 6
帳戶登入事件的數目，現在所有的前七天會登入嘗試中發生的所有鎖定的使用者。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>相關內容
* [概觀](https://docs.microsoft.com/azure/kusto/query/)的 Kusto 查詢語言。
* [Kusto 教學課程](https://docs.microsoft.com/azure/kusto/query/tutorial)可讓您熟悉查詢基本概念。
* [範例查詢](https://docs.microsoft.com/azure/kusto/query/samples)可幫助您學習新的方式，以查看您的資料。
* Kusto[最佳做法](https://docs.microsoft.com/azure/kusto/query/best-practices)– 成功將查詢最佳化。














 
