---
title: 在 Azure Stack 中輪替使用祕密 | Microsoft Docs
description: 了解如何在 Azure Stack 中輪替使用祕密。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e4131bc8f038957e52b914937b2d45e670be8f5f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157270"
---
# <a name="rotate-secrets-in-azure-stack"></a>在 Azure Stack 中輪替使用祕密

這些指示只適用於 Azure Stack 整合系統 1803 版或更新版本。*請勿在早於 1802 版本的 Azure Stack 上嘗試使用秘密輪替*

Azure Stack 會使用各種祕密來維護 Azure Stack 基礎結構資源和服務之間的通訊安全。

- **內部祕密**

由 Azure Stack 基礎結構使用的所有憑證、密碼、安全字串及金鑰 (無須 Azure Stack 操作員介入)。

- **外部祕密**

對外服務基礎結構的服務憑證 (由 Azure Stack 操作員提供)。 這包括下列服務的憑證：

- 系統管理員入口網站
- 公用入口網站
- 系統管理員 Azure Resource Manager
- 全域 Azure Resource Manager
- 系統管理員 KeyVault
- KeyVault
- 管理員延伸主機
- ACS (包含 Blob、資料表和佇列儲存體)
- ADFS *
- Graph *

\* 只有在環境的識別提供者是「Active Directory 同盟服務」(AD FS) 時才適用。

> [!Note]
> 其他所有安全金鑰和字串，包括 BMC 和交換器密碼，以及使用者和系統管理員帳戶密碼仍然由系統管理員手動更新。

> [!Important]
> 從 Azure Stack 1811 版開始，內部憑證和外部憑證的祕密輪替已分開。

為了維護 Azure Stack 基礎結構的完整性，操作員必須能夠定期輪替其基礎結構的祕密，且輪替頻率需與其組織的安全性需求一致。

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>使用新憑證授權單位的外部憑證來輪替祕密

Azure Stack 支援在下列環境中使用新憑證授權單位 (CA) 的外部憑證來輪替祕密：

|已安裝的憑證 CA|要將 CA 輪替為|支援|支援的 Azure Stack 版本|
|-----|-----|-----|-----|
|從自我簽署|到企業|不支援||
|從自我簽署|到自我簽署|不支援||
|從自我簽署|到公用<sup>*</sup>|支援|1803 或更新版本|
|從企業|到企業|支援，前提是客戶使用的企業 CA 與部署時所用的一樣|1803 或更新版本|
|從企業|到自我簽署|不支援||
|從企業|到公用<sup>*</sup>|支援|1803 或更新版本|
|從公用<sup>*</sup>|到企業|不支援|1803 或更新版本|
|從公用<sup>*</sup>|到自我簽署|不支援||
|從公用<sup>*</sup>|到公用<sup>*</sup>|支援|1803 或更新版本|

<sup>*</sup>指出「公用憑證授權單位」是參與「Windows 受信任的根憑證計劃」的授權單位。 您可以在下列文章中找到完整清單：[Microsoft 受信任的根憑證計劃：參與者 (截至 2017 年 6 月 27 日為止)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>警示補救

密碼到期後的 30 天內，系統管理員入口網站中會產生下列警示：

- 擱置的服務帳戶密碼到期
- 擱置的內部憑證到期
- 擱置的外部憑證到期

使用下列指示執行秘密輪替將會修正這些警示。

> [!Note]
> 在 1811 之前版本的 Azure Stack 環境中，可能會看到擱置中的內部憑證或祕密到期警示。
> 這些是不正確的警示，應該予以忽略而不需執行內部祕密輪替。
> 不正確的內部祕密到期警示是在 1811 中已解決的已知問題 – 除非環境處於作用中的時間已達兩年，否則內部祕密不會過期。

## <a name="pre-steps-for-secret-rotation"></a>秘密輪替前的步驟

   > [!IMPORTANT]
   > 如果您的 Azure Stack 環境上已經執行過祕密輪替，在您重新執行祕密輪替之前，必須先將系統更新至 1811 版或更新版本。
   > 執行「祕密輪替」時，必須透過[具有特殊權限的端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)，且必須要有「Azure Stack 操作員」認證。
   > 如果您的環境「Azure Stack 操作員」不知道您的環境上是否已執行過祕密輪替，請先更新至 1811，再重新執行祕密輪替。

1. 強烈建議您將 Azure Stack 執行個體更新至 1811 版。

    > [!Note] 
    > 針對 1811 前的版本，您無須輪替祕密，即可新增延伸主機憑證。 您應該依照 [Azure Stack 的延伸主機準備](azure-stack-extension-host-prepare.md)一文中的指示，來新增延伸主機憑證。

2. 運算子可能會注意到警示開啟，並在 Azure Stack 秘密輪替期間，自動關閉警示。  這是可預期的行為，可以忽略這類警示。  操作員可以執行 **Test-AzureStack** 來驗證這些警示的有效性。  若運算子使用 SCOM 監控 Azure Stack 系統，讓系統進入維護模式，即可避免這些警示傳送到其所屬的 ITSM 系統，但如果無法連線到 Azure Stack，就會持續發出警示。

3. 將任何維護作業告知您的使用者。 排程一般維護期間，盡可能排在非上班時間。 維護作業會影響使用者工作負載和入口網站作業。

    > [!Note]
    > 下一個步驟僅適用於更換 Azure Stack 外部密碼時。

4. 執行 **[Test-AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** 並先確認所有測試輸出都狀況良好，再輪替祕密。
5. 準備一組新的替代外部憑證。 新的一組憑證需符合 [Azure Stack PKI 憑證需求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)中所述的憑證規格。 您可以使用[產生 PKI 憑證](https://docs.microsoft.com/azure/azure-stack/azure-stack-get-pki-certs)中所簡述的步驟，來產生用於購買或建立新憑證的憑證簽署要求 (CSR)，然後使用[準備 Azure Stack PKI 憑證](https://docs.microsoft.com/azure/azure-stack/azure-stack-prepare-pki-certs)中的步驟來準備這些憑證，以在您的 Azure Stack 環境中使用。 請務必使用[驗證 PKI 憑證](https://docs.microsoft.com/azure/azure-stack/azure-stack-validate-pki-certs)中所簡述的步驟來驗證您準備的憑證。
6. 將用於輪替的憑證備份儲存在安全的備份位置。 如果執行輪替時發生失敗，您可以使用備份副本取代檔案共用中的憑證，然後再重新執行輪替。 請記得將備份副本保存在安全的備份位置。
7. 建立可以從 ERCS VM 存取的檔案共用。 檔案共用必須讓 **CloudAdmin** 身分識別可讀取和寫入。
8. 從您可存取檔案共用的電腦中開啟 PowerShell ISE 主控台。 瀏覽至您的檔案共用。
9. 執行 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 以建立您外部憑證所需的目錄。

> [!IMPORTANT]
> CertDirectoryMaker 指令碼將建立符合以下內容的資料夾結構：
>
> **.\Certificates\AAD** 或 ***.\Certificates\ADFS***，取決於用於 Azure Stack 的識別提供者而定
>
> 至關重要的是，您的資料夾結構是以 **AAD** 或 **ADFS** 資料夾結尾，而且所有子資料夾都在此結構中；否則，**Start-SecretRotation** 將提供：
> ```PowerShell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> 正如您所看到的，錯誤訊息會指出存取檔案共用時出現問題，但實際上它是此處強制執行的資料夾結構。
> 詳細資訊可在 Microsoft AzureStack 整備檢查程式 - [PublicCertHelper 模組](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)中找到
>
> 同樣重要的是，您的檔案共用資料夾結構是以 **Certificates** 資料夾開頭，否則驗證時也會失敗。
> 檔案共用裝載應該如下所示：**\\\\\<IPAddress>\\\<ShareName>\\**，而且應該包含資料夾 **Certificates\AAD** 或 **Certificates\ADFS**。
>
> 例如︰
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>輪替外部祕密

輪替外部祕密：

1. 在前面步驟新建的 **\Certificates\\\<IdentityProvider>** 目錄中，依據 [Azure Stack PKI 憑證需求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)內＜必要憑證＞一節中所述的格式，將新的一組替代外部憑證放入目錄結構中。

    AAD 識別提供者的資料夾結構範例：
    ```PowerShell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. 使用 **CloudAdmin** 帳戶建立[具特殊權限端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)的 PowerShell 工作階段，並以變數形式儲存工作階段。 您將使用此變數作為下一個步驟中的參數。

    > [!IMPORTANT]  
    > 請勿輸入工作階段，請以變數形式儲存工作階段。

3. 執行 **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)**。 將具特殊權限的端點 PowerShell 工作階段變數當作 **Session** 參數傳送。

4. 以下列參數執行 **Start-SecretRotation**：
    - **PfxFilesPath**  
    將網路路徑指定為先前建立的憑證目錄。  
    - **PathAccessCredential**  
    向共用進行認證的 PSCredential 物件。
    - **CertificatePassword**  
    密碼的安全字串，用於所有建立的 pfx 憑證檔案。

5. 等候秘密進行輪替。 外部祕密輪替通常需要大約一小時的時間。

    當秘密輪替成功完成時，您的主控台會顯示**整體動作狀態：成功**。

    > [!Note]
    > 如果祕密輪替失敗，請依照錯誤訊息中的指示進行操作，並搭配 **-ReRun** 參數來重新執行 **Start-SecretRotation**。

    ```PowerShell
    Start-SecretRotation -ReRun
    ```
    如果秘密輪替重複失敗，請聯絡支援人員。

6. 成功完成秘密輪替後，請從前面步驟建立的共用中移除您的憑證，並將其儲存在安全的備份位置。

## <a name="walkthrough-of-secret-rotation"></a>秘密輪替的逐步解說

下列 PowerShell 範例會示範輪替秘密時須執行的 Cmdlet 與參數。

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>僅輪替內部秘密

> [!Note]
> 只有當您懷疑內部祕密已受到惡意實體危害，或當您收到指出內部憑證即將到期的警示 (在 1811 或更新版本的組建上) 時，才應該執行「內部祕密輪替」。
> 在 1811 之前版本的 Azure Stack 環境中，可能會看到擱置中的內部憑證或祕密到期警示。
> 這些是不正確的警示，應該予以忽略而不需執行內部祕密輪替。
> 不正確的內部祕密到期警示是在 1811 中已解決的已知問題 – 除非環境處於作用中的時間已達兩年，否則內部祕密不會過期。

1. 建立具有[特殊權限端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)的 PowerShell 工作階段。
2. 在「具特殊權限的端點」工作階段中，執行 **Start-SecretRotation -Internal**。

    > [!Note]
    > 1811 之前版本的 Azure Stack 環境將不需要 **-Internal** 旗標。 **Start-SecretRotation** 只會輪替內部祕密。

3. 等候秘密進行輪替。

當秘密輪替成功完成時，您的主控台會顯示**整體動作狀態：成功**。
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **–Internal** and **-ReRun** parameters.  

```PowerShell
Start-SecretRotation -Internal -ReRun
```

如果秘密輪替重複失敗，請聯絡支援人員。

## <a name="start-secretrotation-reference"></a>Start-SecretRotation 參考

輪替 Azure Stack 系統的秘密。 只針對 Azure Stack 具特殊權限的端點執行。

### <a name="syntax"></a>語法

#### <a name="for-external-secret-rotation"></a>針對外部祕密輪替

```PowerShell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>針對內部祕密輪替

```PowerShell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>針對外部祕密輪替的重新執行

```PowerShell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>針對內部祕密輪替的重新執行

```PowerShell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>說明

**Start-SecretRotation** Cmdlet 會輪替 Azure Stack 系統的基礎結構祕密。 根據預設，它只會輪替所有外部網路基礎結構端點的憑證。 如果與 -Internal 旗標搭配使用，則會輪替內部基礎結構祕密。 輪替外部網路基礎結構端點時，應使用 **Invoke-Command** 指令碼區塊，且其中傳入 Azure Stack 環境的具特殊權限端點工作階段作為 **Session** 參數，來執行 **Start-SecretRotation**。

### <a name="parameters"></a>參數

| 參數 | 類型 | 必要 | 位置 | 預設值 | 說明 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | 字串  | False  | 已命名  | None  | **\Certificates** 目錄的檔案共用路徑包含所有外部網路端點的憑證。 僅在輪替外部密碼時才需要。 結尾目錄必須是 **\Certificates**。 |
| CertificatePassword | SecureString | False  | 已命名  | None  | -PfXFilesPath 中所提供所有憑證的密碼。 如果 PfxFilesPath 是在輪替外部密碼時提供，則為必要值。 |
| 內部 | 字串 | False | 已命名 | None | 每當 Azure Stack 操作員想要輪替內部基礎結構祕密時，都必須使用 Internal 旗標。 |
| PathAccessCredential | PSCredential | False  | 已命名  | None  | **\Certificates** 目錄檔案共用的 PowerShell 認證包含所有外部網路端點的憑證。 僅在輪替外部密碼時才需要。  |
| ReRun | SwitchParameter | False  | 已命名  | None  | 在嘗試失敗之後，每當想重新嘗試祕密輪替時，都必須使用 ReRun。 |

### <a name="examples"></a>範例

#### <a name="rotate-only-internal-infrastructure-secrets"></a>僅輪替內部基礎結構祕密

這必須透過您 Azure Stack [環境的具特殊權限端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)來執行。

```PowerShell
PS C:\> Start-SecretRotation -Internal
```

此命令會輪替對 Azure Stack 內部網路公開的所有基礎結構秘密。

#### <a name="rotate-only-external-infrastructure-secrets"></a>僅輪替外部基礎結構祕密  

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

此命令會輪替用於 Azure Stack 外部網路基礎結構端點的 TLS 憑證。

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>輪替內部和外部基礎結構密碼 (僅限 **1811 之前的版本**)

> [!IMPORTANT]
> 這個命令僅適用於 Azure Stack **1811 之前的版本**，因為已為內部和外部憑證分割輪替。
>
> **在 *1811 以上的版本*中，您無法再同時輪替內部和外部憑證！**

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

此命令會輪替對 Azure Stack 內部網路公開的所有基礎結構祕密，以及用於 Azure Stack 外部網路基礎結構端點的 TLS 憑證。 Start-SecretRotation 會輪替所有堆疊產生的秘密，且因為有提供認證，外部端點憑證也會輪替。  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>更新基礎板管理控制器 (BMC) 認證

基礎板管理控制器 (BMC) 可監視伺服器的實體狀態。 與更新 BMC 使用者帳戶名稱和密碼相關的規格及指示，會依據您的原始設備製造商 (OEM) 硬體廠商而有所不同。 您應該定期更新您的 Azure Stack 元件密碼。

1. 依照您的 OEM 指示，更新 Azure Stack 實體伺服器上的 BMC。 您環境中每個 BMC 的使用者帳戶名稱和密碼必須相同。
2. 在 Azure Stack 工作階段中開啟具有特殊權限的端點。 如需相關指示，請參閱[使用 Azure Stack 中具有特殊權限的端點](azure-stack-privileged-endpoint.md)。
3. 在您的 PowerShell 提示變更為 **[IP 位址或 ERCS VM 名稱]:PS>** 或變更為 **[azs-ercs01]:PS>** (取決於環境) 之後，藉由執行 `Invoke-Command` 來執行 `Set-BmcCredential`。 將具有特殊權限的端點工作階段變數當作參數傳送。 例如︰

    ```PowerShell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    您也可以使用靜態 PowerShell 版本搭配密碼，如以下程式碼行所示：

    ```PowerShell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>後續步驟

[深入了解 Azure Stack 安全性](azure-stack-security-foundations.md)
