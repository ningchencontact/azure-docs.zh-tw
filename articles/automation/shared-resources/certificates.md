---
title: Azure 自動化中的憑證資產
description: 憑證是安全地在 Azure 自動化中，使得 runbook 或 DSC 組態，以向 Azure 和協力廠商資源存取。  這篇文章說明憑證的詳細資料，以及如何以文字和圖形化編寫形式加以使用。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ab8a231d4d44cfe2bfaf7c003ca57772a7a1814
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896305"
---
# <a name="certificate-assets-in-azure-automation"></a>Azure 自動化中的憑證資產

憑證會安全地儲存在 Azure 自動化中，它們可以存取 runbook 或 DSC 組態使用**Get-azurermautomationcertificate** Azure Resource Manager 資源的活動。 這個功能可讓您建立使用憑證進行驗證的 Runbook 和 DSC 設定，或將它們新增至 Azure 或協力廠商資源。

>[!NOTE]
>Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此金鑰會儲存在系統管理的 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。 此程序是由 Azure 自動化所管理。

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell Cmdlet

針對 AzureRM，下表中的 Cmdlet 可透過 Windows PowerShell 來建立和管理自動化認證資產。 可在自动化 Runbook 和 DSC 配置中使用的 [AzureRM.Automation 模块](/powershell/azure/overview)已随附了这些 cmdlet。

|Cmdlet|描述|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|擷取要在 Runbook 或 DSC 組態中使用的憑證相關資訊。 您只能從 Get-AutomationCertificate 活動擷取憑證本身。|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|建立新的憑證到 Azure 自動化。|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|從 Azure 自動化中移除憑證。|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|設定現有的憑證，包括上傳憑證檔案和設定 .pfx 的密碼屬性。|
|[Add-AzureCertificate](/previous-versions/azure/dn495214)|上傳指定雲端服務的服務憑證。|

## <a name="activities"></a>活動

下表中的活動是用來存取 Runbook 和 DSC 設定中的憑證。

| 活動 | 描述 |
|:---|:---|
|Get-AutomationCertificate|取得要在 Runbook 或 DSC 組態中使用的憑證。 傳回 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 物件。|

> [!NOTE] 
> 您應該避免在 Runbook 或 DSC 設定中 **Get-AutomationCertificate** 的 –Name 參數中使用變數，因為它會使在設計階段中探索 Runbook 或 DSC 設定與自動化變數之間的相依性變得複雜。

## <a name="python2-functions"></a>Python2 函式

下表中的函式用於存取 Python2 Runbook 中的憑證。

| 函式 | 描述 |
|:---|:---|
| automationassets.get_automation_certificate | 擷取憑證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入 **automationassets** 模組，才能存取資產函式。

## <a name="creating-a-new-certificate"></a>建立新憑證

建立新憑證時，您會將 cer 或 pfx 檔案上傳到 Azure 自動化。 如果您將憑證標示為可匯出，那麼您可以將它傳送到 Azure 自動化憑證存放區外部。 如果不是可匯出，然後它只能用於簽署的 runbook 或 DSC 組態內。 Azure 自動化需要讓憑證擁有提供者：**Microsoft Enhanced RSA 與 AES 密碼編譯提供者**。

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 入口網站建立新憑證

1. 從您的自動化帳戶，按一下**資產**圖格以開啟**資產**頁面。
2. 按一下 **憑證**圖格以開啟**憑證**頁面。
3. 按一下 **將憑證新增**頁面的頂端。
4. 在 [ **名稱** ] 方塊中輸入憑證的名稱。
5. 若要瀏覽 .cer 或 .pfx 檔案，請按一下 [上傳憑證檔案] 下方的 [選取檔案]。 如果您選取.pfx 檔案，請指定密碼，以及是否可以匯出。
6. 按一下 [ **建立** ] 以儲存新的憑證資產。

### <a name="to-create-a-new-certificate-with-powershell"></a>若要使用 PowerShell 建立新的憑證

下列範例示範如何建立新的自動化憑證，並將其標示為可匯出。 這樣會匯入現有的 pfx 檔案。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>使用 Resource Manager 範本建立新的憑證

下列範例示範如何將憑證部署至您使用 Resource Manager 範本透過 PowerShell 的自動化帳戶：

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>使用憑證

若要使用憑證，請使用 **Get-AutomationCertificate** 活動。 您無法使用[Get-azurermautomationcertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet，因為它會傳回憑證資產，但不是憑證本身的相關資訊。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例程式碼示範如何將憑證新增到 Runbook 中的雲端服務。 在此範例中，密碼是擷取自加密的自動化變數。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>圖形化 Runbook 範例

您將新增**Get-automationcertificate**至圖形化 runbook 程式庫 窗格中的憑證上按一下滑鼠右鍵，然後選取**新增至畫布**。

![將憑證新增至畫布](../media/certificates/automation-certificate-add-to-canvas.png)

下圖顯示在圖形化 Runbook 中使用憑證的範例。 這是上述範例，示範如何將憑證新增至雲端服務中，從文字式 runbook 相同。

![範例圖形化編寫](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 範例

下列範例示範如何存取 Python2 Runbook 中的憑證。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>後續步驟

- 若要深入了解使用連結控制您的 Runbook 設計用來執行的活動之邏輯流程，請參閱[圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)。 
