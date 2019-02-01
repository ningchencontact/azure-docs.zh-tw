---
title: 在 Azure Stack 中搭配 PowerShell 使用 API 版本設定檔 | Microsoft Docs
description: 了解如何在 Azure Stack 中搭配 PowerShell 使用 API 版本設定檔。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: c6bee5c66661f59a6287d624bf5a55428ebbe56e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238110"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>在 Azure Stack 中使用適用於 PowerShell 的 API 版本設定檔

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

API 版本設定檔提供一個管理 Azure 與 Azure Stack 之間版本差異的方式。 API 版本設定檔是一組具有特定 API 版本的 AzureRM PowerShell 模組。 每個雲端平台都有一組支援的 API 版本設定檔。 例如，Azure Stack 支援特定日期的設定檔版本 (例如 **2018-03-01-hybrid**)，而 Azure 則支援**最新的** API 版本設定檔。 當您安裝設定檔時，會安裝與指定的設定檔對應的 AzureRM PowerShell 模組。

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>安裝使用 API 版本設定檔所需的 PowerShell 模組

可透過「PowerShell 資源庫」取得的 **AzureRM.Bootstrapper** 模組會提供使用 API 版本設定檔所需的 PowerShell Cmdlet。 請使用下列 Cmdlet 來安裝 **AzureRM.Bootstrapper** 模組：

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Azure Stack 版本和設定檔版本

下表列出用於 Azure Stack 最新版的必要 API 設定檔版本和 PowerShell 系統管理員模組 moniker。 如果您對於 1808 之前的版本使用本文，請使用正確的值更新您的版本設定檔和 moniker。

| 版本編號 | API 版本設定檔 | PS 管理員模組 moniker |
| --- | --- | --- |
| 1811 或更新版本 | 2018-03-01-hybrid | 1.6.0 |
| 1808 或之後 | 2018-03-01-hybrid | 1.5.0 |
| 1804 或之後 | 2017-03-09-profile | 1.4.0 |
| 1804 之前的版本 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> 若要從 1.2.11 版升級，請參閱[移轉指南](https://aka.ms/azpsh130migration)。

## <a name="install-a-profile"></a>安裝設定檔

使用 **Install-AzureRmProfile** Cmdlet 搭配 **2018-03-01-hybrid** API 版本設定檔，以安裝 Azure Stack 所需的 AzureRM 模組。 Azure Stack 操作員模組並不會隨著這個 API 版本設定檔一起安裝。 您應該如[安裝適用於 Azure Stack 的 PowerShell](../azure-stack-powershell-install.md) 一文的步驟 3 中所指定，個別安裝操作員模組。

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>安裝並匯入設定檔中的模組

使用 **Use-AzureRmProfile** Cmdlet 來安裝並匯入與 API 版本設定檔關聯的模組。 在一個 PowerShell 工作階段中只能匯入一個 API 版本設定檔。 若要匯入不同的 API 版本設定檔，您必須開啟新的 PowerShell 工作階段。 **Use-AzureRMProfile** Cmdlet 會執行下列工作：

1. 檢查目前範圍中是否已安裝與所指定 API 版本設定檔關聯的 PowerShell 模組。  
2. 下載並安裝這些模組 (如果尚未安裝)。
3. 將模組匯入到目前的 PowerShell 工作階段中。

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

若要從某個 API 版本設定檔安裝並匯入選取的 AzureRM 模組，請搭配 **Module** 參數執行 **Use-AzureRMProfile** Cmdlet：

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>取得已安裝的設定檔

使用 **Get-AzureRmProfile** Cmdlet 來取得可用的 API 版本設定檔清單：

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>更新設定檔

使用 **Update-AzureRmProfile** Cmdlet，以將 API 版本設定檔中的模組更新成 PSGallery 中可用的最新版模組。 建議您在匯入模組時，一律在新的 PowerShell 工作階段中執行 **Update-AzureRmProfile** Cmdlet，以避免發生衝突。 **Update-AzureRmProfile** Cmdlet 會執行下列工作：

1. 檢查目前範圍的指定 API 版本設定檔中是否已安裝最新版模組。  
2. 提示您安裝這些模組 (如果尚未安裝)。  
3. 將已更新的模組安裝並匯入到目前的 PowerShell 工作階段中。  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

此 Cmdlet 會執行下列工作：  

1. 檢查目前範圍的指定 API 版本設定檔中是否已安裝最新版模組。  
2. 從目前的 API 版本設定檔並在目前 PowerShell 工作階段中，移除舊版模組。  
3. 提示您安裝最新版本。  
4. 將已更新的模組安裝並匯入到目前的 PowerShell 工作階段中。  

## <a name="uninstall-profiles"></a>將設定檔解除安裝

使用 **Uninstall-AzureRmProfile** Cmdlet 將指定的 API 版本設定檔解除安裝。

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>後續步驟

* [安裝 Azure Stack 適用的 PowerShell](azure-stack-powershell-install.md)
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)  
