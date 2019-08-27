---
title: 瞭解如何審核虛擬機器的內容
description: 了解「Azure 原則」如何使用「來賓設定」來稽核 Azure 虛擬機器內的設定。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 18a85fae7d2d241bd8d582db73c71e1d1472f04d
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036309"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了對 Azure 資源進行審核和[修復](../how-to/remediate-resources.md), Azure 原則可以在虛擬機器內進行設定。 此驗證會由「來賓設定」延伸模組和用戶端執行。 延伸模組會透過用戶端來驗證設定，例如作業系統設定、應用程式設定或目前狀態、環境設定等。

此時, Azure 原則來賓設定只會在機器內部執行設定的審核。
尚未可以套用設定。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>延伸模組和用戶端

為了稽核虛擬機器內的設定，會啟用[虛擬機器延伸模組](../../../virtual-machines/extensions/overview.md)。 延伸模組會下載適用的原則指派及相對應的設定定義。

### <a name="limits-set-on-the-exension"></a>在 exension 上設定的限制

為了限制延伸, 使其不會影響在機器內執行的應用程式, 不允許來賓設定超過 5% 的 CPU 使用率。
這適用于由 Microsoft 提供作為「內建」的設定, 以及客戶所撰寫之自訂設定的實際 boh。

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

在虛擬機器內，「來賓設定」會使用本機工具來執行稽核。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|注意|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 「來賓設定」延伸模組會安裝 Ruby 和 Python。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 一旦收到來賓指派，系統便會每隔 15 分鐘檢查一次設定。 稽核完成後，系統會立即將結果傳送給來賓設定資源提供者。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 這會導致 Azure 原則評估 Azure Resource Manager 屬性。 隨選 Azure 原則評估會從來賓設定資源提供者抓取最新的值。 不過，該評估不會對虛擬機器內的設定觸發新的稽核作業。

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

若要與 Azure 中的來賓設定資源提供者進行通訊, 虛擬機器需要在埠**443**上對 Azure 資料中心進行輸出存取。 如果您在 Azure 中使用私人虛擬網路, 但不允許輸出流量, 則必須使用[網路安全性群組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則來設定例外狀況。 目前, Azure 原則來賓設定的服務標記不存在。

針對 [IP 位址清單], 您可以下載[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。 您只需要允許對部署 Vm 的區域中的 Ip 進行輸出存取。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 此檔案包含計算、SQL 和儲存體範圍。 每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

每個由來賓設定的 audit 執行都需要兩個原則定義: **DeployIfNotExists**定義和**AuditIfNotExists**定義。 **DeployIfNotExists**定義是用來準備具有來賓設定代理程式的虛擬機器, 以及支援[驗證工具](#validation-tools)的其他元件。

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證虛擬機器已獲指派將要評估的設定。 如果目前沒有任何指派，請透過下列方式取得指派並備妥虛擬機器：
  - 使用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)向虛擬機器進行驗證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

如果**DeployIfNotExists**指派不符合規範, 則可以使用[補救](../how-to/remediate-resources.md#create-a-remediation-task)工作。

一旦**DeployIfNotExists**指派符合規範, **AuditIfNotExists**原則指派就會使用本機驗證工具來判斷設定指派是否符合規範。
驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性] 節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/getting-compliance-data.md)。

> [!NOTE]
> **AuditIfNotExists**原則需要**DeployIfNotExists**原則才會傳回結果。
> 若沒有**DeployIfNotExists**, **AuditIfNotExists**原則會顯示 "0 of 0" 資源作為狀態。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 名為 *[預覽] 的內建計劃：Linux 及 Windows 虛擬機器內的「稽核密碼」安全性設定*包含 18 項原則。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **AuditIfNotExists**，針對 Linux 則有 3 組。 在每個案例中，定義內的邏輯僅會驗證依據[原則規則](definition-structure.md#policy-rule)定義進行評估的目標作業系統。

### <a name="multiple-assignments"></a>多個指派

來賓設定原則目前只支援為每個虛擬機器指派相同的來賓指派一次, 即使原則指派使用不同的參數也一樣。

## <a name="client-log-files"></a>用戶端記錄檔

「來賓設定」延伸模組會將記錄檔寫入下列位置:

Windows：`C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux：`/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

其中`<version>` , 是指目前的版本號碼。

## <a name="guest-configuration-samples"></a>來賓設定範例

下列位置提供原則來賓設定的範例:

- [範例索引-來賓設定](../samples/index.md#guest-configuration)
- [Azure 原則 GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)存放庫範例。

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md), 請參閱範例。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/getting-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/index.md)來檢閱何謂管理群組。
