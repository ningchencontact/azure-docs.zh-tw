---
title: 適用於 Linux 的 Azure 磁碟加密 | Microsoft Docs
description: 使用虛擬機器擴充功能將 Azure 磁碟加密部署至 Linux 的虛擬機器。
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 05d20e75cf8f0c84936ff4e5dfa42d60678f6ffc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295339"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>適用於 Linux 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概觀

Azure 磁碟加密會使用 Linux 中的 dm-crypt 子系統在[選取的 Azure Linux 發行版本](https://aka.ms/adelinux)上提供完整的磁碟加密。  此解決方案會與 Azure Key Vault 整合，以管理磁碟加密金鑰和祕密。

## <a name="prerequisites"></a>必要條件

如需先決條件的完整清單，請參閱 [Azure 磁碟加密先決條件](
../../security/azure-security-disk-encryption-prerequisites.md)。

### <a name="operating-system"></a>作業系統

Azure 磁碟加密目前在特定的發行版本和版本上受到支援。  請參閱[Azure 磁碟加密支援的作業系統：Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux)如需支援的 Linux 散發套件的清單。

### <a name="internet-connectivity"></a>網際網路連線

適用於 Linux 的 Azure 磁碟加密需要網際網路連線以存取 Active Directory、Key Vault、儲存體和套件管理端點。  如需詳細資訊，請參閱 [Azure 磁碟加密先決條件](../../security/azure-security-disk-encryption-prerequisites.md)。

## <a name="extension-schemata"></a>延伸模組結構描述

有兩個結構描述針對 Azure 磁碟加密： v1.1、 更新、 建議的結構描述，不會使用 Azure Active Directory (AAD) 屬性和 v0.1、 舊版的結構描述需要 AAD 屬性。 您必須使用對應至延伸模組所使用的結構描述版本： AzureDiskEncryptionForLinux 擴充功能版本 1.1 AzureDiskEncryptionForLinux 擴充功能版本 0.1 的結構描述 v0.1 的結構描述 v1.1。
### <a name="schema-v11-no-aad-recommended"></a>結構描述 v1.1:無 AAD （建議選項）

V1.1 結構描述建議，並不需要 Azure Active Directory 屬性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>結構描述 v0.1： 與 AAD 

0\.1 的結構描述需要`aadClientID`任一個`aadClientSecret`或`AADClientCertificate`。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | 字串 |
| type | AzureDiskEncryptionForLinux | 字串 |
| typeHandlerVersion | 0.1, 1.1 | int |
| （0.1 的結構描述）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0.1 schema) AADClientSecret | password | 字串 |
| (0.1 schema) AADClientCertificate | thumbprint | 字串 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | 字串 | 
| KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 字串 |
| KeyEncryptionKeyURL | url | 字串 |
| （選擇性）KeyVaultURL | url | 字串 |
| Passphrase | password | 字串 | 
| SequenceVersion | uniqueidentifier | 字串 |
| VolumeType | 作業系統、資料、全部 | 字串 |

## <a name="template-deployment"></a>範本部署

如需範本部署的範例，請參閱[在執行中的 Linux VM 上啟用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

您可以在最新的 [Azure CLI 文件](/cli/azure/vm/encryption?view=azure-cli-latest)中找到相關指示。 

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

如需疑難排解資訊，請參閱 [Azure 磁碟加密疑難排解指南](../../security/azure-security-disk-encryption-tsg.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟

如需 VM 擴充功能的詳細資訊，請參閱[適用於 Linux 的虛擬機器擴充功能和功能](features-linux.md)。