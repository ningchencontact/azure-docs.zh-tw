---
title: "為 Azure Stack 整合式系統部署驗證 Azure Stack 公開金鑰基礎結構憑證 | Microsoft Docs"
description: "說明如何驗證 Azure Stack 整合式系統的 Azure Stack PKI 憑證。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 86f1b889d83905abfb5ddab2e82f32922409ff5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>驗證 Azure Stack PKI 憑證
本文中所述的 Azure Stack 憑證檢查工具是由隨附於 deploymentdata.json 檔案的 OEM 提供，以驗證[產生的 PKI 憑證](azure-stack-get-pki-certs.md)適用於預先部署。 憑證驗證應該具有足夠的時間，以進行測試並在必要時將憑證重新發出。 

憑證檢查工具 (Certchecker) 會執行下列檢查：

- **讀取 PFX**。 確認 PFX 檔案有效且密碼正確，並在公用資訊未受到密碼保護時發出警告。 
- **簽章演算法**。 檢查簽章演算法不是 SHA1 
- **私密金鑰**。 確認私密金鑰存在，且是以本機電腦屬性匯出。 
- **信任鏈結**。 確認包括適用自我簽署憑證的信任鏈結。 
- **DNS 名稱**。 檢查 SAN 包含每個端點的相關 DNS 名稱，或支援萬用字元是否存在。 
- **金鑰使用方式**。 檢查金鑰使用方式包含數位簽章和金鑰加密，而增強金鑰使用方法包含伺服器驗證和用戶端驗證。 
- **金鑰大小**。 檢查金鑰大小為 2048 或更大 
- **鏈結順序**。 檢查其他鏈結的順序使憑證正確。 
- **其他憑證**。 請確定除了相關的分葉憑證及其鏈結之外，PFX 中沒有封裝其他憑證。 
- **沒有設定檔**。 檢查新的使用者無須載入使用者設定檔即可載入 PFX 資料，在憑證服務期間模擬 gMSA 帳戶的行為。   

> [!IMPORTANT]
> PKI 憑證的 PFX 檔案和密碼都應該視為機密資訊。

## <a name="prerequisites"></a>先決條件
在驗證 Azure Stack 部署的 PKI 憑證之前，您的系統應該符合下列必要條件：
- CertChecker (在 PartnerToolKit 的 \utils\certchecker 下)
- 遵循[準備指示](prepare-pki-certs.md)匯出的 SSL 憑證
- DeploymentData.json
- Windows 10 或 Windows Server 2016

## <a name="perform-certificate-validation"></a>執行憑證驗證
使用下列步驟來準備及驗證 Azure Stack PKI 憑證： 

1. 將 <partnerToolkit>\utils\certchecker 的內容擷取至新的目錄，例如，**c:\certchecker**。

2. 以系統管理員身分開啟 PowerShell，並將目錄變更為 certchecker 資料夾：

  ```powershell
  cd c:\certchecker
  ```
 
3. 執行下列 PowerShell 命令來建立憑證的目錄結構：

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  如果 Azure Stack 部署的識別提供者為 Azure AD，請移除 **ADFS** 和 **Graph** 目錄。 

4. 將您的憑證放在上一個步驟中建立的適當目錄，例如： 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - 依此類推… 

5. 將 **deploymentdata.json** 複製到 **c:\certchecker** 目錄。

6. 在 PowerShell 視窗中執行下列命令： 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. 所有憑證的輸出都應該包含 OK，並已檢查所有屬性： 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>已知問題 
**徵兆**：Certchecker 會提前結束，且您會收到下列錯誤： 
> Failed

> 詳細資料：無法執行此命令，因為發生錯誤：目錄名稱無效。 

**原因**：從限制的資料夾 (例如 c:\temp 或 %temp%) 中執行 certchecker.ps1 

**解析**：將 certchecker 工具移動到新目錄，例如 C:\CertChecker 


**徵兆**：Certchecker 會提供有關使用 (如同上述步驟 7 中的範例) Pre-1803 的警告：

> [!WARNING]
> Pre-1803 憑證結構。 Azure Stack 1803 及更新版本的資料夾結構為：ACSBlob、ACSQueue、ACSTable，而不是 ACS 資料夾。 如需詳細資訊，請參閱部署文件。

**原因**：CertChecker 偵測到使用單一 ACS 資料夾，這在 1803 之前的部署是正確的。 對於 Azure Stack 第 1803 版及更新版本的部署，資料夾結構變更為 ACSTable、ACSQueue、ACSBlob。 已將 Certchecker 更新為可支援這項功能。

**解析**：如果部署 1802，則不需要任何動作。 如果部署 1803 和更新版本，請將 ACS 取代為 ACSTable、ACSQueue、ACSBlob，並將 ACS 憑證複製到這些資料夾中。

**徵兆**：測試會略過

**原因**：如果不符合相依性，CertChecker 會略過某些測試：
- 如果信任鏈結失敗，則會略過其他憑證。
- 不會略過任何設定檔，如果：
  - 有安全性原則限制建立暫時使用者，並以該使用者身分執行 Powershell。
  - 私密金鑰檢查失敗。

**解析**：遵循每個憑證測試集合下詳細資料區段中的工具指引。


## <a name="prepare-deployment-script-certificates"></a>準備部署指令碼憑證 
在最後一個步驟中，您已備妥的所有憑證都必須放在部署主機上的適當目錄中。 在您的部署主機上，建立名為 Certificates**  的資料夾，並將您的匯出憑證檔案放在[必要憑證](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)一節中指定的對應子資料夾中：

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> 僅當使用 AD FS 作為身分識別儲存時，才需要以星號 * 標示的憑證。


## <a name="next-steps"></a>後續步驟
[資料中心身分識別整合](azure-stack-integrate-identity.md)