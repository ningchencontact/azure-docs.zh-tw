---
title: Start-AzsReadinessChecker Cmdlet 參考 | Microsoft Docs
description: Azure Stack 整備檢查程式模組的 PowerShell Cmdlet 說明。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757774"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Start-AzsReadinessChecker Cmdlet 參考

模組：**Microsoft.AzureStack.ReadinessChecker**

此模組只包含單一 Cmdlet。 Cmdlet 會為 Azure Stack 執行一或多個預先部署或預先維護函式。

## <a name="syntax"></a>語法

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>說明

**Start-AzsReadinessChecker** Cmdlet 會驗證憑證、Azure 帳戶、Azure 訂用帳戶和 Azure Active Directory。 在部署 Azure Stack 之前，或在執行 Azure Stack 維護動作 (例如祕密輪替) 之前，請先執行驗證。 此 Cmdlet 也可用來產生基礎結構憑證的憑證簽署要求，以及選擇性地產生 PaaS 憑證的要求。 最後，此 Cmdlet 可以重新封裝 PFX 憑證，以修正常見的封裝問題。

## <a name="examples"></a>範例

### <a name="example-generate-certificate-signing-request"></a>範例：產生憑證簽署要求

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

在此範例中，`Start-AzsReadinessChecker` 會針對區域名稱為 **east** 且外部 FQDN 為 **azurestack.contoso.com** 的 AD FS Azure Stack 部署所適用的憑證產生多個憑證簽署要求 (CSR)。

### <a name="example-validate-certificates"></a>範例：驗證憑證

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

在此範例中，基於安全考量會需要 PFX 密碼，而 `Start-AzsReadinessChecker` 會在相對的資料夾 **Certificates** 中檢查是否有區域名稱為 **east** 且外部 FQDN 為 **azurestack.contoso.com** 的 AAD 部署所適用的有效憑證。

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>範例：驗證部署資料的憑證 (部署和支援)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

在此部署和支援範例中，基於安全考量需要 PFX 密碼，而 `Start-AzsReadinessChecker` 會在相對的資料夾 **Certificates** 中檢查是否有身分識別、區域和外部 FQDN 皆是從為部署產生的部署資料 JSON 檔案中讀取的部署所適用的有效憑證。

### <a name="example-validate-paas-certificates"></a>範例：驗證 PaaS 憑證

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

在此範例中，會以每個 PaaS 憑證的路徑和密碼建構雜湊表。 連接埠可以省略。 `Start-AzsReadinessChecker` 會檢查每個 PFX 路徑是否存在，並使用區域 **east** 和外部 FQDN **azurestack.contoso.com** 加以驗證。

### <a name="example-validate-paas-certificates-with-deployment-data"></a>範例：驗證部署資料的 PaaS 憑證

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

在此範例中，會以每個 PaaS 憑證的路徑和密碼建構雜湊表。 連接埠可以省略。 `Start-AzsReadinessChecker` 會檢查每個 PFX 路徑是否存在，並使用從為部署產生的部署資料 JSON 檔案中讀取的區域和外部 FQDN 加以驗證。

### <a name="example-validate-azure-identity"></a>範例：驗證 Azure 身分識別

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

在此範例中，基於安全考量需要服務管理員帳戶認證，而 `Start-AzsReadinessChecker` 會檢查 Azure 帳戶和 Azure Active Directory 是否適用於租用戶目錄名稱為 **azurestack.contoso.com** 的 AAD 部署。

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>範例：驗證部署資料的 Azure 身分識別 (部署支援)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

在此範例中，基於安全考量需要服務管理員帳戶認證，而 `Start-AzsReadinessChecker` 會檢查 Azure 帳戶和 Azure Active Directory 是否適用於 **AzureCloud** 和 **TenantName** 皆是從為部署產生的部署資料 JSON 檔案讀取的 AAD 部署。

### <a name="example-validate-azure-registration"></a>範例：驗證 Azure 註冊

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

在此範例中，基於安全考量需要訂用帳戶擁有者認證，而 `Start-AzsReadinessChecker` 會對指定的帳戶和訂用帳戶執行驗證，以確定它可以用於 Azure Stack 註冊。

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>範例：驗證部署資料的 Azure 註冊 (部署小組)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

在此範例中，基於安全考量需要訂用帳戶擁有者認證，而 `Start-AzsReadinessChecker` 會對指定的帳戶和訂用帳戶執行驗證，以確定它可以用於有其他詳細資料是從為部署產生的部署資料 JSON 檔案中讀取的 Azure Stack 註冊。

### <a name="example-importexport-pfx-package"></a>範例：匯入/匯出 PFX 套件

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

在此範例中，基於安全考量需要 PFX 密碼。 Ssl.pfx 檔案會匯入到本機電腦憑證存放區、以相同的密碼重新匯出，並儲存為 Ssl_new.pfx。 此程序適用於憑證驗證以旗標指出私密金鑰未設定**本機電腦**屬性、憑證鏈結已中斷、PFX 中有不相關的憑證存在，或憑證鏈結順序錯誤等情況。

### <a name="example-view-validation-report-deployment-support"></a>範例：檢視驗證報告 (部署支援)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

在此範例中，部署或支援小組會接收客戶 (Contoso) 傳來的整備報告，並使用 `Start-AzsReadinessChecker` 檢視 Contoso 所執行之驗證的狀態。

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>範例：僅檢視憑證驗證的驗證報告摘要 (部署和支援)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

在此範例中，部署或支援小組會接收客戶 (Contoso) 傳來的整備報告，並使用 `Start-AzsReadinessChecker` 檢視 Contoso 所執行之憑證驗證的摘要狀態。

## <a name="required-parameters"></a>必要參數

### <a name="-regionname"></a>-RegionName

指定 Azure Stack 部署區域名稱。

|  |  |
|----------------------------|--------------|
|輸入：                       |字串        |
|位置：                   |已命名         |
|預設值：              |None          |
|接受管線輸入：      |False         |
|接受萬用字元： |False         |

### <a name="-fqdn"></a>-FQDN

指定 Azure Stack 部署外部 FQDN，同時設定為 **ExternalFQDN** 和 **ExternalDomainName** 別名。

|  |  |
|----------------------------|--------------|
|輸入：                       |字串        |
|位置：                   |已命名         |
|預設值：              |ExternalFQDN、ExternalDomainName |
|接受管線輸入：      |False         |
|接受萬用字元： |False         |

### <a name="-identitysystem"></a>-IdentitySystem

指定 Azure Stack 部署身分識別系統有效值 AAD 或 ADFS (分別代表 Azure Active Directory 和 Active Directory 同盟服務)。

|  |  |
|----------------------------|--------------|
|輸入：                       |字串        |
|位置：                   |已命名         |
|預設值：              |None          |
|有效值：               |'AAD','ADFS'  |
|接受管線輸入：      |False         |
|接受萬用字元： |False         |

### <a name="-pfxpassword"></a>-PfxPassword

指定與 PFX 憑證檔案相關聯的密碼。

|  |  |
|----------------------------|---------|
|輸入：                       |SecureString |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

指定包含 PaaS 憑證之路徑與密碼的雜湊表。

|  |  |
|----------------------------|---------|
|輸入：                       |雜湊表 |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

指定 Azure Stack 部署的資料 JSON 組態檔。 此檔案是為部署產生的。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-pfxpath"></a>-PfxPath

指定有問題而需要以匯入/匯出常式修正的憑證 (如此工具中的憑證驗證所指出) 的路徑。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

指定匯入/匯出常式所產生之 PFX 檔案的目的地路徑。  

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-subject"></a>-Subject

指定用來產生憑證要求之主體已排序的字典。

|  |  |
|----------------------------|---------|
|輸入：                       |OrderedDictionary   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-requesttype"></a>-RequestType

指定憑證要求的 SAN 類型。 有效值為 **MultipleCSR**、**SingleCSR**。

- **MultipleCSR** 會產生多個憑證要求，每項服務各一個。
- **SingleCSR** 會為所有服務產生一個憑證要求。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|有效值：               |'MultipleCSR','SingleCSR' |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

指定憑證要求檔案的目的地路徑。 目錄必須已存在。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

指定要用於 Azure Stack 部署的 Azure Active Directory 服務管理員。

|  |  |
|----------------------------|---------|
|輸入：                       |PSCredential   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

指定要用於 Azure Stack 部署的 Azure Active Directory 名稱。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

指定要用於 Azure Stack 部署與註冊的帳戶、目錄和訂用帳戶所屬的 Azure 服務執行個體。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|有效值：               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

指定要用於 Azure Stack 註冊的註冊帳戶。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

指定要用於 Azure Stack 註冊的註冊訂用帳戶識別碼。

|  |  |
|----------------------------|---------|
|輸入：                       |Guid     |
|位置：                   |已命名    |
|預設值：              |None     |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-reportpath"></a>-ReportPath

指定整備報告的路徑，預設值為目前的目錄和預設報告名稱。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |全部      |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

## <a name="optional-parameters"></a>選擇性參數

### <a name="-certificatepath"></a>-CertificatePath

指定只有憑證所需的憑證資料夾存在的路徑。

Azure Active Directory 身分識別系統的 Azure Stack 部署所需的資料夾為：

ACSBlob、ACSQueue、ACSTable、Admin Portal、ARM Admin、ARM Public、KeyVault、KeyVaultInternal、Public Portal

Active Directory 同盟服務身分識別系統的 Azure Stack 部署所需的資料夾為：

ACSBlob、ACSQueue、ACSTable、ADFS、Admin Portal、ARM Admin、ARM Public、Graph、KeyVault、KeyVaultInternal、Public Portal

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |.\Certificates |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-includepaas"></a>-IncludePaaS  

指定 PaaS 服務/主機名稱是否應新增至憑證要求。

|  |  |
|----------------------------|------------------|
|輸入：                       |SwitchParameter   |
|位置：                   |已命名             |
|預設值：              |False             |
|接受管線輸入：      |False             |
|接受萬用字元： |False             |

### <a name="-reportsections"></a>-ReportSections

指定是否僅顯示報告摘要，而省略詳細資料。

|  |  |
|----------------------------|---------|
|輸入：                       |字串   |
|位置：                   |已命名    |
|預設值：              |全部      |
|有效值：               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|接受管線輸入：      |False    |
|接受萬用字元： |False    |

### <a name="-summary"></a>-Summary

指定是否僅顯示報告摘要，而省略詳細資料。

|  |  |
|----------------------------|------------------|
|輸入：                       |SwitchParameter   |
|位置：                   |已命名             |
|預設值：              |False             |
|接受管線輸入：      |False             |
|接受萬用字元： |False             |

### <a name="-cleanreport"></a>-CleanReport

移除先前的執行和驗證歷程記錄，並將驗證寫入至新報表。

|  |  |
|----------------------------|------------------|
|輸入：                       |SwitchParameter   |
|別名：                    |cf                |
|位置：                   |已命名             |
|預設值：              |False             |
|接受管線輸入：      |False             |
|接受萬用字元： |False             |

### <a name="-outputpath"></a>-OutputPath

指定用來儲存整備 JSON 報告和詳細記錄檔的自訂路徑。 如果路徑不存在，命令會嘗試建立目錄。

|  |  |
|----------------------------|------------------|
|輸入：                       |字串            |
|位置：                   |已命名             |
|預設值：              |$ENV:TEMP\AzsReadinessChecker  |
|接受管線輸入：      |False             |
|接受萬用字元： |False             |

### <a name="-confirm"></a>-Confirm

在執行 Cmdlet 前提示您確認。

|  |  |
|----------------------------|------------------|
|輸入：                       |SwitchParameter   |
|別名：                    |cf                |
|位置：                   |已命名             |
|預設值：              |False             |
|接受管線輸入：      |False             |
|接受萬用字元： |False             |

### <a name="-whatif"></a>-WhatIf

顯示執行 Cmdlet 後會發生的情況。 Cmdlet 並不會執行。

|  |  |
|----------------------------|------------------|
|輸入：                       |SwitchParameter   |
|別名：                    |wi                |
|位置：                   |已命名             |
|預設值：              |False             |
|接受管線輸入：      |False             |
|接受萬用字元： |False             |
