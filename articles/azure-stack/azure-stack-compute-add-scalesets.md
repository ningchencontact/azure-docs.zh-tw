---
title: 在 Azure Stack 中提供虛擬機器擴展集 | Microsoft Docs
description: 了解雲端操作員如何可以將虛擬機器擴展新增至 Azure Stack Marketplace
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 12425ab53ca16bb985a0a8658b5058998565b01a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>在 Azure Stack 中提供虛擬機器擴展集

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

虛擬機器擴展集是 Azure Stack 計算資源。 您可以使用它們來部署和管理一組相同的虛擬機器。 由於所有虛擬機器具有相同的設定，擴展集不需要預先佈建虛擬機器。 您可以更輕鬆地針對大量計算、巨量資料和容器化工作負載，建置大規模服務。

本文引導您完成讓擴展集可在 Azure Stack Marketplace 中使用的程序。 完成此程序之後，您的使用者可以將虛擬機器擴展集新增至其訂用帳戶。

Azure Stack 上的虛擬機器擴展集就像是 Azure 上的虛擬機器擴展集。 如需詳細資訊，請參閱下列視訊：
* [Mark Russinovich 講述 Azure 擴展集](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman 與虛擬機器擴展集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack 上，虛擬機器擴展集不支援自動擴展。 您可以使用 Azure Stack 入口網站、Resource Manager 範本或 PowerShell 將更多執行個體新增至擴展集。

## <a name="prerequisites"></a>先決條件
* **Powershell 和工具**

   安裝和設定適用於 Azure Stack 的 PowerShell 和 Azure Stack 工具。 請參閱[在 Azure Stack 使用 PowerShell 啟動和執行](azure-stack-powershell-configure-quickstart.md)。

   安裝 Azure Stack 工具之後，請確定您匯入下列 PowerShell 模組 (AzureStack-Tools-master 資料夾中 \ComputeAdmin 資料夾的相對路徑)：
  ````PowerShell
        Import-Module .\AzureStack.ComputeAdmin.psm1
  ````

* **作業系統映像**

   如果您尚未新增至您的 Azure Stack Marketplace 作業系統映像，請參閱[Windows Server 2016 VM 映像新增至 Azure Stack Marketplace](azure-stack-add-default-image.md)。

   如需 Linux 支援，請下載 Ubuntu Server 16.04，並將使用 ```Add-AzsPlatformImage``` 搭配下列參數來新增它：```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```。


## <a name="add-the-virtual-machine-scale-set"></a>新增虛擬機器擴展集

為您的環境編輯下列 PowerShell 指令碼，然後執行它以將虛擬機器擴展集新增至您的 Azure Stack Marketplace。 

``$User`` 是您用來連線系統管理員入口網站的帳戶。 例如： serviceadmin@contoso.onmicrosoft.com。

````PowerShell  
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
````

## <a name="update-images-in-a-virtual-machine-scale-set"></a>更新虛擬機器擴展集中的映像 
建立虛擬機器擴展集之後，使用者可以更新擴展集中的映像，而不必重新建立擴展集。 更新映像的程序取決於下列案例：

1. 虛擬機器擴展集部署範本會**指定最新的** version：  

   當 version 在擴展集範本的 imageReference 區段中設定為 **latest** 時，擴展集的相應放大作業會針對擴展集執行個體使用映像可用的最新版本。 相應放大完成之後，您可以刪除較舊的虛擬機器擴展集執行個體。  (publisher、offer 和 sku 的值維持不變)。 

   以下是指定 latest 的範例：  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   您必須先下載新映像，相應放大才可使用這個新映像：  

   - 當 Marketplace 上的映像版本比擴展集中的映像還要新時：下載新映像以取代舊映像。 取代映像之後，使用者可以繼續相應放大。 

   - 當 Marketplace 上的映像版本與擴展集中的映像相同時：刪除擴展集中使用的映像，然後下載新映像。 在移除原始映像與下載新映像之間的這段時間內，您無法相應放大。 
      
     您必須執行此程序，才能重新整合使用疏鬆檔案格式 (在版本 1803 中引進) 的映像。 
 

2. 虛擬機器擴展集部署範本**不會指定最新的** version，而會改為指定一個版本號碼：  

     如果您下載較新版本的映像 (它會變更可用版本)，則無法相應放大擴展集。 這是因為根據設計，擴展集範本中指定的映像版本必須可以使用。  

如需詳細資訊，請參閱[作業系統磁碟和映像](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images)。  


## <a name="remove-a-virtual-machine-scale-set"></a>移除虛擬機器擴展集

若要移除虛擬機器擴充集資源庫項目，請執行下列 PowerShell 命令：

```PowerShell  
    Remove-AzsVMSSGalleryItem
````

> [!NOTE]
> 資源庫項目可能不會立即移除。 您可能需要重新整理入口網站數次，項目才會顯示已從 Marketplace 中移除。

## <a name="next-steps"></a>後續步驟
[Azure Stack 的常見問題集](azure-stack-faq.md)