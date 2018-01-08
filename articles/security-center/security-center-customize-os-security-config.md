---
title: "自訂 Azure 資訊安全中心 [預覽] 中的 OS 安全性設定 |Microsoft 文件"
description: "本文將教您如何自訂安全性中心評估"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 2fa63515d290e6700fbe4a90ae509f4635b19f29
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2018
---
# <a name="customizing-os-security-configurations-in-azure-security-center-preview"></a>自訂 Azure 資訊安全中心 [預覽] 中的 OS 安全性組態

了解如何自訂作業系統安全性組態評估中使用本逐步解說 Azure 資訊安全中心。

## <a name="what-are-os-security-configurations"></a>作業系統安全性設定有哪些？

Azure 資訊安全中心來強化作業系統使用的一組超過 150 部的建議規則監視的安全性組態，包括規則與防火牆、 稽核、 密碼原則，以及更多有關。 如果電腦發現有弱點的組態，則會產生的安全性建議。

自訂的規則可協助組織來控制哪些設定選項是更適合其環境。 這項功能可讓使用者設定的自訂的評估原則，並將它套用在訂閱中所有適用的電腦上。

> [!NOTE]
> - 目前作業系統安全性組態自訂適用於 Windows Server 2008、 2008R2、 2012、 2012R2 僅限作業系統。
- 設定會套用到所有的 Vm，並且電腦連線到選取的訂用帳戶底下的所有工作區。
- 作業系統安全性組態自訂是只能在資訊安全中心標準層上使用。
>
>

如何自訂作業系統安全性組態規則？

您可以啟用和停用特定的規則、 變更現有的規則所需的設定以及新增新的規則，根據支援的規則類型 （登錄、 稽核原則和安全性原則），來自訂作業系統安全性組態規則。 目前，所需的設定必須是精確的值。

新的規則必須是與相同類型的其他現有的規則中相同的格式和結構。

> [!NOTE]
> 若要自訂作業系統的安全性設定，您必須被指派訂用帳戶擁有者、 參與者訂用帳戶或安全性系統管理員的角色。
>
>

## <a name="customize-security-configuration"></a>自訂安全性設定

自訂預設的作業系統資訊安全中心的安全性組態：

1.  開啟 [資訊安全中心] 儀表板。

2.  在資訊安全中心主功能表上選取**安全性原則**。  **資訊安全中心的安全性原則**隨即開啟。

3.  選取您想要執行的自訂，訂用帳戶。

    ![](media/security-center-customize-os-security-config/open-security-policy.png)

4. 在下**原則元件**，選取**編輯安全性組態**。

6.  **編輯安全性組態**隨即開啟。 遵循的步驟來下載、 編輯和修改過的檔案上傳至畫面中反白顯示。

    ![](media/security-center-customize-os-security-config/blade.png)

  > [!NOTE]
  > 根據預設，您所下載的組態檔位於*json*格式。 如需有關如何修改這個檔案的指示，請移至[自訂設定檔](#customize-the-configuration-file)。
  >

7. 已成功儲存檔案之後, 設定會套用至所有 Vm 和電腦連接到選取的訂用帳戶底下的所有 workspace(s)。 此程序可能需要一些時間，通常幾分鐘的時間，但可以花較長因為它相依於基礎結構大小。 選取**儲存**以認可變更，否則不儲存原則。

    ![](media/security-center-customize-os-security-config/save-successfully.png)

在任何時間點，您可以重設目前的原則設定的預設原則狀態選取**重設**選項**編輯作業系統安全性組態規則**。 如果您選擇此選項時，您會收到下列快顯警示。 選取**是**確認。

![](media/security-center-customize-os-security-config/edit-alert.png)

## <a name="customize-the-configuration-file"></a>自訂設定檔

自訂檔案中每個支援的作業系統版本有一組規則 （規則集）。 每一組規則有它自己的名稱和唯一識別碼，如下列範例所示：

![](media/security-center-customize-os-security-config/custom-file.png)

> [!NOTE]
> 使用 Visual Studio 中，編輯此檔案，但您也可以使用 [記事本]，只要您持有 JSON 檢視器外掛程式安裝。
>
>

編輯此檔案時，您可以修改一個規則，或全都。 每個規則集包含*規則*區段，其中包含的規則，分為 3 種規則： 登錄、 稽核原則和安全性原則，如下所示：

![](media/security-center-customize-os-security-config/rules-section.png)

每個類別都有它自己的屬性集。 針對現有的規則，可以在您變更下列項目：

- expectedValue： 這個屬性的欄位資料型別必須符合支援的值，每個每個規則類型，例如：

  - baselineRegistryRules： 的值應該符合 [regValueType] (https://msdn.microsoft.com/library/windows/desktop/ms724884 (v=vs.85) 該規則所定義的。

  - baselineAuditPolicyRules: 值應該是字串值，下列其中之一：

    - 成功和失敗

    - 成功

  - baselineSecurityPolicyRules: 值應該是字串值，下列其中之一：

    - 「 沒有 」

    - 列出允許使用者群組，例如: 「 系統管理員，備份操作員 」

-   狀態： 可能包含 「 停用 」 或"Enabled"選項的字串。 此私人預覽版本中，字串會區分大小寫。

這些是唯一可設定的欄位。 如果您違反的檔案格式或大小，您將無法儲存變更。 無法處理檔案時，就會發生下列錯誤訊息：

![](media/security-center-customize-os-security-config/invalid-json.png)

請參閱[錯誤碼](#error-codes)如潛在錯誤的清單。

下面，您會有這些規則，可以變更的屬性 （以粗體顯示） 的範例：

**規則 區段中：** baselineRegistryRules
```
{

    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\LanManServer\\\\Parameters",
    "valueName": "restrictnullsessaccess",
    "ruleId": "f9020046-6340-451d-9548-3c45d765d06d",
    "originalId": "0f319931-aa36-4313-9320-86311c0fa623",
    "cceId": "CCE-10940-5",
    "ruleName": "Network access: Restrict anonymous access to Named Pipes and
    Shares",
    "ruleType": "Registry",
    "**expectedValue**": "1",
    "severity": "Warning",
    "analyzeOperation": "Equals",
    "source": "Microsoft",
    "**state**": "Disabled"

}
```

**規則 區段中：** baselineAuditPolicyRules
```
{
"auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
"ruleId": "37745508-95fb-44ec-ab0f-644ec0b16995",
"originalId": "2ea0de1a-c71d-46c8-8350-a7dd4d447895",
"cceId": "CCE-11001-5",
"ruleName": "Audit Policy: Account Management: Other Account Management Events",
"ruleType": "AuditPolicy",
"**expectedValue**": "Success and Failure",
"severity": "Critical",
"analyzeOperation": "Equals",
"source": "Microsoft",
"**state**": "Enabled"
},
```

**規則的章節：** baselineSecurityPolicyRules
```
{
"sectionName": "Privilege Rights",
"settingName": "SeIncreaseWorkingSetPrivilege",
"ruleId": "b0ec9d5e-916f-4356-83aa-c23522102b33",
"originalId": "b61bd492-74b0-40f3-909d-36b9bf54e94c",
"cceId": "CCE-10548-6",
"ruleName": "Increase a process working set",
"ruleType": "SecurityPolicy",
"**expectedValue**": "Administrators, Local Service",
"severity": "Warning",
"analyzeOperation": "Equals",
"source": "Microsoft", "**state**": "Enabled"
},
```

有一些不同的作業系統類型重複的規則。 重複的規則有相同的 'originalId'。

## <a name="adding-a-new-custom-rule"></a>加入新的自訂規則

您也可以建立新的規則。 之前建立新的規則，請記住下列限制：

-   結構描述版本*baselineId*和*baselineName*無法變更。

-   無法移除規則集。

-   無法加入規則集。

-   允許 （包括預設規則） 的規則數目上限是 1000年規則。

新的自訂規則以新的自訂來源標記 (！ ="Microsoft")。 *RuleId*欄位可能是 null 或空白。 如果是空的 Microsoft 會產生一個。 如果它不是空的它必須是有效的唯一 GUID 跨所有規則 （預設和自訂）。 檢閱下面有關核心欄位的條件約束：

-   *originalId* -可能是 null 或空白。 如果*originalId*是不是空的它應該是有效的 GUID。

-   *cceId* -可能是 null 或空白。 如果*cceId*是不是空的它必須是唯一的。

-   *ruleType* -一個的： 登錄、 AuditPolicy 或空格。

-   *嚴重性*-一個的： Unknown、 重大、 警告還是資訊。

-   *analyzeOperation* -必須是等號。

-   *auditPolicyId* -必須是有效的 GUID。

-   *regValueType* -必須是其中一個： Int、 Long、 字串或 MultipleString。

> [!NOTE]
> 登錄區必須是*LocalMachine*。
>
>

新的自訂規則的範例：

**登錄**:
```
    {
    "hive": "LocalMachine",
    "regValueType": "Int",
    "keyPath":
    "System\\\\CurrentControlSet\\\\Services\\\\Netlogon\\\\MyKeyName",
    "valueName": "MyValueName",
    "originalId": "",
    "cceId": "",
    "ruleName": "My new registry rule”, "baselineRuleType": "Registry",
    "expectedValue": "123", "severity": "Critical",
    "analyzeOperation": "Equals",
    "source": "MyCustomSource",
    "state": "Enabled"
   }
```
**安全性原則**:
```
{

   "sectionName": "Privilege Rights",
   "settingName": "SeDenyBatchLogonRight",
   "originalId": "",
   "cceId": "",
   "ruleName": "My new security policy rule", "baselineRuleType":
   "SecurityPolicy",
   "expectedValue": "Guests",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```
**稽核原則：**
```
   {
   "auditPolicyId": "0cce923a-69ae-11d9-bed3-505054503030",
   "originalId": "",
   "cceId": "",
   "ruleName": " My new audit policy rule ", "baselineRuleType": "AuditPolicy",
   "expectedValue": " Failure",
   "severity": "Critical",
   "analyzeOperation": "Equals", "source": " MyCustomSource ",
   "state": "Enabled"
   }
```

## <a name="file-upload-failures"></a>檔案上傳失敗

如果提交的組態檔無效，因為發生錯誤或格式化的值，會顯示失敗錯誤。 您可以下載補救並修正錯誤後再重新提交已更正的組態檔的詳細的錯誤 csv 報表。

![](media/security-center-customize-os-security-config/invalid-configuration.png)

錯誤檔的範例：

![](media/security-center-customize-os-security-config/errors-file.png)

## <a name="error-codes"></a>錯誤碼

下列清單具有所有可能的錯誤：

| **錯誤**                                | **說明**                                                                                                                              |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| BaselineConfiguratiohSchemaVersionError  | 無效或空白，找不到屬性 'schemaVersion'。 值必須設定為 '{0}' 中。                                                         |
| BaselineInvalidStringError               | 屬性 '{0}' 不能包含 '\\n'。                                                                                                         |
| BaselineNullRuleError                    | 基準組態規則清單包含具有值 'null' 的規則。                                                                         |
| BaselineRuleCceIdNotUniqueError          | CCE ID '{0}' 不是唯一的。                                                                                                                  |
| BaselineRuleEmptyProperty                | 屬性: '{0}' 已遺失或無效。                                                                                                       |
| BaselineRuleIdNotInDefault               | 此規則的來源屬性 '的 Microsoft'，但找不到在 Microsoft 預設規則集。                                                   |
| BaselineRuleIdNotUniqueError             | 規則 Id 不是唯一的。                                                                                                                       |
| BaselineRuleInvalidGuid                  | 屬性 '{0}' 找不到不正確。 值不是有效的 Guid。                                                                             |
| BaselineRuleInvalidHive                  | 登錄區必須是 LocalMachine。                                                                                                                   |
| BaselineRuleNameNotUniqueError           | 規則名稱不是唯一的。                                                                                                                 |
| BaselineRuleNotExistInConfigError        | 新的組態中找不到規則。 無法刪除規則。                                                                     |
| BaselineRuleNotFoundError                | 此規則找不到預設基準規則集。                                                                                        |
| BaselineRuleNotInPlace                   | 此規則會比對類型為 {0} 預設規則，並列出 {1} 清單中。                                                                       |
| BaselineRulePropertyTooLong              | 屬性: '{0}' 名稱太長。 允許的最大長度： {1}。                                                                                        |
| BaselineRuleRegTypeInvalidError          | 預期值 '{0}' 不符合定義的登錄值類型。                                                              |
| BaselineRulesetAdded                     | 在預設組態中找不到識別碼為 '{0}' 的規則集。 無法加入規則集。                                               |
| BaselineRulesetIdMustBeUnique            | 指定的基準規則集 '{0}' 必須是唯一的。                                                                                           |
| BaselineRulesetNotFound                  | 識別碼為 '{0}' 所設定的規則，並在指定的組態中找不到名稱 '{1}'。 無法刪除規則集。                                |
| BaselineRuleSourceNotMatch               | 已有定義識別碼為 '{0}' 的規則。                                                                                                       |
| BaselineRuleTypeDoesntMatch              | 預設規則類型為 '{0}'。                                                                                                              |
| BaselineRuleTypeDoesntMatchError         | 此規則的實際類型為: {0}，但 ruleType 屬性是： {1}。                                                                          |
| BaselineRuleUnpermittedChangesError      | 若要變更允許只有 'expectedValue' 和 'state' 的內容。                                                                       |
| BaselineTooManyRules                     | 允許自訂的規則數目上限為 {0} 規則。 指定的設定包含 {1} 規則。 （{2} 預設規則 + {3} 自訂規則。 |
| ErrorNoConfigurationStatus               | 找不到沒有組態狀態。 請狀態所需的組態狀態為 'Default' 或 'Custom'。                                    |
| ErrorNonEmptyRulesetOnDefault            | 設定狀態會設為預設值。 BaselineRulesets 清單必須是 null 或空白。                                                          |
| ErrorNullRulesetsPropertyOnCustom        | 指定的組態狀態為 'Custom'，但是 'baselineRulesets' 屬性為 null 或空白。                                             |
| ErrorParsingBaselineConfig               | 指定的設定不正確。 一或多個定義的值有 null 值或無效的類型。                                  |
| ErrorParsingIsDefaultProperty            | 指定 'configurationStatus' value '{0}' 無效。 值可以是只有 'Default' 或 'Custom'。                                         |
| InCompatibleViewVersion                  | 檢視版本： {0} 不支援這個工作區類型。                                                                                   |
| InvalidBaselineConfigurationGeneralError | 有一或多個型別驗證錯誤，找不到指定的基準組態。                                                          |
| ViewConversionError                      | 檢視是該工作區所支援的較舊版本。 檢視轉換失敗： {0}                                                                 |

如果您沒有足夠的權限，您可能會收到時發生一般錯誤，錯誤：

![](media/security-center-customize-os-security-config/general-failure-error.png)

## <a name="next-steps"></a>後續步驟
這篇文章會示範如何自訂在資訊安全中心的作業系統安全性組態評估。 若要了解設定規則及補救的詳細資訊，請參閱：

- [資訊安全中心常見的組態識別碼與基準規則](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。
- 資訊安全中心會使用 CCE (一般設定列舉) 指派設定規則的唯一識別碼。 如需詳細資訊，請造訪 [CCE](https://nvd.nist.gov/config/cce/index) 網站。
- [修復安全性設定](security-center-remediate-os-vulnerabilities.md)示範如何解決弱點，當作業系統設定不符合建議的安全性設定規則。
