---
title: 受控服務識別 (MSI) 常見問題和已知問題 (Azure Active Directory)
description: 受控服務識別已知問題 (Azure Active Directory)
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: 552f9e7cae4d7f46ea1548cfe7d9482bff79e5bc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930981"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>受控服務識別 (MSI) 常見問題和已知問題 (Azure Active Directory)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI 是否使用 Azure 雲端服務？

否，沒有在 Azure 雲端服務中支援 MSI 的計劃。

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI 可搭配 Directory Authentication Library (ADAL) 或 Microsoft Authentication Library (MSAL) 使用嗎？

不行，MSI 未尚未與 ADAL 或 MSAL 整合。 如需使用 MSI REST 端點取得 MSI 權杖的詳細資訊，請參閱[如何使用 Azure VM 受控服務識別 (MSI) 取得權杖](how-to-use-vm-token.md)。

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>什麼是受控服務識別的安全性界限？

身分識別的安全性界限，是指身分識別所連結到的資源。 例如，虛擬機器 MSI 安全性界限就是虛擬機器。 在該 VM 上執行的任何程式碼，都能呼叫 MSI 端點並要求權杖。 它是與支援 MSI 其他資源類似的體驗。

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>我應該使用 MSI VM IMDS 端點還是 MSI VM 延伸模組端點？

若搭配使用 MSI 與 VM，建議您使用 MSI IMDS 端點。 Azure 執行個體中繼資料服務是透過 Azure Resource Manager建立之所有 IaaS VM 可存取的 REST 端點。 使用 MSI 勝過使用 IMDS 的一些優點包括：

1. 所有 Azure IaaS 支援的作業系統皆可使用 MSI，勝過使用 IMDS。 
2. 不再需要為了啟用 MSI 而在 VM 上安裝延伸模組。 
3. VM 中不會再有 MSI 所使用的憑證。 
4. IMDS 端點是眾所周知的非可路由 IP 位址，且只能從 VM 內取得。 

MSI VM 延伸模組目前仍可供使用；但往後，我們會預設為使用 IMDS 端點。 MSI VM 延伸模組很快就會啟動淘汰計劃。 

如需 Azure 執行個體中繼資料服務的詳細資訊，請參閱 [IMDS 文件](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>支援的 Linux 散發套件有哪些？

Azure IaaS 所支援的所有 Linux 散發套件都可以透過 IMDS 端點與 MSI 搭配使用。 

注意：MSI VM 延伸模組僅支援下列 Linux 散發套件：
- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

其他 Linux 散發套件目前不受支援，且在不支援的散發套件上擴充功能可能會失敗。

擴充功能在 CentOS 6.9 上可以運作。 不過，由於在 6.9 中缺乏系統支援，因此若當機或停止，擴充功能將不會自動重新啟動。 在虛擬機器重新啟動時，它才會跟著重新啟動。 若要手動重新啟動擴充功能，請參閱[如何重新啟動 MSI 擴充功能？](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>如何重新啟動 MSI 擴充功能？
在特定版本的 Windows 和 Linux 上，擴充功能停止時，可手動用下列 Cmdlet 來重新啟動：

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- 適用於 Windows 的擴充功能名稱和類型是：ManagedIdentityExtensionForWindows
- 適用於 Linux 的擴充功能名稱和類型是：ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>已知問題

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>嘗試匯出 MSI 擴充的結構描述時，「自動化指令碼」失敗

在虛擬機器上啟用受控服務識別後，嘗試對虛擬機器或其資源群組使用「自動化指令碼」功能時，出現下列錯誤：

![MSI 自動化指令碼匯出錯誤](../media/msi-known-issues/automation-script-export-error.png)

「受控服務識別」虛擬機器擴充目前不支援將其結構描述匯出至資源群組範本。 因此，產生的範本不會顯示可在資源上啟用受控服務識別的設定參數。 您可以依照[使用範本來設定虛擬機器受控服務識別](qs-configure-template-windows-vm.md)中的範例，手動新增這些區段。

當結構描述匯出功能變成可用於 MSI 虛擬機器擴充時，就會列在[匯出包含虛擬機器擴充的資源群組](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)中。

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure 入口網站中沒出現組態刀鋒視窗

如果 VM 組態刀鋒視窗沒有出現在您的 VM 上，則表示 MSI 尚未在您區域的入口網站中啟用。  請稍後再試。  您也可以使用 [PowerShell](qs-configure-powershell-windows-vm.md) 或 [Azure CLI](qs-configure-cli-windows-vm.md)來啟用 VM 的 MSI。

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>在 [存取控制 (IAM)] 刀鋒視窗中無法將存取權指派給虛擬機器

在 Azure 入口網站中，如果**虛擬機器**沒有在 [存取控制 (IAM)] >  [新增權限] 中顯示為**指派存取權的對象**，則表示受控服務識別尚未在您區域的入口網站中啟用。 請稍後再試。  您仍然可藉由搜尋受控服務識別服務主體，選取 MSI 來指派角色。  在 [選取] 欄位中輸入 VM 名稱，服務主體就會出現在搜尋結果中。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM 從資源群組或訂用帳戶移走後會無法啟動

如果 VM 在執行期間遭到移動，VM 會在移動時繼續執行。 不過，移動之後，如果 VM 停止並重新啟動，則會無法啟動。 此問題是因為 VM 不會更新 MSI 身分識別的參考值，而持續指向舊資源群組中的參考值所致。

**因應措施** 
 
觸發 VM 上的更新，如此一來 VM 就可以為 MSI 取得正確的值。 您可以執行 VM 屬性變更，更新 MSI 身分識別的參考值。 例如，您可以使用下列命令在 VM 上設定新的標記值：

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令會在 VM 上設定值為 1 的新標記 "fixVM"。 
 
設定此屬性後，VM 會使用正確的 MSI 資源 URI 進行更新，然後您應該就可以啟動 VM 了。 
 
一旦啟動 VM 後，您可以使用下列命令移除標記：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>使用者指派身分識別的已知問題

- 使用者指派身分識別的指派僅適用於 VM 和 VMSS。 重要：使用者指派身分識別的指派將會在之後幾個月進行變更。
- 相同 VM/VMSS 上若有重複的使用者指派身分識別，將導致 VM/VMSS 失敗。 這包括以大小寫不同而新增的身分識別。 例如 MyUserAssignedIdentity 和 myuserassignedidentity。 
- 將 VM 延伸模組佈建到 VM 的作業，可能會因為 DNS 查閱失敗而失敗。 重新啟動 VM，然後再試一次。 
- 新增「不存在」的使用者指派身分識別會造成 VM 失敗。 
- 不支援在名稱中使用特殊字元 (例如底線) 建立使用者指派的身分識別。
- 針對端對端案例，使用者指派身分識別的名稱僅限 24 個字元。 使用者指派身分識別的名稱若超過 24 個字元，將會無法進行指派。  
- 新增第二個使用者指派的身分識別時，可能無法使用 clientID 來要求 VM 擴充的權杖。 使用下列兩個 bash 命令重新啟動 MSI VM 延伸模組來作為風險降低措施：
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- 當 VM 具有使用者指派的身分識別，但是沒有系統指派的身分識別時，入口網站 UI 會顯示 MSI 為已停用狀態。 若要啟用系統指派的身分識別，請使用 Azure Resource Manager 範本、Azure CLI 或 SDK。
