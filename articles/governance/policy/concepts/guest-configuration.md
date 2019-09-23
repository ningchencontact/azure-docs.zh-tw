---
title: 瞭解如何審核電腦的內容
description: 瞭解 Azure 原則如何使用「來賓設定」來審核 Azure 機器內的設定。
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/20/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 733b7fd8ef5b302df754fc7299b420739baff153
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172472"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了[審查和修復](../how-to/remediate-resources.md)Azure 資源之外，Azure 原則可以在機器內進行設定。 此驗證會由「來賓設定」延伸模組和用戶端執行。 透過用戶端的延伸模組會驗證下列設定：

- 作業系統的設定
- 應用程式設定或目前狀態
- 環境設定

此時，Azure 原則來賓設定只會審核計算機內的設定。 它不會套用設定。

## <a name="extension-and-client"></a>延伸模組和用戶端

若要在電腦內審核設定，會啟用[虛擬機器擴充](../../../virtual-machines/extensions/overview.md)功能。 延伸模組會下載適用的原則指派及相對應的設定定義。

### <a name="limits-set-on-the-extension"></a>延伸模組上設定的限制

若要限制延伸，使其不會影響在機器內執行的應用程式，來賓設定不得超過 5% 的 CPU 使用率。 內建和自訂定義都有這項限制。

## <a name="register-guest-configuration-resource-provider"></a>註冊來賓設定資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 您可以透過入口網站或透過 PowerShell 註冊。 如果透過入口網站來指派來賓設定原則, 則會自動註冊資源提供者。

### <a name="registration---portal"></a>註冊 - 入口網站

若要透過 Azure 入口網站註冊「來賓設定」的資源提供者，請依照下列步驟進行操作：

1. 啟動 Azure 入口網站，然後按一下 [所有服務]。 搜尋並選取 [訂用帳戶]。

1. 尋找並按一下您想要啟用「來賓設定」的訂用帳戶。

1. 在 [訂用帳戶] 頁面的左側功能表中，按一下 [資源提供者]。

1. 篩選或捲動，直到找出 **Microsoft.GuestConfiguration** 為止，然後按一下同一列上的 [註冊]。

### <a name="registration---powershell"></a>註冊 - PowerShell

若要透過 PowerShell 註冊「來賓設定」的資源提供者，請執行下列命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>驗證工具

在機器內部，來賓設定用戶端會使用本機工具來執行 audit。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|注意|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 「來賓設定」延伸模組會安裝 Ruby 和 Python。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 一旦收到來賓指派，系統便會每隔 15 分鐘檢查一次設定。 稽核完成後，系統會立即將結果傳送給來賓設定資源提供者。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 此更新會導致 Azure 原則評估 Azure Resource Manager 屬性。 隨選 Azure 原則評估會從來賓設定資源提供者抓取最新的值。 不過，它不會在機器內觸發設定的新審核。

## <a name="supported-client-types"></a>支援的用戶端類型

下表顯示 Azure 映像上的支援作業系統清單：

|發行者|名稱|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 datacenter、2012 R2 Datacenter、2016 Datacenter、2019 Datacenter|
|Microsoft|Windows 用戶端|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 「來賓設定」可以審核執行受支援 OS 的節點。 如果您想要審核使用自訂映射的虛擬機器, 您需要複製**DeployIfNotExists**定義, 並修改**If**區段以包含您的映射屬性。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

任何版本都不支援 Windows Server Nano Server。

## <a name="guest-configuration-extension-network-requirements"></a>來賓設定擴充功能網路需求

若要與 Azure 中的來賓設定資源提供者通訊，機器需要在埠**443**上對 Azure 資料中心進行輸出存取。 如果您使用 Azure 中不允許輸出流量的私人虛擬網路，請使用[網路安全性群組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則來設定例外狀況。 Azure 原則來賓設定目前不存在服務標籤。

針對 [IP 位址清單], 您可以下載[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。 您只需要允許對部署 Vm 的區域中的 Ip 進行輸出存取。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 此檔案包含計算、SQL 和儲存體範圍。 每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

每個由來賓設定的 audit 執行都需要兩個原則定義： **DeployIfNotExists**定義和**AuditIfNotExists**定義。 **DeployIfNotExists**定義是用來以來賓設定代理程式和其他元件來準備機器，以支援[驗證工具](#validation-tools)。

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證電腦是否已獲指派要評估的設定。 如果目前沒有任何指派，請透過下列方式取得指派並準備機器：
  - 使用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)向電腦進行驗證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

如果**DeployIfNotExists**指派不符合規範, 則可以使用[補救](../how-to/remediate-resources.md#create-a-remediation-task)工作。

一旦**DeployIfNotExists**指派符合規範， **AuditIfNotExists**原則指派就會使用本機驗證工具來判斷設定指派是否符合規範。 驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性] 節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/getting-compliance-data.md)。

> [!NOTE]
> **AuditIfNotExists**原則需要**DeployIfNotExists**原則才會傳回結果。 若沒有**DeployIfNotExists**， **AuditIfNotExists**原則會顯示 "0 of 0" 資源作為狀態。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 名為 *[預覽] 的內建計劃：Linux 和 Windows 機器*內的 Audit Password security 設定包含18個原則。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **AuditIfNotExists**，針對 Linux 則有 3 組。 [原則定義](definition-structure.md#policy-rule)邏輯會驗證只會評估目標作業系統。

### <a name="multiple-assignments"></a>多個指派

來賓設定原則目前只支援針對每部電腦指派相同的來賓指派一次，即使原則指派使用不同的參數也一樣。

## <a name="built-in-resource-modules"></a>內建資源模組

安裝來賓設定擴充功能時，' GuestConfiguration ' PowerShell 模組隨附于最新版的 DSC 資源模組。 您可以使用模組頁面[GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/)的 [手動下載] 連結，從 PowerShell 資源庫下載此模組。
' Nupkg ' 檔案格式可以重新命名為 ' .zip '，以解壓縮和檢查。

## <a name="client-log-files"></a>用戶端記錄檔

「來賓設定」延伸模組會將記錄檔寫入下列位置:

Windows：`C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux：`/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

其中`<version>` , 是指目前的版本號碼。

### <a name="collecting-logs-remotely"></a>從遠端收集記錄檔

針對來賓設定設定或模組進行疑難排解的第一個步驟，應該是`Test-GuestConfigurationPackage`遵循[測試來賓設定套件](../how-to/guest-configuration-create.md#test-a-guest-configuration-package)中的步驟來使用 Cmdlet。
如果不成功，則收集用戶端記錄檔有助於診斷問題。

#### <a name="windows"></a>Windows

若要使用 Azure VM 執行命令功能來從 Windows 機器的記錄檔中捕獲資訊，下列 PowerShell 腳本範例可能很有説明。 如需詳細資訊，請參閱[在具有執行命令的 WINDOWS VM 中執行 PowerShell 腳本](../../../virtual-machines/windows/run-command.md)。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

若要使用 Azure VM 執行命令功能來從 Linux 機器的記錄檔中捕獲資訊，下列範例 Bash 腳本可能很有説明。 如需詳細資訊，請參閱[在 LINUX VM 中使用執行命令執行 shell 腳本](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>來賓設定範例

下列位置提供原則來賓設定的範例:

- [範例索引-來賓設定](../samples/index.md#guest-configuration)
- [Azure 原則 GitHub 存放庫範例](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md), 請參閱範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/getting-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/index.md)來檢閱何謂管理群組。