---
title: 透過 Azure REST API 建立 Linux 虛擬機器 | Microsoft Docs
description: 了解如何透過 Azure REST API 在 Azure 中建立使用受控磁碟與 SSH 驗證的 Linux 虛擬機器。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825969"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>透過 REST API 建立使用 SSH 驗證的 Linux 虛擬機器

Azure 中的虛擬機器 (VM) 是由各種不同的參數所定義，例如位置、硬體大小、作業系統映像，以及登入認證。 本文說明如何使用 REST API 建立使用 SSH 驗證的 Linux 虛擬機器。

若要建立或更新虛擬機器，請使用下列 *PUT* 作業：

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>建立要求

若要建立 *PUT* 要求，`{subscription-id}` 是必要參數。 如果您有多個訂用帳戶，請參閱[使用多個訂用帳戶](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)。 您需針對資源定義 `{resourceGroupName}` 和 `{vmName}`，以及定義 `api-version` 參數。 本文使用 `api-version=2017-12-01`。

以下是必要標頭：

| 要求標頭   | 說明 |
|------------------|-----------------|
| *Content-Type:*  | 必要。 設定為 `application/json`。 |
| *Authorization:* | 必要。 設定為無效的 `Bearer` [存取權杖](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)。 |

如需如何建立要求的詳細資訊，請參閱 [REST API 要求/回應的元件](/rest/api/azure/#components-of-a-rest-api-requestresponse)。

## <a name="create-the-request-body"></a>建立要求本文

以下是用來建立要求本文的常用定義：

| Name                       | 必要 | 類型                                                                                | 說明  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | 字串                                                                              | 資源位置。 |
| name                       |          | 字串                                                                              | 虛擬機器的名稱。 |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | 指定虛擬機器的硬體設定。 |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | 指定虛擬機器磁碟的儲存體設定。 |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | 指定虛擬機器的作業系統設定。 |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | 指定虛擬機器的網路介面。 |

如需要求主文中可用定義的完整清單，請參閱[虛擬機器建立或更新要求本文定義](/rest/api/compute/virtualmachines/createorupdate#definitions)。

### <a name="example-request-body"></a>要求本文範例

下列範例要求本文會定義使用進階受控磁碟的 Ubuntu 18.04-LTS 映像。 它會使用 SSH 公開金鑰驗證，而且 VM 會使用您[先前建立](../../virtual-network/virtual-network-network-interface.md)的現有虛擬網路介面卡 (NIC)。 請在 *osProfile.linuxConfiguration.ssh.publicKeys.keyData* 欄位提供您的 SSH 公開金鑰。 如有需要，您可以[產生 SSH 金鑰組](mac-create-ssh-keys.md)。

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>回應

建立或更新虛擬機器的作業會有兩個成功的回應：

| Name        | 類型                                                                              | 說明 |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 確定          |
| 201 Created | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | 已建立     |

如需 REST API 回應的詳細資訊，請參閱[處理回應訊息](/rest/api/azure/#process-the-response-message)。

### <a name="example-response"></a>範例回應

先前建立 VM 的要求本文範例，所傳回的扼要 *201 Created* 回應顯示已指派 *vmId*，且 *provisioningState* 為 *Creating*：

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>後續步驟

如需有關 Azure REST API 或諸如 Azure CLI 2.0 或 Azure PowerShell 等其他管理工具的詳細資訊，請參閱下列內容：

- [Azure 計算提供者 REST API](/rest/api/compute/)
- [Get started with Azure REST API](/rest/api/azure/) (開始使用 Azure REST API)
- [Azure CLI 2.0](/cli/azure/)
- [Azure PowerShell 模組](/powershell/azure/overview)
