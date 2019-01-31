---
title: 為 Azure Stack 使用者啟用 Azure CLI | Microsoft Docs
description: 了解如何使用跨平台命令列介面 (CLI) 在 Azure Stack 上管理及部署資源
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 01/15/2019
ms.author: mabrigg
ms.lastreviewed: 01/15/2019
ms.openlocfilehash: 0d6a93b396e90f3bc27a1eb879556d774075c86f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243295"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>為 Azure Stack 使用者啟用 Azure CLI

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以對 Azure Stack 的使用者提供 CA 根憑證，使其可以在其開發電腦上使用 Azure CLI。 使用者需有該憑證才能透過 CLI 管理資源。

 - **Azure Stack CA 根憑證** - 如果使用者是從位於 Azure Stack 開發套件外的工作站使用 CLI，就需要此根憑證。  

 - **虛擬機器別名端點** - 此端點提供一個在部署 VM 時，以單一參數參考映像發行者、供應項目、SKU 及版本的別名 (例如 "UbuntuLTS" 或 "Win2012Datacenter")。  

下列各節會說明如何取得這些值。

## <a name="export-the-azure-stack-ca-root-certificate"></a>匯出 Azure Stack CA 根憑證

如果您使用整合式系統，則不需要匯出 CA 根憑證。 您需要在 Azure Stack 開發套件 (ASDK) 上匯出 CA 根憑證。

若要以 PEM 格式匯出 ASDK 根憑證，請登入並執行下列指令碼：

```powershell
$label = "<Your Azure Stack CA root certificate name>"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>設定虛擬機器別名端點

Azure Stack 操作員應該設定裝載虛擬機器別名檔案的可公開存取端點。 虛擬機器別名檔案是為映像提供通用名稱的 JSON 檔案。 您會在部署 VM 作為 Azure CLI 參數時使用此名稱。  

在將項目新增到別名檔案之前，確定您是[從 Azure Marketplace 下載映像](azure-stack-download-azure-marketplace-item.md)，或[已發佈自己的自訂映像](azure-stack-add-vm-image.md)。 如果您發行自訂映像，請記下您在發行時所指定的發行者、供應項目、SKU 及版本資訊。 如果映像來自 Marketplace，則可以使用 ```Get-AzureVMImage``` Cmdlet 來檢視資訊。  

有一個包含許多常見映像別名的[別名檔案範例](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)可供使用。 您可使用該範例作為起點。 將此檔案裝載在 CLI 用戶端能夠存取的空間中。 其中一個做法是將檔案裝載在 Blob 儲存體帳戶中，然後與您的使用者分享該 URL：

1. 從 GitHub 下載[範例檔案](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json)。
2. 在 Azure Stack 中建立儲存體帳戶。 完成時，建立 Blob 容器。 將存取原則設定為「公用」。  
3. 將 JSON 檔案上傳到新的容器。 完成時，您可以檢視 Blob 的 URL。 選取 Blob 名稱，然後選取 Blob 屬性中的 URL。

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 部署範本](azure-stack-deploy-template-command-line.md)
- [使用 PowerShell 連線](azure-stack-connect-powershell.md)
- [管理使用者權限](azure-stack-manage-permissions.md)
