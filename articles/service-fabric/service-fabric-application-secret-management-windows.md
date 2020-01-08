---
title: 在 Windows 叢集上設定加密憑證
description: 了解如何設定加密憑證，並在 Windows 叢集上將祕密加密。
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d9413a37be221adc375836719dc1f467a5571fa0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610177"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>設定加密憑證，並在 Windows 叢集上將祕密加密
本文書名如何設定加密憑證，並在 Windows 叢集上將祕密加密。 針對 Linux 叢集，請參閱[設定加密憑證和在 linux 叢集上將秘密加密。][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started]在這裡用來作為憑證的安全儲存位置，以及取得在 Azure 中的 Service Fabric 叢集上安裝憑證的方法。 如果您沒有要部署至 Azure，您不需要使用金鑰保存庫管理 Service Fabric 應用程式中的密碼。 不過，應用程式中的密碼「使用」 是由平台驗證，讓應用程式可部署至裝載在任何位置的叢集。 

## <a name="obtain-a-data-encipherment-certificate"></a>取得資料加密憑證
資料加密憑證是嚴格用來加密和解密服務之設定中的[參數][parameters-link]。 xml 和服務 ServiceManifest 中的[環境變數][environment-variables-link]。 它不是用來驗證或簽署密碼文字。 憑證必須符合下列要求：

* 憑證必須包含私密金鑰。
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
* 憑證的金鑰使用法必須包含資料編密 (10)，而且不應該包含伺服器驗證或用戶端驗證。 
  
  例如，當使用 PowerShell 建立自我簽署的憑證時，`KeyUsage` 旗標必須設定為 `DataEncipherment`：
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>在叢集中安裝憑證
此憑證必須安裝在叢集中的每個節點上。 如需安裝指示，請參閱[如何使用 Azure Resource Manager 建立][service-fabric-cluster-creation-via-arm]叢集。 

## <a name="encrypt-application-secrets"></a>加密應用程式密碼
下列 PowerShell 命令會用來加密密碼。 此命令只會將值加密；並**不會**簽署密碼文字。 您必須使用安裝在叢集中相同的編密憑證，以產生密碼值的加密文字︰

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

產生的 Base-64 編碼字串同時包含密碼的加密文字，以及用來對其加密的憑證相關資訊。

## <a name="next-steps"></a>後續步驟
瞭解如何[在應用程式中指定加密的秘密。][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
