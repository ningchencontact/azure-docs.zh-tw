---
title: 建立和設定 Azure 磁碟加密的金鑰保存庫
description: 本文提供建立和設定金鑰保存庫以搭配使用的步驟 Azure 磁碟加密
ms.service: virtual-machine-scale-sets
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: e4f6dc068969efd3f66e6808531594ed4063347f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530871"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>建立和設定 Azure 磁碟加密的金鑰保存庫

Azure 磁碟加密使用 Azure Key Vault 來控制及管理磁片加密金鑰和密碼。  如需金鑰保存庫的詳細資訊，請參閱[開始使用 Azure Key Vault](../key-vault/key-vault-get-started.md) 和[保護金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。

建立和設定與 Azure 磁碟加密搭配使用的金鑰保存庫包含三個步驟：

1. 建立資源群組（如有需要）。
2. 建立金鑰保存庫。 
3. 設定金鑰保存庫的先進存取原則。

下列快速入門將說明這些步驟：

如果您想要的話，也可以產生或匯入金鑰加密金鑰（KEK）。

## <a name="install-tools-and-connect-to-azure"></a>安裝工具並連接至 Azure

您可以使用[Azure CLI](/cli/azure/)、 [Azure PowerShell Az 模組](/powershell/azure/overview)或[Azure 入口網站](https://portal.azure.com)來完成這篇文章中的步驟。

雖然可以透過瀏覽器存取入口網站，但 Azure CLI 和 Azure PowerShell 需要本機安裝。

### <a name="connect-to-your-azure-account"></a>連線至您的 Azure 帳戶

使用 Azure CLI 或 Azure PowerShell 之前，您必須先連接到您的 Azure 訂用帳戶。 若要這麼做，請[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)、[使用 Azure Powershell 登入](/powershell/azure/authenticate-azureps?view=azps-2.5.0)，或在出現提示時提供您的認證給 Azure 入口網站。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>後續步驟

- [Azure 磁碟加密概觀](disk-encryption-overview.md)
- [使用 Azure CLI 加密虛擬機器擴展集](disk-encryption-cli.md)
- [使用 Azure PowerShell 加密虛擬機器擴展集](disk-encryption-powershell.md)