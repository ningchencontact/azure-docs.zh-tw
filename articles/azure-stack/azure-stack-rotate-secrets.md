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
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 2b1dc0ad28a6608e3a46087d31a3d077e9291a3d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841671"
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
    - 系統管理員 Keyvault 
    - Keyvault 
    - ACS (包含 Blob、資料表和佇列儲存體) 
    - ADFS<sup>*</sup>
    - Graph<sup>*</sup>

   <sup>*</sup> 僅在環境的身分識別提供者是 Active Directory 同盟服務 (AD FS) 時適用。

> [!NOTE]  
> 其他所有安全金鑰和字串，包括 BMC 和交換器密碼，以及使用者和系統管理員帳戶密碼仍然由系統管理員手動更新。 

為維持 Azure Stack 基礎結構的完整性，操作員必須可定期輪替其基礎結構的秘密，而輪替頻率需與其組織的安全性需求一致。

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

<sup>*</sup> 此處的公用憑證授權單位都包含在 Windows 受信任的根憑證計劃之中。 您可以找到完整清單 [Microsoft 受信任的根憑證計劃：參與者 (截至 2017 年 6 月 27 日為止)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>警示補救

密碼到期後的 30 天內，系統管理員入口網站中會產生下列警示： 

- 擱置的服務帳戶密碼到期 
- 擱置的內部憑證到期 
- 擱置的外部憑證到期 

使用下列指示執行秘密輪替將會修正這些警示。

## <a name="pre-steps-for-secret-rotation"></a>秘密輪替前的步驟

   > [!IMPORTANT]  
   > 確定之前未成功在您的環境上執行祕密輪替。 如果已執行過秘密輪替，請在執行祕密輪替之前，將 Azure Stack 更新至版本 1807 或更新版本。 

1.  運算子可能會注意到警示開啟，並在 Azure Stack 秘密輪替期間，自動關閉警示。  這是可預期的行為，可以忽略這類警示。  運算子可以透過執行 Test-AzureStack 來驗證這些警示的有效性。  若運算子使用 SCOM 監控 Azure Stack 系統，讓系統進入維護模式，即可避免這些警示傳送到其所屬的 ITSM 系統，但如果無法連線到 Azure Stack，就會持續發出警示。 
2. 將任何維護作業告知您的使用者。 排程一般維護期間，盡可能排在非上班時間。 維護作業會影響使用者工作負載和入口網站作業。
    > [!note]  
    > 下一個步驟僅適用於更換 Azure Stack 外部密碼時。

3. 執行 **[Test-AzureStack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)** 並先確認所有測試輸出都狀況良好，再輪替祕密。
4. 準備一組新的替代外部憑證。 新的一組憑證需符合 [Azure Stack PKI 憑證需求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)中所述的憑證規格。
5.  將用於輪替的憑證備份儲存在安全的備份位置。 如果執行輪替時發生失敗，您可以使用備份副本取代檔案共用中的憑證，然後再重新執行輪替。 請記得將備份副本保存在安全的備份位置。
6.  建立可以從 ERCS VM 存取的檔案共用。 檔案共用必須讓 **CloudAdmin** 身分識別可讀取和寫入。
7.  從您可存取檔案共用的電腦中開啟 PowerShell ISE 主控台。 瀏覽至您的檔案共用。 
8.  執行 **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** 以建立您外部憑證所需的目錄。

## <a name="rotating-external-and-internal-secrets"></a>輪替外部和內部秘密

若要輪替外部和內部秘密：

1. 在前面步驟新建的 **/Certificates** 目錄中，依據 [Azure Stack PKI 憑證需求](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)內＜必要憑證＞一節中所述的格式，將新的一組替代外部憑證放入目錄結構中。
2. 使用 **CloudAdmin** 帳戶建立[具特殊權限端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)的 PowerShell 工作階段，並以變數形式儲存工作階段。 您將使用此變數作為下一個步驟中的參數。

    > [!IMPORTANT]  
    > 請勿輸入工作階段，請以變數形式儲存工作階段。
    
3. 執行 **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**。 將具特殊權限的端點 PowerShell 工作階段變數當作 **Session** 參數傳送。 
4. 以下列參數執行 **Start-SecretRotation**：
    - **PfxFilesPath**  
    將網路路徑指定為先前建立的憑證目錄。  
    - **PathAccessCredential**  
    向共用進行認證的 PSCredential 物件。 
    - **CertificatePassword**  
    密碼的安全字串，用於所有建立的 pfx 憑證檔案。
4. 等候秘密進行輪替。  
當秘密輪替成功完成時，您的主控台會顯示**整體動作狀態：成功**。 
    > [!note]  
    > 如果祕密輪替失敗，請依照錯誤訊息中的指示進行，並使用 **-Rerun** 參數重新執行 start-secretrotation。 如果秘密輪替重複失敗，請聯絡支援人員。 
5. 成功完成秘密輪替後，請從前面步驟建立的共用中移除您的憑證，並將其儲存在安全的備份位置。 

## <a name="walkthrough-of-secret-rotation"></a>秘密輪替的逐步解說

下列 PowerShell 範例會示範輪替秘密時須執行的 Cmdlet 與參數。

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>僅輪替內部秘密

若要輪替 Azure Stack 的內部秘密：

1. 建立具有[特殊權限端點](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)的 PowerShell 工作階段。
2. 在具特殊權限的端點工作階段中，執行 **Start-SecretRotation** (不使用引數)。
3. 等候秘密進行輪替。  
當秘密輪替成功完成時，您的主控台會顯示**整體動作狀態：成功**。 
    > [!note]  
    > 如果祕密輪替失敗，請依照錯誤訊息中的指示進行，並使用 **-Rerun** 參數重新執行 start-secretrotation。 如果秘密輪替重複失敗，請聯絡支援人員。 

## <a name="start-secretrotation-reference"></a>Start-SecretRotation 參考

輪替 Azure Stack 系統的秘密。 只針對 Azure Stack 具特殊權限的端點執行。

### <a name="syntax"></a>語法

路徑 (預設值)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>說明

Start-SecretRotation Cmdlet 會輪替 Azure Stack 系統的基礎結構秘密。 依預設，其會輪替對內部基礎結構網路公開的所有秘密，而透過使用者輸入，其也會輪替所有外部網路基礎結構端點的憑證。 輪替外部網路基礎結構端點時，應透過 Invoke-Command 指令碼區塊，以及 Azure Stack 環境中具特殊權限的端點工作階段 (作為工作階段參數傳入) 來執行 Start-SecretRotation。
 
### <a name="parameters"></a>參數

| 參數 | 類型 | 必要 | 位置 | 預設值 | 說明 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | 字串  | False  | 已命名  | None  | **\Certificates** 目錄的檔案共用路徑包含所有外部網路端點的憑證。 僅在輪替外部秘密或所有祕密時才需要。 結尾目錄必須是 **\Certificates**。 |
| CertificatePassword | SecureString | False  | 已命名  | None  | -PfXFilesPath 中所提供所有憑證的密碼。 如果 PfxFilesPath 是在輪替內部和外部密碼時提供，則屬於必要值。 |
| PathAccessCredential | PSCredential | False  | 已命名  | None  | **\Certificates** 目錄檔案共用的 PowerShell 認證包含所有外部網路端點的憑證。 僅在輪替外部秘密或所有祕密時才需要。  |
| Rerun | SwitchParameter | False  | 已命名  | None  | 在嘗試失敗後，若要重新嘗試秘密輪替時，就必須執行 Rerun。 |

### <a name="examples"></a>範例
 
**僅輪替內部基礎結構秘密**

```powershell  
PS C:\> Start-SecretRotation  
```

此命令會輪替對 Azure Stack 內部網路公開的所有基礎結構秘密。 Start-SecretRotation 會輪替所有堆疊產生的秘密，但因為沒有提供認證，外部端點憑證將不會輪替。  

**輪替內部和外部基礎結構秘密**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

此命令會輪替對 Azure Stack 內部網路公開的所有基礎結構秘密，以及用於 Azure Stack 外部網路基礎結構端點的 TLS 憑證。 Start-SecretRotation 會輪替所有堆疊產生的秘密，且因為有提供認證，外部端點憑證也會輪替。  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>更新基礎板管理控制器 (BMC) 密碼

基礎板管理控制器 (BMC) 可監視伺服器的實體狀態。 有關更新 BMC 密碼的規格和指示會隨著您的原始設備製造商 (OEM) 硬體廠商而有所不同。 您應定期更新您的 Azure Stack 元件密碼。

1. 依照您的 OEM 指示，在 Azure Stack 的實體伺服器上更新 BMC。 您環境中每個 BMC 的密碼必須相同。
2. 在 Azure Stack 工作階段中開啟具有特殊權限的端點。 如需相關指示，請參閱[使用 Azure Stack 中具有特殊權限的端點](azure-stack-privileged-endpoint.md)。
3. 在您的 PowerShell 提示變更為 **[IP 位址或 ERCS VM 名稱]:PS>** 或變更為 **[azs-ercs01]:PS>** (取決於環境) 之後，藉由執行 `invoke-command` 來執行 `Set-BmcPassword`。 將具有特殊權限的端點工作階段變數當作參數傳送。 例如︰

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    您也可以使用靜態 PowerShell 版本搭配密碼，如以下程式碼行所示：
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>後續步驟

[深入了解 Azure Stack 安全性](azure-stack-security-foundations.md)
