---
title: Windows 的 Azure 自訂指令碼延伸模組 | Microsoft Docs
description: 使用「自訂指令碼擴充功能」來自動執行 Windows VM 組態工作
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: gwallace
ms.openlocfilehash: b71ba69bcf4965ea607e097c392573e77aab6865
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408280"
---
# <a name="custom-script-extension-for-windows"></a>Windows 的自訂指令碼延伸模組

「自訂指令碼擴充功能」會在 Azure 虛擬機器上下載並執行指令碼。 此延伸模組可用於部署後組態、 軟體安裝，或任何其他組態或管理工作項目。 您可以從 Azure 儲存體或 GitHub 下載指令碼，或是在擴充功能執行階段將指令碼提供給 Azure 入口網站。 自訂指令碼擴充功能與 Azure Resource Manager 範本整合，並可使用 Azure CLI、 PowerShell、 Azure 入口網站或 Azure 虛擬機器 REST API 來執行。

本文件詳細說明如何透過 Azure PowerShell 模組、Azure Resource Manager 範本使用自訂指令碼擴充功能，同時也詳細說明 Windows 系統上的疑難排解步驟。

## <a name="prerequisites"></a>必要條件

> [!NOTE]  
> 請勿使用自訂指令碼擴充功能以相同的 VM 執行 Update-AzVM 作為其參數，因為它會等候其本身。  

### <a name="operating-system"></a>作業系統

自訂指令碼擴充功能的 Windows 會執行於延伸模組支援擴充 OSs，如需詳細資訊，請參閱這[Azure 延伸模組支援的作業系統](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)。

### <a name="script-location"></a>指令碼位置

您可以設定要使用您的 Azure Blob 儲存體認證存取 Azure Blob 儲存體的擴充功能。 指令碼位置可以是任何地方，只要 VM 可以路由傳送至結束點，例如 GitHub 或內部的檔案伺服器。

### <a name="internet-connectivity"></a>網際網路連線

如果您需要下載指令碼外部例如從 GitHub 或 Azure 儲存體，則其他的防火牆和網路安全性群組連接埠需要開啟。 例如，如果您的指令碼位於 Azure 儲存體中，您可以允許存取使用 Azure NSG 服務標記[儲存體](../../virtual-network/security-overview.md#service-tags)。

如果您的指令碼是在本機伺服器上，則您仍可能需要額外的防火牆和網路安全性群組的連接埠必須開啟。

### <a name="tips-and-tricks"></a>秘訣與技巧

* 此延伸模組的最高失敗率是因為指令碼，而沒有錯誤，執行指令碼的測試中的語法錯誤，並也將放在其他登入指令碼來輕鬆地找到失敗的地方。
* 撰寫指令碼具有等冪性。 這可確保，如果不小心重新執行，就不會造成系統變更。
* 請確定在指令碼執行時，不需要使用者輸入。
* 指令碼可執行的時間為 90 分鐘。若超過這個時間，將會導致擴充功能佈建失敗。
* 不要在指令碼中放置重新開機，此動作會導致正在安裝的其他擴充功能發生問題。 若發佈重新開機，擴充功能將不會在重新啟動之後繼續執行。
* 如果您有會導致重新開機，然後安裝應用程式並執行指令碼的指令碼時，您可以排程使用 Windows 排程工作，重新開機，或使用 DSC、 Chef 或 Puppet 的擴充功能之類的工具。
* 擴充功能只會執行指令碼一次。如果您想要在每次開機時執行指令碼，則必須使用擴充功能建立 Windows 排程工作。
* 如果您想要排程指令碼的執行時間，則應該使用擴充功能來建立 Windows 排程工作。
* 當指令碼正在執行時，只能從 Azure 入口網站或 CLI 看到「正在轉換」擴充功能狀態。 如果您需要執行中指令碼更頻繁的狀態更新，便必須建立自己的解決方案。
* 自訂指令碼擴充功能未原生支援 Proxy 伺服器，但是您可以在指令碼中使用支援 Proxy 伺服器的檔案傳輸工具，例如 *Curl*
* 請留意指令碼或命令所依賴的非預設目錄位置是否具備處理此情形的邏輯。

## <a name="extension-schema"></a>擴充功能結構描述

「自訂指令碼擴充功能」組態會指定指令碼位置和要執行命令等項目。 您可將此組態儲存在組態檔中、在命令列中指定組態，或在 Azure Resource Manager 範本中指定組態。

您可將敏感性資料儲存在受保護的組態中，此組態會經過加密，並且只會在虛擬機器內解密。 當執行命令包含機密資料 (例如密碼) 時，受保護的組態會相當有用。

這些項目應被視為敏感性資料，並在擴充功能保護的設定組態中指定。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> 只有一個版本的延伸模組可以安裝在點上的 VM 上的時間，指定自訂指令碼兩次相同的 Resource Manager 範本，針對相同的 VM 將會失敗。

### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (例如) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (範例) | 123456789 | 32 位元整數 |
| commandToExecute (例如) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (例如) | examplestorageacct | string |
| storageAccountKey (例如) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>這些屬性名稱會區分大小寫。 為了避免發生部署問題，請使用如下所示的名稱。

#### <a name="property-value-details"></a>屬性值詳細資料

* `commandToExecute`：(**必要**，字串) 要執行的進入點指令碼。 如果您的命令包含機密資料 (例如密碼)，或您的 fileUris 是敏感資料，請改用此欄位。
* `fileUris`：(選擇性，字串陣列) 要下載之檔案的 URL。
* `timestamp` (選擇性，32 位元整數) 只有在透過變更此欄位的值來觸發指令碼的重新執行時，才需使用此欄位。  任何整數值都是可接受的；只要與先前的值不同即可。
* `storageAccountName`：(選用，字串) 儲存體帳戶的名稱。 如果您指定儲存體證明資料，則所有 `fileUris` 都必須是 Azure Blob 的 URL。
* `storageAccountKey`：(選用，字串) 儲存體帳戶的存取金鑰

下列值可以在公開或受保護的設定中設定，擴充功能將會拒絕任何同時在公開和受保護的設定中設定下列值的組態。

* `commandToExecute`

使用偵錯，但它可能很有用的公用設定，建議您使用受保護的設定。

公開設定會以純文字的格式，傳送到執行指令碼所在的 VM。  受保護的設定會使用只有 Azure 和 VM 知道的金鑰來加密。 這些設定會儲存至 VM，因為它們已傳送，也就是，如果設定已加密儲存加密的 VM 上。 用來解密加密值的憑證會儲存在 VM 上，並在執行階段用於解密設定 (如有必要)。

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 JSON 結構描述，如上一節會詳細可以用於 Azure Resource Manager 範本，若要在部署期間執行自訂指令碼擴充功能。 下列範例會說明如何使用自訂指令碼擴充功能：

* [教學課程：使用 Azure Resource Manager 範本部署虛擬機器擴充功能](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [在 Windows 和 Azure SQL DB 上部署兩層式應用程式](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzVMCustomScriptExtension` 命令可以用來將自訂指令碼擴充功能新增至現有的虛擬機器。 如需詳細資訊，請參閱 [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension)。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>其他範例

### <a name="using-multiple-scripts"></a>使用多個指令碼

在此範例中，您會有三個指令碼，用來建置您的伺服器。 **CommandToExecute**呼叫第一個指令碼，則您可以在其他則稱為選項。 例如，您可以控制執行並使用正確的錯誤處理、 記錄和狀態管理對主要指令碼。 指令碼會下載到本機電腦執行。 秷濆 菾`1_Add_Tools.ps1`您可以呼叫`2_Add_Features.ps1`加上`.\2_Add_Features.ps1`指令碼，並重複此程序的其他指令碼您定義在`$settings`。

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>從本機共用執行指令碼

在此範例中，您可能想要使用本機的 SMB 伺服器指令碼位置。 如此一來，您不需要提供任何其他設定，除了**commandToExecute**。

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>如何使用 CLI 多次執行自訂指令碼

如果您想要執行自訂指令碼延伸模組多次，您只有在下列情況下才能執行此動作：

* 延伸模組**名稱**參數等同於先前的部署擴充功能。
* 更新的設定，否則不會重新執行命令。 您可以將動態屬性加入命令，例如時間戳記。

或者，您可以設定[ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag)屬性設 **，則為 true**。

### <a name="using-invoke-webrequest"></a>使用 Invoke-webrequest

如果您使用[Invoke-webrequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest)在您的指令碼中，您必須指定參數`-UseBasicParsing`或其他您會收到下列錯誤檢查的詳細的狀態時：

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>傳統 VM

若要部署在傳統 Vm 上的自訂指令碼擴充功能，您可以使用 Azure 入口網站或傳統的 Azure PowerShell cmdlet。

### <a name="azure-portal"></a>Azure 入口網站

瀏覽至您的傳統 VM 資源。 選取 **延伸模組**下方**設定**。

按一下  **+ 新增**，然後在資源清單中選擇 **自訂指令碼擴充功能**。

在 **安裝擴充功能**頁面上選取本機的 PowerShell 檔案，並填寫任何引數，然後按一下**確定**。

### <a name="powershell"></a>PowerShell

使用[組 AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet 可以用來將自訂指令碼擴充功能新增至現有的虛擬機器。

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell 模組，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請執行下列命令：

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

延伸模組的輸出會記錄至目標虛擬機器上，找到下列資料夾的檔案。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定的檔案會下載至目標虛擬機器上的下列資料夾中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

其中 `<n>` 是十進位整數，可能會在擴充功能的執行之間變更。  `1.*` 值符合擴充功能目前實際的 `typeHandlerVersion` 值。  例如，實際的目錄可能是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`。  

執行 `commandToExecute` 命令時，擴充功能會將此目錄 (例如 `...\Downloads\2`) 設定為目前的工作目錄。 此程序可讓您使用相對路徑找出透過 `fileURIs` 屬性下載的檔案位置。 如需範例，請參閱下表。

由於絕對下載路徑會隨時間而改變，最好盡可能在 `commandToExecute` 字串中選擇相對的指令碼/檔案路徑。 例如:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

透過下載的檔案會保留第一個 URI 區段後的路徑資訊`fileUris`屬性清單。  如下表所示，下載的檔案會對應到下載的子目錄，以反映 `fileUris` 值結構。  

#### <a name="examples-of-downloaded-files"></a>下載檔案的範例

| fileUris 中的 URI | 相對下載位置 | 絕對下載位置<sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup>絕對目錄路徑變更的 VM，但不是會在 CustomScript 擴充功能的單一執行的存留期。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
