---
title: 停止使用受控身分識別的 VM 擴充功能，並開始使用 Azure 執行個體中繼資料服務端點
description: 若要停止使用 VM 延伸模組，並開始使用 Azure 執行個體中繼資料服務 (IMDS) 進行驗證的逐步指示的步驟。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 6ee8891eae108256875660cc3f2256b65703a1aa
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406775"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>如何停止使用虛擬機器管理身分識別擴充功能並開始使用 Azure 執行個體中繼資料服務

## <a name="virtual-machine-extension-for-managed-identities"></a>受管理的身分識別的虛擬機器擴充功能

受管理的身分識別的虛擬機器擴充功能用來要求內虛擬機器的受管理身分識別的權杖。 工作流程包含下列步驟：

1. 首先，工作負載的資源內呼叫的本機端點`http://localhost/oauth2/token`來要求存取權杖。
2. 虛擬機器擴充功能接著會使用認證的受管理的身分識別，向 Azure AD 要求存取權杖... 
3. 存取權杖傳回給呼叫者，並可用來向支援 Azure AD 驗證，例如 Azure 金鑰保存庫或 Azure 儲存體的服務。

下一節中所述的幾項限制，因為受管理的身分識別的 VM 擴充功能已被取代改為使用對等的端點在 Azure 執行個體中繼資料服務 (IMDS)

### <a name="provision-the-extension"></a>佈建擴充功能 

當您設定虛擬機器或虛擬機器擴展集有受管理的身分識別時，您可以選擇性地選擇佈建 Azure 資源的 VM 延伸模組使用的受管理的身分識別`-Type`參數上的[設定 AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet。 您可以傳遞`ManagedIdentityExtensionForWindows`或是`ManagedIdentityExtensionForLinux`，視虛擬機器的類型，並命名它使用`-Name`參數。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

您也可以使用 Azure Resource Manager 部署範本來佈建的 VM 擴充功能中，加入以下 JSON`resources`範本區段 (使用`ManagedIdentityExtensionForLinux`的 Linux 版本的名稱和型別項目)。

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
如果您正在使用虛擬機器擴展集，您也可以佈建受管理的身分識別延伸模組使用的 Azure 資源的虛擬機器擴展集[新增 AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet。 您可以傳遞`ManagedIdentityExtensionForWindows`或是`ManagedIdentityExtensionForLinux`、 根據類型的虛擬機器擴展集，並將它命名使用`-Name`參數。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
佈建虛擬機器擴展集延伸模組與 Azure Resource Manager 部署範本中，新增以下 JSON`extensionpProfile`範本區段 (使用`ManagedIdentityExtensionForLinux`的 Linux 版本的名稱和型別項目)。

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

佈建的虛擬機器擴充功能可能會因為 DNS 查閱失敗。 如果發生這種情況，重新啟動虛擬機器，並再試一次。 

### <a name="remove-the-extension"></a>移除擴充功能 
若要移除擴充功能，請使用`-n ManagedIdentityExtensionForWindows`或`-n ManagedIdentityExtensionForLinux`切換 （取決於虛擬機器類型），含[az vm 擴充功能刪除](https://docs.microsoft.com/cli/azure/vm/)，或[az vmss 擴充功能刪除](https://docs.microsoft.com/cli/azure/vmss)虛擬機器擴展設定使用 Azure CLI 或`Remove-AzVMExtension`適用於 Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>使用虛擬機器擴充功能取得權杖

以下是使用 VM 延伸模組端點的 Azure 資源管理的身分識別的範例要求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 元素 | 說明 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `http://localhost:50342/oauth2/token` | Azure 資源受控識別端點，其中 50342 是預設連接埠且可設定。 |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 也會出現在所核發權杖的 `aud` (對象) 宣告中。 此範例會要求用來存取 Azure Resource Manager 的權杖，其中包含應用程式識別碼 URI https://management.azure.com/。 |
| `Metadata` | HTTP 要求標頭欄位，Azure 資源受控識別需要此元素以減輕伺服器端偽造要求 (SSRF) 攻擊。 此值必須設定為 "true" (全部小寫)。|
| `object_id` | (選擇性) 查詢字串參數，指出要使用權杖的受控識別 object_id。 如果您的 VM 有多個使用者指派的受控識別，這會是必要項目。|
| `client_id` | (選擇性) 查詢字串參數，指出要使用權杖的受控識別 client_id。 如果您的 VM 有多個使用者指派的受控識別，這會是必要項目。|


範例回應：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 元素 | 說明 |
| ------- | ----------- |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `refresh_token` | 並未由 Azure 資源受控識別使用。 |
| `expires_in` | 存取權杖從發行到過期之前持續有效的秒數。 在權杖的 `iat` 宣告中可找到發行時間。 |
| `expires_on` | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。 |
| `not_before` | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。 |
| `resource` | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。 |
| `token_type` | 權杖的類型，即「持有人」存取權杖，表示此權杖的持有人可以存取資源。 |


### <a name="troubleshoot-the-virtual-machine-extension"></a>虛擬機器擴充功能進行疑難排解 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>在失敗之後重新啟動虛擬機器擴充功能

在特定版本的 Windows 和 Linux 上，擴充功能停止時，可手動用下列 Cmdlet 來重新啟動：

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- 擴充功能名稱和 Windows 的類型是： `ManagedIdentityExtensionForWindows`
- 延伸模組名稱和適用於 Linux 的類型是： `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>嘗試使用 Azure 資源適用受控識別擴充功能的結構描述匯出時，「自動化指令碼」會失敗

在虛擬機器上啟用適用於 Azure 資源管理的身分識別時，嘗試使用 「 自動化指令碼 」 功能的虛擬機器或其資源群組時，會顯示下列錯誤：

![Azure 資源適用受控識別自動化指令碼匯出錯誤](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Azure 資源的虛擬機器擴充功能管理的身分識別目前不支援對資源群組範本匯出其結構描述的能力。 因此，產生的範本不會顯示可在資源上啟用 Azure 資源適用受控識別的設定參數。 可以手動新增這些區段，依照下列中的範例[設定受管理身分識別，使用範本在 Azure 虛擬機器上的 Azure 資源的](qs-configure-template-windows-vm.md)。

當結構描述匯出功能變成可供 Azure 資源 （在 2019 年 1 月的淘汰已計劃） 的虛擬機器擴充功能管理的身分識別時，它將會列在[匯出資源群組包含 VM 擴充功能](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>虛擬機器擴充功能的限制 

有幾項主要的限制，若要使用的虛擬機器擴充功能。 

 * 最嚴重的限制是用來要求權杖的認證會儲存在虛擬機器上。 已成功破壞虛擬機器的攻擊者可以竊取認證。 
 * 此外，虛擬機器擴充功能是仍支援數個 Linux 發行版本中，使用大量的開發成本來修改、 建置及測試上每個這些散發套件的延伸模組。 目前，只有下列的 Linux 散發套件支援： 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 沒有要管理的身分識別，以部署虛擬機器的效能造成影響，因為虛擬機器擴充功能也必須將他們佈建。 
 * 最後，將虛擬機器擴充功能可以只支援 32 使用者指派給受控身分識別每個虛擬機器中。 

## <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務

[Azure 執行個體中繼資料服務 (IMDS)](/azure/virtual-machines/windows/instance-metadata-service)是 REST 端點，提供執行可用來管理和設定您的虛擬機器的虛擬機器執行個體的相關資訊。 端點是可在已知的非可路由 IP 位址 (`169.254.169.254`) 可以存取的只能從虛擬機器內。

有一些使用 Azure IMDS 來要求權杖的數個優點。 

1. 虛擬機器的外部服務，因此已不再存在於虛擬機器上使用受管理的身分識別的認證。 相反地，它們會裝載，並在主機上的 Azure 虛擬機器保護。   
2. 所有 Windows 和 Linux 作業系統支援 Azure IaaS 上可以使用受管理的身分識別。
3. 因為 VM 延伸模組不需要再將他們佈建，部署會是更快速且更容易。
4. 使用 IMDS 端點，最多 1000 個使用者指派的受控識別可指派給單一虛擬機器。
5. 沒有任何重大的變更，而不使用虛擬機器擴充功能使用 IMDS 的要求，因此它是相當容易的連接埠目前使用的虛擬機器擴充功能的現有部署。

基於這些理由，一旦將虛擬機器擴充功能已被取代 Azure IMDS 服務時，將會成為方式，來要求權杖。 


## <a name="next-steps"></a>後續步驟

* [如何使用適用於 Azure 的虛擬機器上的 Azure 資源的受管理的身分識別取得存取權杖](how-to-use-vm-token.md)
* [Azure 執行個體中繼資料服務](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
