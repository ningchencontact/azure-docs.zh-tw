---
title: Azure Active Directory 中的屬性動態群組成員資格規則 | Microsoft Docs
description: 如何建立動態群組成員資格的進階規則，包括支援的運算式規則運算子和參數。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/05/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a48dcff6eedc2aa6e8bb6cd5b0668af72259493b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869082"
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>在 Azure Active Directory 中針對動態群組成員資格建立以屬性為基礎的規則
在 Azure Active Directory (Azure AD) 中，您可以建立自訂規則，以對群組啟用複雜屬性動態成員資格。 本文將詳細說明用以建立適用於使用者或裝置之動態成員資格規則的屬性和語法。 您可以為安全性群組或 Office 365 群組的動態成員資格設定規則。

當使用者或裝置的任何屬性變更時，系統會評估目錄中的所有動態群組規則，以查看變更是否會觸發任何的群組新增或移除。 如果使用者或裝置滿足群組上的規則，就會將他們新增為該群組的成員。 如果他們不再符合此規則，則會予以移除。

> [!NOTE]
> 此功能需要一或多個動態群組成員中每個唯一使用者的 Azure AD Premium P1 授權。 您不需要將授權指派給使用者，使用者就能成為動態群組成員，但是您必須在租用戶中有最小數量的授權，才能涵蓋所有這類使用者。 例如，如果您租用戶中的所有動態群組總計有 1,000 位唯一使用者，則需要至少有 Azure AD Premium P1 的 1,000 個授權，才符合授權需求。
>
> 您可以為裝置或使用者建立動態群組，但無法建立同時包含使用者和裝置的規則。
> 
> 目前無法依據擁有使用者的屬性建立裝置群組。 裝置成員資格規則只能參考目錄中裝置物件的直接屬性。

## <a name="to-create-an-advanced-rule"></a>建立進階規則
1. 使用具備全域管理員或使用者帳戶管理員身分的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。
3. 選取 [所有群組]，然後選取 [新增群組]。

   ![Add new group](./media/groups-dynamic-membership/new-group-creation.png)

4. 在 [群組]  刀鋒視窗上，輸入新群組的名稱和描述。 依據您是要為使用者還是裝置建立規則，在 [成員資格類型] 選取 [動態使用者] 或 [動態裝置]，然後選取 [新增動態查詢]。 您可以使用規則建立器來建立簡單的規則，或自行撰寫進階的規則。 這篇文章包含可用的使用者和裝置屬性的詳細資訊，以及進階規則的範例。

   ![新增動態成員資格規則](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. 建立規則之後，在刀鋒視窗的底部選取 [新增查詢]。
6. 選取 [更多服務]  on the  來建立群組。

> [!TIP]
> 如果您所輸入規則的格式不正確或無效，則群組建立會失敗。 入口網站右上角會顯示通知，當中包含為何無法處理規則的解釋。 請仔細閱讀，了解您需要如何調整規則而讓規則有效。

## <a name="status-of-the-dynamic-rule"></a>動態規則的狀態

您可以在動態群組的 [概觀] 頁面上看到成員資格處理狀態和上次更新日期。
  
  ![動態群組狀態顯示](./media/groups-dynamic-membership/group-status.png)


可能會針對**成員資格處理**狀態顯示下列狀態訊息：
* **評估中**：已收到群組變更，而且正在評估更新。
* **處理中**：正在處理更新。
* **更新完成**：處理已完成，並已建立所有適用的更新。
* **處理錯誤**：評估成員資格規則時發生錯誤，因此無法完成處理。
* **已暫停更新**：系統管理員已暫停動態成員資格規則更新。 MembershipRuleProcessingState 設定為「已暫停」。

可能會針對**成員資格上次更新**狀態顯示下列狀態訊息：
* &lt;**日期和時間**&gt;：上次更新成員資格的時間。
* **進行中**：目前正在更新。
* **未知**：無法擷取上次更新時間。 原因可能是正在新建立群組。

如果處理特定群組的成員資格規則時發生錯誤，則會在群組的 [概觀] 頁面頂端顯示警示。 如果無法處理租用戶內所有群組 24 小時內的暫止動態成員資格更新，則會在 [所有群組] 頂端顯示警示。

![處理錯誤訊息](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>建構進階規則的主體
您可以為群組的動態成員資格建立的進階規則基本上是一個二進位運算式，其中包含三個部分，且會產生 true 或 false 的結果。 這三個部分包括：

* 左側的參數
* 二進位運算子
* 右側的常數

完整的進階規則外觀如下：(leftParameter binaryOperator "RightConstant")，其中可選擇性地使用左右括號來括住整個二進位運算式，也可以選擇性地使用雙引號、只有在其為字串時需要括住右側常數，且左側參數的語法是 user.property。 進階規則可以包含多個二進位運算式，並以 -and、 -or 和 -not 邏輯運算子分隔。

以下是正確建構的進階規則的範例：
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
如需支援的參數和運算式規則運算子的完整清單，請參閱下列各節。 如需了解有哪些用於裝置規則的屬性，請參閱 [使用屬性來建立裝置物件的規則](#using-attributes-to-create-rules-for-device-objects)。

進階規則主體的總長度不得超過 2048 個字元。

> [!NOTE]
> 字串和 regex 運算都不區分大小寫。 您也可以使用 *null* 作為常數來執行 Null 檢查，例如 user.department -eq *null*。
> 包含引號 " 的字串應該使用 ' 字元逸出，例如 user.department -eq \`"Sales"。

## <a name="supported-expression-rule-operators"></a>支援的運算式規則運算子
下表列出所有支援的運算式規則運算子及其用於進階規則主體中的語法：

| 運算子 | 語法 |
| --- | --- |
| Not Equals |-ne |
| Equals |-eq |
| Not Starts With |-notStartsWith |
| 開頭為 |-startsWith |
| Not Contains |-notContains |
| Contains |-contains |
| Not Match |-notMatch |
| Match |-match |
| 在 | -in |
| 不在 | -notIn |

## <a name="operator-precedence"></a>運算子優先順序

所有運算子都會列在下面 (由高至低排定優先順序)。 同一行上運算子的優先順序相等：
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
不管有沒有連字號前置詞，均可使用所有運算子。 優先順序不符合您的需求時，才需要括號。
例如︰
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
相當於：
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>使用 -In 和 -notIn 運算子

如果您想要針對許多不同的值比較使用者屬性的值，您可以使用 -In 或 -notIn 運算子。 使用 -In 運算子的範例如下︰
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
請注意值清單的開頭和結尾有使用 "[" 和 "]"。 當 user.department 的值等於清單中的其中一個值時，這個條件會評估為 True。


## <a name="query-error-remediation"></a>查詢錯誤補救
下表列出一般的錯誤及其更正方式

| 查詢剖析錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| 錯誤：不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>請確定此屬性位於[支援的屬性清單](#supported-properties)中。 |
| 錯誤：屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>屬性類型不支援使用的運算子 (在此範例中，-contains 不能在布林類型上使用)。 屬為屬性類型使用正確的運算子。 |
| 錯誤：查詢編譯錯誤。 |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1.缺少運算子。 使用這兩個 -and 或 -or 聯結述詞<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. 使用 -match 的規則運算式發生錯誤<br/><br/>(user.userPrincipalName -match ".*@domain.ext")，此外：(user.userPrincipalName -match "\@domain.ext$")|

## <a name="supported-properties"></a>支援的屬性
以下是您可以在進階規則中使用的所有使用者屬性：

### <a name="properties-of-type-boolean"></a>布林型別的屬性
允許的運算子

* -eq
* -ne

| properties | 允許的值 | 使用量 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>字串類型的屬性
允許的運算子

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| properties | 允許的值 | 使用量 |
| --- | --- | --- |
| city |任何字串值或 *null* |(user.city -eq "value") |
| country |任何字串值或 *null* |(user.country -eq "value") |
| companyName | 任何字串值或 *null* | (user.companyName -eq "value") |
| department |任何字串值或 *null* |(user.department -eq "value") |
| displayName |任何字串值 |(user.displayName -eq "value") |
| employeeId |任何字串值 |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |任何字串值或 *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任何字串值或 *null* |(user.givenName -eq "value") |
| jobTitle |任何字串值或 *null* |(user.jobTitle -eq "value") |
| mail |任何字串值或 *null* (使用者的 SMTP 位址) |(user.mail -eq "value") |
| mailNickName |任何字串值 (使用者的郵件別名) |(user.mailNickName -eq "value") |
| mobile |任何字串值或 *null* |(user.mobile -eq "value") |
| objectId |使用者物件的 GUID |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 已從內部部署環境同步至雲端之使用者的內部部署安全性識別碼 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任何字串值或 *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任何字串值或 *null* |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任何字串值或 *null* |(user.sipProxyAddress -eq "value") |
| state |任何字串值或 *null* |(user.state -eq "value") |
| streetAddress |任何字串值或 *null* |(user.streetAddress -eq "value") |
| surname |任何字串值或 *null* |(user.surname -eq "value") |
| telephoneNumber |任何字串值或 *null* |(user.telephoneNumber -eq "value") |
| usageLocation |兩個字母的國家 (地區) 代碼 |(user.usageLocation -eq "US") |
| userPrincipalName |任何字串值 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>字串集合類型的屬性
允許的運算子

* -contains
* -notContains

| properties | 允許的值 | 使用量 |
| --- | --- | --- |
| otherMails |任何字串值 |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>多重值屬性
允許的運算子

* -any (集合中至少有一個項目符合條件時滿足)
* -all (集合中的所有項目符合條件時滿足)

| properties | 值 | 使用量 |
| --- | --- | --- |
| assignedPlans |集合中的每個物件都會公開下列字串屬性：capabilityStatus、service、servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

多重值屬性是相同類型之物件的集合。 您可以使用 -any 和 -all 運算子，分別將條件套用至集合中的一個或所有項目。 例如︰

assignedPlans 是多重值屬性，可列出所有指派給使用者的服務方案。 下列運算式將會選取擁有 Exchange Online (方案 2) 服務方案 (也處於 [已啟用] 狀態) 的使用者：

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(GUID 識別碼可識別 Exchange Online (方案 2) 服務方案。)

> [!NOTE]
> 如果您想要識別已啟用 Office 365 (或其他 Microsoft Online 服務) 功能的所有使用者，例如以一組特定原則鎖定他們，此屬性非常有用。

下列運算式將會選取有任何服務方案與 Intune 服務 (透過服務名稱 "SCO" 來識別) 相關聯的所有使用者：
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>使用 Null 值

若要在規則中指定 null 值，您可以使用 *null* 值。 注意，請不要在 *null* 文字前後使用引號，這麼做會使系統將它解釋為常值字串值。 -not 運算子不能用來作為 null 的比較運算子。 如果使用它，則不論您使用 null 還是 $null 都會收到錯誤。 請改用 -eq 或 -ne。 參考 null 值的正確方法如下：
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>擴充屬性和自訂屬性
動態成員資格規則支援擴充屬性和自訂屬性。

擴充屬性會從內部部署 Windows Server AD 進行同步處理，並採用 "ExtensionAttributeX" 格式，其中 X 等於 1-15。
以下是使用擴充屬性的規則範例：
```
(user.extensionAttribute15 -eq "Marketing")
```
自訂屬性會從內部部署 Windows Server AD 或從連線的 SaaS 應用程式進行同步處理，並採用 "user.extension_[GUID]\__[Attribute]" 格式，其中 [GUID] 是 AAD 中的唯一識別碼 (適用於在 AAD 中建立屬性的應用程式)，而 [Attribute] 是其建立的屬性名稱。
以下是使用自訂屬性的規則範例：
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
使用 [Graph 總管] 查詢使用者的屬性並搜尋屬性名稱，即可在目錄中找到自訂屬性名稱。

## <a name="direct-reports-rule"></a>「直屬員工」規則
您可以建立一個群組，其中包含某位經理的所有直屬員工。 當經理的直屬員工在未來變更時，系統將會自動調整群組的成員資格。

> [!NOTE]
> 1. 若要讓規則得以運作，請確定已對您租用戶中的使用者正確設定 [經理識別碼] 屬性。 您可以在其 **[設定檔] 索引標籤**上檢查使用者的目前值。
> 2. 此規則只支援**直屬**員工。 目前無法為巢狀階層建立群組；例如包含直屬員工及其員工的群組。
> 3. 此規則無法與任何其他進階規則結合。

**設定群組**

1. 依照[建立進階規則](#to-create-the-advanced-rule)一節中的步驟 1-5 操作，然後在 [成員資格類型] 中選取 [動態使用者]。
2. 在 [動態成員資格規則]  刀鋒視窗上，使用下列語法來輸入規則：

    *"{objectID_of_manager}" 的直屬員工*

    有效規則的範例：
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. 儲存規則之後，即會將具有指定經理識別碼值的所有使用者新增至群組。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用屬性來建立裝置物件的規則
您也可以建立規則以在群組中選取成員資格的裝置物件。 可以使用下列裝置屬性。

 裝置屬性  | 值 | 範例
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 任何字串值 |(device.displayName -eq "Rob Iphone”)
 deviceOSType | 任何字串值 | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | 任何字串值 | (device.OSVersion -eq "9.1")
 deviceCategory | 有效的裝置類別名稱 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 任何字串值 | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 任何字串值 | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 個人、公司、未知 | (device.deviceOwnership -eq "Company")
 domainName | 任何字串值 | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Apple 裝置註冊設定檔或 Windows Autopilot 設定檔名稱 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (適用於行動裝置)<br>PC (適用於 Intune PC 代理程式管理的電腦) | (device.managementType -eq "MDM")
 organizationalUnit | 符合內部部署 Active Directory 所設定的組織單位名稱的任何字串值 | (device.organizationalUnit -eq "US PCs")
 deviceId | 有效的 Azure AD 裝置識別碼 | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 有效的 Azure AD 物件識別碼 |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>將動態成員資格變更為靜態，反之亦然
您可以變更在群組中管理成員資格的方式。 當您想要在系統中保留相同的群組名稱和識別碼，讓任何現有的群組參考仍然有效時，這非常實用；建立新的群組需要更新這些參考。

我們已更新 Azure AD 系統管理中心，以新增這項功能的支援。 現在，客戶可以透過 Azure AD 系統管理中心或 PowerShell Cmdlet，將現有的群組從動態成員資格轉換為指派的成員資格，反之亦然，如下所示。

> [!WARNING]
> 將現有的靜態群組變更為動態群組時，系統將從群組中移除所有現有的成員，然後再處理成員資格規則，以加入新的成員。 如果群組用來控制對應用程式或資源的存取，則在完全處理成員資格規則之前，原始成員可能會失去存取權。
>
> 建議您先測試新的成員資格規則，以確保群組中的新成員資格如預期般運作。

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>使用 Azure AD 系統管理中心來變更群組上的成員資格管理 

1. 使用具備您租用戶中全域管理員或使用者帳戶管理員身分的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
2. 選取 [群組]。
3. 從 [所有群組] 清單中，開啟您想要變更的群組。
4. 選取 [屬性] 。
5. 在群組的 [屬性] 頁面上，依據您所需的成員資格類型，選取 [已指派] (靜態)、[動態使用者] 或 [動態裝置] 作為 [成員資格類型]。 針對動態成員資格，您可以使用規則建立器來選取簡單規則的選項，或自行撰寫進階規則。 

下列步驟是針對某個使用者群組將群組從靜態變更為動態成員資格的範例。 

1. 在所選群組的 [屬性] 上，選取 [動態使用者] 作為 [成員資格類型]，然後在說明群組成員資格變更的對話方塊上，選取 [是] 以繼續。 
  
   ![選取動態使用者作為成員資格類型](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. 選取 [新增動態查詢]，然後提供規則。
  
   ![輸入規則](./media/groups-dynamic-membership/enter-rule.png)
  
3. 建立規則之後，在頁面底部選取 [新增查詢]。
4. 在群組的 [屬性] 頁面上，選取 [儲存] 以儲存變更。 群組的 [成員資格類型] 會立即在群組清單中更新。

> [!TIP]
> 如果您輸入的進階規則不正確，群組轉換可能會失敗。 入口網站右上角將會顯示通知，當中包含系統為什麼無法接受該規則的解釋。 請仔細閱讀，以了解您可以如何調整規則來讓規則變成有效。

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>使用 PowerShell 來變更群組上的成員資格管理

> [!NOTE]
> 若要變更動態群組屬性，您必須使用**預覽版本** [Azure AD PowerShell 第 2 版](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)中的 Cmdlet。 您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview)安裝預覽版。

以下是在現有群組上切換成員資格管理的函式範例。 在此範例中，需小心以正確操作 GroupTypes 屬性，並保留任何與動態成員資格無關的值。

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
若要讓群組變成靜態的：
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
若要讓群組變成動態的：
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 中群組的其他資訊。

* [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](groups-dynamic-membership.md)
