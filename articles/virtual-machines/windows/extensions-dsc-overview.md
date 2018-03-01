---
title: "Azure 的預期狀態組態概觀 | Microsoft Docs"
description: "將 Microsoft Azure 擴充功能處理常式用於 PowerShell 預期狀態組態的概觀。 包括必要條件、架構、Cmdlet..."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure 期望狀態組態擴充功能處理常式簡介

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM 代理程式和相關聯的擴充功能是 Microsoft Azure 基礎結構服務的一部分。
VM 擴充功能是可擴充 VM 功能及簡化各種 VM 管理作業的軟體元件。

預期狀態設定擴充功能的主要使用案例是將 VM 啟動到 [Azure 自動化 DSC 服務](../../automation/automation-dsc-overview.md)，以提供包含持續管理 VM 設定和整合其他操作工具 (如 Azure 監視) 的[優點](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service)。

DSC 擴充功能也可獨立於 Azure 自動化 DSC 服務使用，但這是部署期間發生的單一動作，而且除了 VM 本機內以外，沒有任何進行中的報告或設定管理。

本文包含兩種案例的資訊、Azure 自動化上架的 DSC 擴充功能，以及如何使用 DSC 擴充功能作為工具，將設定指派給使用 Azure SDK 的虛擬機器。

## <a name="prerequisites"></a>先決條件

- **本機電腦**：若要與 Azure VM 擴充功能互動，您需要使用 Azure 入口網站或 Azure PowerShell SDK。
- **客體代理程式**：由 DSC 設定所設定的 Azure VM 必須是支援 Windows Management Framework (WMF) 4.0 或更新版本的 OS。 如需所支援 OS 版本的完整清單，請參閱 [DSC 擴充功能版本歷程記錄](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/)頁面。

## <a name="terms-and-concepts"></a>詞彙和概念

本指南假設您已熟悉下列概念︰

- **設定** - DSC 設定文件。
- **節點** - DSC 設定的目標。 在本文件中，「節點」一律是指 Azure VM。
- **設定資料** - 包含設定之環境資料的 .psd1 檔案。

## <a name="architectural-overview"></a>架構概觀

Azure DSC 擴充功能會使用「Azure VM 代理程式」架構來傳遞、套用在 Azure VM 上執行的 DSC 組態，並針對這些組態提出報告。
DSC 擴充功能接受設定文件和一組參數。
如果未提供任何檔案，[預設設定指令碼](###default-configuration-script)會內嵌只用於設定[本機設定管理員](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)內之中繼資料的擴充功能。

第一次呼叫擴充功能時，會使用下列邏輯來安裝某個 Windows Management Framework (WMF) 版本︰

1. 如果 Azure VM 作業系統是 Windows Server 2016，則不採取任何動作。 Windows Server 2016 已安裝最新版的 PowerShell。
2. 如果已指定 `wmfVersion` 屬性，則除非該 WMF 版本與 VM 的作業系統不相容，否則會逕行安裝。
3. 如果未指定 `wmfVersion` 屬性，則會安裝 WMF 的最新適用版本。

安裝 WMF 需要重新開機。
重新開機後，擴充功能會下載 `modulesUrl` 屬性所指定的 .zip 檔案 (如有提供)。
如果此位置在 Azure Blob 儲存體中，您可以在 `sasToken` 屬性中指定 SAS 權杖來存取檔案。
將 .zip 下載並解壓縮之後，系統會執行 `configurationFunction` 中定義的設定函數來產生 MOF 檔案。
接著，擴充功能就會使用產生的 MOF 檔案來執行 `Start-DscConfiguration -Force`。
擴充功能會擷取輸出並寫入 Azure 狀態通道。

### <a name="default-configuration-script"></a>預設設定指令碼

Azure DSC 擴充功能包含預設設定指令碼，可在將 VM 上架到 Azure 自動化 DSC 服務時使用。
指令碼參數會對齊[本機設定管理員](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)的可設定屬性。
指令碼參數[文件記載](extensions-dsc-template.md##default-configuration-script)，可於 [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true) 找到完整的指令碼。

## <a name="dsc-extension-in-arm-templates"></a>ARM 範本中的 DSC 擴充功能

在多數案例中，Azure Resource Manager (ARM) 部署範本是使用 DSC 擴充功能的預期方式。
如需在 ARM 部署範本中包含 DSC 擴充功能的資訊和範例，請參閱專用文件頁面[採用 Azure Resource Manager 範本的預期狀態設定擴充功能](extensions-dsc-template.md)。

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 擴充功能 PowerShell Cmdlet

管理 DSC 擴充功能的 PowerShell Cmdlet 最適合用於互動式疑難排解和資訊收集案例。
這些 Cmdlet 可用來封裝、發佈和監視 DSC 擴充功能部署。
請注意，DSC 擴充功能的 Cmdlet 尚未更新，無法搭配使用[預設設定指令碼](###default-configuration-script)。

`Publish-AzureRMVMDscConfiguration` 會接受組態檔、掃描其中是否有相依的 DSC 資源，然後建立包含了組態及套用組態所需之 DSC 資源的 .zip 檔案。
它也可以使用 `-ConfigurationArchivePath` 參數在本機建立封裝。
否則，它會將 .zip 檔案發佈至 Azure Blob 儲存體，然後以 SAS 權杖加以保護。

對於這個 Cmdlet 所建立的 .zip 檔案，.ps1 組態指令碼位於封存資料夾的根目錄。
資源會將模組資料夾放置在封存資料夾中。

`Set-AzureRMVMDscExtension` 會將 PowerShell DSC 擴充功能所需的設定插入 VM 組態物件中。

`Get-AzureRMVMDscExtension` 會擷取特定 VM 的 DSC 擴充功能狀態。

`Get-AzureRMVMDscExtensionStatus` 會擷取 DSC 擴充功能處理常式所套用 DSC 組態的狀態。
此動作可在單一 VM 或一組 VM 上執行。

`Remove-AzureRMVMDscExtension` 會從指定的虛擬機器移除擴充功能處理常式。
此 Cmdlet「不會」  移除組態、將 WMF 解除安裝或變更虛擬機器上已套用的設定。
它只會移除擴充功能處理常式。 

與 AzureRM DSC 擴充功能 Cmdlet 有關的重要資訊：

- Azure Resource Manager Cmdlet 是同步的。
- ResourceGroupName、VMName、ArchiveStorageAccountName、Version 及 Location 皆為必要參數。
- ArchiveResourceGroupName 是選擇性參數。 當儲存體帳戶所屬的資源群組與建立虛擬機器的資源群組不同時，您可以指定此參數。
- AutoUpdate 參數讓擴充功能處理常式能在有最新版本時自動更新。 請注意，當新版本的 WMF 發行時，此參數有可能會導致 VM 重新啟動。

### <a name="getting-started-with-cmdlets"></a>開始使用 Cmdlet

Azure DSC 擴充功能可在部署期間直接使用 DSC 組態文件來設定 Azure VM，但這不會將節點登錄至 Azure 自動化，所以節點「不會」集中管理。

以下是簡單的組態範例。
以 "IisInstall.ps1" 的名稱將它儲存在本機：

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

下列步驟會將 IisInstall.ps1 指令碼置於指定的 VM 上、執行組態，然後回報狀態。

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Azure 入口網站功能

瀏覽至 VM。 在 [設定] -> [一般] 底下，按一下 [擴充功能]。
系統會建立新窗格。
按一下 [加入]，然後選取 [PowerShell DSC]。

入口網站需要輸入。
**組態模組或指令碼**：此為必要欄位 (此表單尚未針對[預設組態指令碼](###default-configuration-script)更新)。
需要一個包含組態指令碼的 .ps1 檔案，或一個 .ps1 組態指令碼位於根目錄而所有相依資源位於模組資料夾內的 .zip 檔案。
您可以使用 Azure PowerShell SDK 隨附的 `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` Cmdlet 來建立此檔案。
系統會將 .zip 檔案上傳到受 SAS 權杖保護的使用者 Blob 儲存體中。

**組態資料 PSD1 檔案**︰這是選擇性欄位。
如果您的組態要求的是在 .psd1 中的組態資料檔，請使用此欄位來選取檔案，然後將它上傳到受 SAS 權杖保護的使用者 Blob 儲存體。

**組態的模組完整名稱**：.ps1 檔案可以有多個組態函數。
請輸入組態 .ps1 指令碼的名稱，後面加上 '\' 和組態函數的名稱。
例如，如果 .ps1 指令碼的名稱為 "configuration.ps1"，而組態為 "IisInstall"，您應輸入： `configuration.ps1\IisInstall`

**組態引數**︰如果組態函數接受引數，請以 `argumentName1=value1,argumentName2=value2` 格式在這裡輸入引數。
請注意，此格式與透過 PowerShell Cmdlet 或 Resource Manager 範本接受組態引數的方式所採用的格式不同。

## <a name="logging"></a>記錄
記錄檔位於︰

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>後續步驟
如需有關 PowerShell DSC 的詳細資訊，請 [瀏覽 PowerShell 文件中心](https://msdn.microsoft.com/powershell/dsc/overview)。 

查看 [適用於 DSC 擴充功能的 Azure Resource Manager 範本](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

若要尋找您可以使用 PowerShell DSC 來管理的其他功能，請 [瀏覽 PowerShell 資源庫](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) 以取得更多 DSC 資源。

如需有關將敏感性參數傳遞到組態中的詳細資訊，請參閱 [使用 DSC 擴充功能處理常式安全地管理認證](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
