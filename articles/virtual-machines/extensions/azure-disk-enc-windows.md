---
title: 適用於 Windows 的 Azure 磁碟加密 | Microsoft Docs
description: 使用虛擬機器擴充功能將 Azure 磁碟加密部署至 Windows 虛擬機器。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 11394f692765cc1df5db0eb5c0dd06425026505d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092636"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>適用於 Windows 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>總覽

Azure 磁碟加密會利用 BitLocker 在執行 Windows 的 Azure 虛擬機器上提供完整的磁碟加密。  此解決方案與 Azure Key Vault 整合，可讓您管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 

## <a name="prerequisites"></a>必要條件

如需先決條件的完整清單，請參閱 [Azure 磁碟加密先決條件](
../../security/azure-security-disk-encryption-prerequisites.md)。

### <a name="operating-system"></a>作業系統

如需目前的 Windows 版本清單，請參閱 [Azure 磁碟加密先決條件](../../security/azure-security-disk-encryption-prerequisites.md)。

### <a name="internet-connectivity"></a>網際網路的連線能力

Azure 磁碟加密需要網際網路連線以存取 Active Directory、Key Vault、儲存體和套件管理端點。  如需網路安全性設定的詳細資訊，請參閱 [Azure 磁碟加密先決條件](
../../security/azure-security-disk-encryption-prerequisites.md)。

## <a name="extension-schemata"></a>延伸模組架構

Azure 磁碟加密有兩個架構: v1.1, 這是較新的建議架構, 不會使用 Azure Active Directory (AAD) 屬性和 v 0.1, 這是需要 AAD 屬性的較舊架構。 您必須使用對應至您所使用之延伸模組的架構版本: 架構 v1.1 (適用于 AzureDiskEncryption 延伸模組版本 1.1)、架構 v 0.1 (AzureDiskEncryption 擴充功能版本 0.1)。

### <a name="schema-v11-no-aad-recommended"></a>架構 v1.1:無 AAD (建議)

建議使用 v1.1 架構, 而且不需要 Azure Active Directory 的屬性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>架構 v 0.1: 使用 AAD 

0\.1 架構需要`aadClientID` `aadClientSecret`和或`AADClientCertificate`。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

使用 `AADClientCertificate`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>屬性值

| Name | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1、1。1 | ssNoversion |
| (0.1 架構)AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 架構)AADClientSecret | password | string |
| (0.1 架構)AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| (選擇性) Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | 作業系統、資料、全部 | string |

## <a name="template-deployment"></a>範本部署
如需範本部署的範例，請參閱[從資源庫映像建立新的加密 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

您可以在最新的 [Azure CLI 文件](/cli/azure/vm/encryption?view=azure-cli-latest)中找到相關指示。 

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

請參閱 [Azure 磁碟加密疑難排解指南](../../security/azure-security-disk-encryption-tsg.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Windows 功能](features-windows.md)。
