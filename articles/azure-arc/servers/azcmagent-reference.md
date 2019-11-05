---
title: Azure 連線的機器代理程式 CLI 介面
description: Azure 已連線機器代理程式 CLI 的參考檔
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513194"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure 連線的機器代理程式 CLI 介面

`Azcmagent` （Azure 連線機器代理程式）工具可用來設定和疑難排解 Azure 的非 azure 機器連線。

代理程式本身是在 Linux 上稱為 `himdsd` 的背景工作進程，以及 Windows 上稱為 `himds` 的 Windows 服務。

在正常使用方式下，`azcmagent connect` 是用來建立這部電腦與 Azure 之間的連線，而且如果您決定不再需要該連線，`azcmagent disconnect`。 其他命令則適用于疑難排解或其他特殊情況。

## <a name="options"></a>選項

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>另請參閱

* [azcmagent connect](#azcmagent-connect) -將這部機器連接到 Azure
* [azcmagent 中斷](#azcmagent-disconnect)連線-中斷這部電腦與 Azure 的連線
* [azcmagent 重新](#azcmagent-reconnect)連線-將這部機器重新連接至 Azure
* [azcmagent show](#azcmagent-show) -取得電腦中繼資料和代理程式狀態。 這主要適用于疑難排解。
* [azcmagent 版本](#azcmagent-version)-顯示混合式管理代理程式版本

## <a name="azcmagent-connect"></a>azcmagent connect

將這部電腦連接到 Azure

### <a name="synopsis"></a>摘要

在 Azure 中建立代表這部電腦的資源。

這會使用提供的驗證選項，在代表這部電腦的 Azure Resource Manager 中建立資源。 資源位於要求的訂用帳戶和資源群組中，而電腦的相關資料則儲存在 location 參數所指定的 Azure 區域中。
預設的資源名稱是這部電腦的主機名稱（如果未覆寫的話）。

接著會下載與系統指派的此電腦身分識別對應的憑證，並儲存在本機。 此步驟完成後， **Azure 連線的電腦中繼資料**服務和來賓設定代理程式會開始與 azure 雲端進行同步處理。

驗證選項：

* 存取權杖 `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* 服務主體識別碼和密碼 `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* 裝置登入（互動式） `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>語法

```none
azcmagent connect [flags]
```

### <a name="options"></a>選項

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent 中斷連線

中斷這部電腦與 Azure 的連線

### <a name="synopsis"></a>摘要

刪除 Azure 中代表此伺服器的資源。

此命令會使用提供的驗證選項來移除代表這部電腦的 Azure Resource Manager 資源。 在此時間點之後， **Azure 連線的機器 Metadata Service**和來賓設定代理程式將會中斷連線。 此命令不會停止或移除服務：移除封裝，以便執行該動作。

此命令所需的許可權高於「Azure 已連線的電腦上線」角色。

一旦電腦中斷連線，如果您想要在 Azure 中為其建立新的資源，請使用 `azcmagent connect`，而不是 `azcmagent reconnect`。

驗證選項：

* 存取權杖 `azcmagent disconnect --access-token <>`
* 服務主體識別碼和密碼 `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 互動式裝置登入 `azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>語法

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>選項

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent 重新連接

將這部機器重新連接至 Azure

### <a name="synopsis"></a>摘要

將具有無效認證的電腦重新連線至 Azure。

如果電腦已有 Azure 中的資源，但無法對其進行驗證，則可以使用此命令重新連接。 如果電腦已關閉一段足夠的時間讓其憑證過期（至少45天），就可能發生這種情況。

如果電腦與 `azcmagent disconnect`中斷連線，請改用 `azcmagent connect`。

此命令會使用提供的驗證選項來抓取對應于代表這部電腦之 Azure Resource Manager 資源的新認證。

此命令所需的許可權高於**Azure 連線的電腦**上線角色。

驗證選項

* 存取權杖 `azcmagent reconnect --access-token <>`
* 服務主體識別碼和密碼 `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 互動式裝置登入 `azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>語法

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>選項

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>azcmagent show

取得電腦中繼資料和代理程式狀態。 這主要適用于疑難排解。

### <a name="synopsis"></a>摘要

取得電腦中繼資料和代理程式狀態。 這主要適用于疑難排解。


### <a name="syntax"></a>語法

```
azcmagent show [flags]
```

### <a name="options"></a>選項

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent 版本

顯示混合式管理代理程式版本

### <a name="synopsis"></a>摘要

顯示混合式管理代理程式版本

### <a name="syntax"></a>語法

```none
azcmagent version [flags]
```

### <a name="options"></a>選項

```none
  -h, --help   help for version
```
