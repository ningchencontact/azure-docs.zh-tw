---
title: "將 Azure DevTest Labs 整合到 VSTS 連續整合和傳送管線 |Microsoft 文件"
description: "了解如何將 Azure DevTest Labs 整合到您的 VSTS 連續整合和傳送管線"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: tarcher
ms.openlocfilehash: 3cd6939e890f59e7c5b188edd8620da5c9fcc935
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>將 Azure DevTest Labs 整合到您的 VSTS 連續整合和傳送管線
您可以使用**Azure DevTest Labs 工作擴充**安裝在 Visual Studio Team Services (VSTS) 來輕鬆地整合 CI/CD 組建，並釋放具有 Azure DevTest Labs 管線。 安裝三個工作建立 VM、 建立自訂映像從 VM，並刪除 VM 擴充功能。 此程序讓您更容易，例如，快速部署 「 黃金映像 」 特定的測試工作，然後在測試完成時將它刪除。

這篇文章會示範如何建立及部署 VM、 建立自訂映像，然後刪除 VM，所有做為一個完整的管線。 一般而言，不過，您會使用工作個別在您自己自訂的建置測試部署管線。

## <a name="before-you-begin"></a>開始之前
您可以整合 Azure DevTest Labs CI/CD 管線之前，您必須先安裝擴充功能，從 Visual Studio Marketplace。
1. 移至[Azure 的 DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
1. 選擇 [安裝]
1. 完成精靈

## <a name="create-a-resource-manager-template"></a>建立資源管理員範本
本章節描述如何建立您用於視需要建立 Azure 虛擬機器的 Azure Resource Manager 範本。

1. 請依照下列步驟[使用資源管理員範本](devtest-lab-use-resource-manager-template.md)您訂用帳戶中建立資源管理員範本。
1. 新增[WinRM 成品](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)建立 VM （之前產生的資源管理員範本） 的一部分。

   WinRM 存取，才能使用這類部署工作**Azure File Copy**和**目標電腦上的 PowerShell**。

   > [!NOTE]
   > 搭配使用時 WinRM 共用 IP 位址，您必須新增至 WinRM 連接埠對應的外部連接埠的 NAT 規則。 如果在建立 VM 使用公用 IP 位址，這是不需要。
   >
   >

1. 儲存範本，為您的電腦上的檔案。 將檔案命名**CreateVMTemplate.json**。
1. 簽入原始檔控制系統中的範本。
1. 開啟文字編輯器，並將下列指令碼複製到其中。

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. 簽入原始檔控制系統中的指令碼。 類似的項目名稱**GetLabVMParams.ps1**。

   此指令碼，當執行代理程式上的發行定義中，一部分會收集您需要將您的應用程式部署至 VM，如果您使用工作步驟，例如值**Azure File Copy**或**目標電腦上的 PowerShell**。 您通常會將應用程式部署到 Azure VM，使用這些工作，它們需要 VM 資源群組名稱、 IP 位址和完整的網域名稱 (FDQN)。

## <a name="create-the-release-definition-in-release-management"></a>在 Release Management 中建立發行定義
執行下列步驟來建立發行定義。

1. 開啟**版本**] 索引標籤**建置和發行**集線器，然後選擇 ["**+**」 圖示。
1. 在**建立發行定義**對話方塊中，選取**空**範本，然後選擇 **下一步**。
1. 在下一個頁面中，選取**稍後選擇**，然後選擇 **建立**使用一個預設的環境和任何連結的成品中建立新的發行定義。
1. 在新的發行定義中，選擇開啟捷徑功能表，然後選取的環境名稱旁的省略符號 （...）**設定變數**。 
1. 在**設定-環境** 對話方塊中，輸入您的發行定義的工作中使用的變數的下列值：
   - **vmName**： 輸入您在 Azure 入口網站中建立資源管理員範本時，您指派給 VM 的名稱。
   - **使用者名稱**： 輸入您在 Azure 入口網站中建立資源管理員範本時，您指派給 VM 的使用者名稱。
   - **密碼**： 輸入您在 Azure 入口網站中建立資源管理員範本時，您指派給 VM 的密碼。 使用 「 鎖 」 圖示來隱藏和安全密碼。

   <a name="create-a-vm"></a>建立 VM
   ---

   此部署中的第一個階段是建立要用於後續部署為 「 黃金映像 」 VM。 在您的 Azure DevTest 實驗室執行個體使用特別針對此用途開發工作中建立 VM。 在發行定義中，選取**+ 加入工作**並加入**Azure DevTest Labs 建立 VM**工作從 [部署] 索引標籤。進行下列設定：

   請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)來建立可用於後續部署 VM。
   - **Azure RM 訂用帳戶**： 在清單中選取連接**可用的 Azure 服務連線**或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。
   - **實驗室名稱**： 選取您稍早建立的執行個體的名稱。
   - **範本名稱**： 輸入完整路徑和名稱的範本檔案儲存到來源的程式碼儲存機制。 您可以使用 Release Management 的內建屬性，以簡化路徑，例如：
      ```
      $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
      ```
   - **範本參數**： 輸入參數，在範本中定義的變數。 使用您所定義的變數在環境中，例如名稱：
      ```
      -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
      ```
   - **輸出變數-實驗室 VM 識別碼**： 後續步驟所需的新建立的 VM 識別碼。 預設名稱自動填入此識別碼的環境變數中設定**輸出變數**> 一節。 您可以編輯變數，如有必要，但請記得要在後續工作中使用正確的名稱。 實驗室 VM 識別碼的格式為：
      ```
      /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
      ```
1. 執行您先前收集的詳細資料的 DevTest Labs VM 建立指令碼。 在發行定義中，選取**+ 加入工作**並加入**Azure PowerShell**工作從**部署** 索引標籤。設定工作，如下所示：

   請參閱[部署： Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell)執行指令碼來收集 DevTest Labs VM 的詳細資料。

   - **Azure 連接類型**: Azure 資源管理員。
   - **Azure RM 訂用帳戶**： 在清單中選取連接**可用的 Azure 服務連線**或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。
   - **指令碼類型**： 指令碼檔案。
   - **指令碼路徑**： 輸入完整路徑和儲存到來源的程式碼儲存機制的指令碼名稱。 您可以使用 Release Management 的內建屬性，以簡化路徑，例如：
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   - **指令碼引數**： 輸入做為指令碼引數的環境變數所自動填入實驗室 VM 識別碼的前一個工作，例如名稱： 
      ```
      -labVmId '$(labVMId)'
      ```
   指令碼會收集所需的值，並將它們儲存在發行定義環境變數，因此，您可以輕鬆地參考後續工作步驟中。

1. 現在您可以在新的 DevTest Labs vm 部署您的應用程式。 通常用於部署的工作是**Azure File Copy**和**目標電腦上的 PowerShell**。
   - VM 所需的參數，這些工作的相關資訊會儲存在名為三個組態變數**labVmRgName**， **labVMIpAddress**，和**labVMFqdn**發行定義中。
   - 如果您只想要試驗建立 DevTest Labs VM 和自訂映像，而不將應用程式部署到它，您可以略過此步驟。

   <a name="create-an-image"></a>建立映像
   ---

   下一個階段是在您的 Azure DevTest Labs 執行個體中建立的新部署的 VM 映像。 您接著可以使用此映像視情況下，建立 VM 的複本，每當您想要執行的開發工作或執行某些測試。 在發行定義中，選取**+ 加入工作**並加入**Azure DevTest Labs 建立自訂映像**工作從**部署** 索引標籤。進行下列設定：

   請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)建立映像。
   - **Azure RM 訂用帳戶**： 在清單中選取連接**可用的 Azure 服務連線**或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。
   - **實驗室名稱**： 選取您稍早建立的執行個體的名稱。
   - **自訂映像名稱**： 輸入自訂映像的名稱。
   - **描述**： 選擇性地輸入描述，以便於稍後再選取正確的映像。
   - **來源實驗室 VM-來源實驗室 VM 識別碼**： 如果您變更預設名稱自動填入的環境變數的識別碼為實驗室 VM 由稍早的工作，進行編輯這裡。 預設值是*$(labVMId)*。
   - **輸出變數的自訂映像識別碼**： 當您想要管理或刪除它時，您會需要新建立的映像的識別碼。 預設名稱自動填入此識別碼的環境變數中設定**輸出變數**> 一節。 如有必要，您可以編輯該變數。
   
   <a name="delete-the-vm"></a>刪除 VM
   ---
   
   在此範例中的最後階段，就是刪除您部署 Azure DevTest Labs 執行個體中的 VM。 一般而言，您執行的開發工作，或在已部署的 VM 上執行您所需要的測試之後刪除 VM。 在發行定義中，選取**+ 加入工作**並加入**Azure DevTest Labs 刪除 VM**工作從**部署** 索引標籤。進行下列設定：

   請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)刪除 VM。
   - **Azure RM 訂用帳戶**： 在清單中選取連接**可用的 Azure 服務連線**或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。
   - **實驗室 VM 識別碼**： 如果您變更預設名稱自動填入的環境變數的識別碼為實驗室 VM 由稍早的工作，進行編輯這裡。 預設值是*$(labVMId)*。
1. 輸入發行定義的名稱，並將它儲存。
1. 建立新版本，選取最新的組建，將它部署到單一的環境中定義。

在每個階段中，重新整理您的 VM 與正在建立的映像，請參閱 < 在 Azure 入口網站中的 DevTest Labs 執行個體與要一次刪除 VM 的檢視。

您現在可以使用自訂映像建立 Vm 時所需。


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
* 了解如何[使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
* 瀏覽更多的快速入門資源管理員範本，從 DevTest Labs 自動化[公用的 DevTest Labs GitHub 儲存機制](https://github.com/Azure/azure-quickstart-templates)。
* 如有必要，請參閱[VSTS 疑難排解](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting)頁面。
