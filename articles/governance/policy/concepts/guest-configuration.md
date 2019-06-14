---
title: 了解如何稽核的虛擬機器內容
description: 了解「Azure 原則」如何使用「來賓設定」來稽核 Azure 虛擬機器內的設定。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c98229a28f31ff715f252dc3915ca690e99245ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65979520"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure 原則的來賓設定

除了稽核並[修復](../how-to/remediate-resources.md)Azure 資源，Azure 原則可稽核的虛擬機器內的設定。 此驗證會由「來賓設定」延伸模組和用戶端執行。 延伸模組會透過用戶端來驗證設定，例如作業系統設定、應用程式設定或目前狀態、環境設定等。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>延伸模組和用戶端

為了稽核虛擬機器內的設定，會啟用[虛擬機器延伸模組](../../../virtual-machines/extensions/overview.md)。 延伸模組會下載適用的原則指派及相對應的設定定義。

### <a name="register-guest-configuration-resource-provider"></a>註冊來賓設定資源提供者

您必須先註冊資源提供者，才能使用「來賓設定」。 您可以透過入口網站或透過 PowerShell 註冊。 如果您的客體設定原則指派透過入口網站時，會自動註冊資源提供者。

#### <a name="registration---portal"></a>註冊 - 入口網站

若要透過 Azure 入口網站註冊「來賓設定」的資源提供者，請依照下列步驟進行操作：

1. 啟動 Azure 入口網站，然後按一下 [所有服務]  。 搜尋並選取 [訂用帳戶]  。

1. 尋找並按一下您想要啟用「來賓設定」的訂用帳戶。

1. 在 [訂用帳戶]  頁面的左側功能表中，按一下 [資源提供者]  。

1. 篩選或捲動，直到找出 **Microsoft.GuestConfiguration** 為止，然後按一下同一列上的 [註冊]  。

#### <a name="registration---powershell"></a>註冊 - PowerShell

若要透過 PowerShell 註冊「來賓設定」的資源提供者，請執行下列命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>驗證工具

在虛擬機器內，「來賓設定」會使用本機工具來執行稽核。

下表顯示每個支援的作業系統上所使用的本機工具清單：

|作業系統|驗證工具|注意|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 「來賓設定」延伸模組會安裝 Ruby 和 Python。 |

### <a name="validation-frequency"></a>驗證頻率

「來賓設定」用戶端會每隔 5 分鐘檢查一次是否有新內容。 一旦收到來賓指派，系統便會每隔 15 分鐘檢查一次設定。 稽核完成後，系統會立即將結果傳送給來賓設定資源提供者。 發生原則[評估觸發程序](../how-to/get-compliance-data.md#evaluation-triggers)時，系統會將機器的狀態寫入到來賓設定資源提供者。 這會導致 Azure 原則評估 Azure Resource Manager 屬性。 隨 Azure 原則評估從來賓設定資源提供者擷取的最新的值。 不過，該評估不會對虛擬機器內的設定觸發新的稽核作業。

### <a name="supported-client-types"></a>支援的用戶端類型

下表顯示 Azure 映像上的支援作業系統清單：

|發行者|Name|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter、 2012 R2 Datacenter、 2016年資料中心、 2019年資料中心|
|Microsoft|Windows 用戶端|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Guest 設定可以稽核執行受支援的作業系統的節點。 如果您想要稽核使用自訂映像的虛擬機器，您需要重複**DeployIfNotExists**定義及修改**如果**節，以加入您的映像屬性。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

Windows Server Nano Server 不支援任何版本。

### <a name="guest-configuration-extension-network-requirements"></a>Guest 設定的擴充功能的網路需求

若要在 Azure 中的客體設定資源提供者與通訊，虛擬機器需要對外存取連接埠上的 Azure 資料中心**443**。 如果您在 Azure 中使用的私人虛擬網路，並不允許輸出流量，必須使用設定的例外狀況[網路安全性群組](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則。 此時，服務標籤不存在 Azure 原則客體組態。

如需 IP 位址清單，您可以下載[Microsoft Azure Datacenter IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並具有目前已部署的範圍及任何即將進行的 IP 範圍變更。 您只需要允許輸出存取您的 Vm 部署所在的區域中的 Ip。

> [!NOTE]
> Azure 資料中心 IP 位址 XML 檔會列出在 Microsoft Azure 資料中心使用的 IP 位址範圍。 此檔案包含計算、SQL 和儲存體範圍。 每週會公佈已更新的檔案。 檔案會反映目前已部署的範圍及任何即將進行的 IP 範圍變更。 出現在檔案中的新範圍至少有一週的時間不會在資料中心中使用。 最好是每週下載新的 XML 檔案。 接著，更新您的網站以便正確地識別在 Azure 中執行的服務。 Azure ExpressRoute 使用者應該注意到，在每個月的第一週，此檔案會用來更新 Azure 空間的邊界閘道協定 (BGP) 公告。

## <a name="guest-configuration-definition-requirements"></a>來賓設定定義需求

每個來賓組態所執行的稽核需要兩個原則定義， **DeployIfNotExists**定義並**稽核**定義。 **DeployIfNotExists**定義會用來準備虛擬機器的客體設定代理程式和其他元件，以支援[驗證工具](#validation-tools)。

**DeployIfNotExists** 原則定義會驗證並修正下列項目：

- 驗證虛擬機器已獲指派將要評估的設定。 如果目前沒有任何指派，請透過下列方式取得指派並備妥虛擬機器：
  - 使用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)向虛擬機器進行驗證
  - 安裝最新版的 **Microsoft.GuestConfiguration** 延伸模組
  - 安裝[驗證工具](#validation-tools)和相依性 (如有需要)

如果**DeployIfNotExists**指派為不符合規範，[補救工作](../how-to/remediate-resources.md#create-a-remediation-task)可用。

一次**DeployIfNotExists**指派為符合規範，**稽核**原則指派是使用本機驗證工具來判斷是否符合規範或不符合規範設定指派。
驗證工具會將結果提供給「來賓設定」用戶端。 用戶端會將結果轉送至「來賓延伸模組」，以便透過「來賓設定」資源提供者提供結果。

「Azure 原則」會使用「來賓設定」資源提供者 **complianceStatus** 屬性在 [合規性]  節點中回報合規性。 如需詳細資訊，請參閱[取得合規性資料](../how-to/getting-compliance-data.md)。

> [!NOTE]
> 每個「來賓設定」定義都必須有 **DeployIfNotExists** 和 **Audit** 原則定義。

「來賓設定」的所有內建原則都包含在一個方案中，以聚集要在指派中使用的定義。 名為 *[預覽] 的內建計劃：Linux 及 Windows 虛擬機器內的「稽核密碼」安全性設定*包含 18 項原則。 針對 Windows 有 6 組 **DeployIfNotExists** 和 **Audit**，針對 Linux 則有 3 組。 在每個案例中，定義內的邏輯僅會驗證依據[原則規則](definition-structure.md#policy-rule)定義進行評估的目標作業系統。

## <a name="client-log-files"></a>用戶端記錄檔

Guest 設定的延伸模組會將記錄檔寫入下列位置：

Windows：`C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux：`/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

其中`<version>`指的是目前的版本號碼。

## <a name="guest-configuration-samples"></a>Guest 設定範例

針對原則 Guest 設定的範例可在下列位置：

- [範例索引-來賓設定](../samples/index.md#guest-configuration)
- [Azure 原則範例的 GitHub 存放庫](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)。

## <a name="next-steps"></a>後續步驟

- 檢閱範例[「 Azure 原則範例](../samples/index.md)。
- 檢閱 [Azure 原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/getting-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/index.md)來檢閱何謂管理群組。