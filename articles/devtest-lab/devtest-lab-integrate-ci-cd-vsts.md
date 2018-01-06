---
title: "將 Azure DevTest Labs 整合到 VSTS 連續整合和傳送管線 |Microsoft 文件"
description: "了解如何將 Azure DevTest Labs 整合到您的 VSTS 連續整合和傳送管線"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: v-craic
ms.openlocfilehash: db2ee6a25626f0a47bf86c5ee286fddc2441d3f8
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>將 Azure DevTest Labs 整合到您的 VSTS 連續整合和傳送管線
您可以使用*Azure DevTest Labs 工作*延伸模組，輕鬆地安裝在 Visual Studio Team Services (VSTS) 來整合您 CI/CD 的組建發行管線與 Azure DevTest Labs。 擴充功能會安裝三項工作： 
* 建立 VM
* 從 VM 建立自訂映像
* 刪除 VM 

處理程序讓您更容易，比方說，快速部署 「 黃金映像 」 特定的測試工作，然後再刪除它，在測試完成時。

這篇文章會示範如何建立和部署的 VM，建立自訂映像，然後再刪除 VM，所有做為一個完整的管線。 您通常會在您自己自訂的建置測試部署管線中個別執行每項工作。

## <a name="before-you-begin"></a>開始之前
整合 Azure DevTest Labs 的 CI/CD 管線之前，您必須從 Visual Studio Marketplace 安裝擴充功能。
1. 移至[Azure 的 DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
1. 選取 [安裝]。
1. 完成精靈。

## <a name="create-a-resource-manager-template"></a>建立資源管理員範本
本章節描述如何建立您用於視需要建立 Azure 虛擬機器的 Azure Resource Manager 範本。

1. 若要建立您的訂用帳戶中的 Resource Manager 範本，完成中的程序[使用資源管理員範本](devtest-lab-use-resource-manager-template.md)。
1. 產生的資源管理員範本之前，先將[WinRM 成品](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)一部分建立 VM。

   WinRM 存取，才能使用部署工作，例如*Azure File Copy*和*目標電腦上的 PowerShell*。

   > [!NOTE]
   > 當您使用 WinRM 與共用的 IP 位址時，您必須新增到 WinRM 連接埠對應的外部連接埠的 NAT 規則。 如果您建立 VM 使用公用 IP 位址，就不需要此步驟。
   >
   >

1. 儲存範本，為您的電腦上的檔案。 將檔案命名**CreateVMTemplate.json**。
1. 簽入原始檔控制系統的範本。
1. 開啟文字編輯器，並貼上下列指令碼。

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

1. 簽入的指令碼，您的原始檔控制系統。 類似名稱**GetLabVMParams.ps1**。

   當代理程式上執行這個指令碼一部分的發行定義中，而且如果您使用工作步驟，例如*Azure File Copy*或*目標電腦上的 PowerShell*，指令碼會收集您需要的值將您的應用程式部署到 VM。 您通常會將應用程式部署至 Azure VM 中使用這些工作。 工作需要 VM 資源群組名稱、 IP 位址和完整的網域名稱 (FDQN)。

## <a name="create-a-release-definition-in-release-management"></a>在 Release Management 中建立的發行定義
若要建立發行定義，執行下列作業：

1. 在**版本** 索引標籤**建置和發行**中樞，選取加號 （+） 按鈕。
2. 在**建立發行定義**視窗中，選取**空**範本，，然後選取**下一步**。
3. 選取**稍後選擇**，然後選取**建立**使用一個預設的環境和任何連結的成品中建立新的發行定義。
4. 若要開啟捷徑功能表，在新發行定義中，選取環境名稱旁邊的省略符號 （...），然後選取**設定變數**。 
5. 在**設定-環境**發行定義的工作，在您使用的變數 視窗，輸入下列值：

   a. 如**vmName**，輸入您在 Azure 入口網站中建立資源管理員範本時，您指派給 VM 的名稱。

   b. 如**userName**，輸入您在 Azure 入口網站中建立資源管理員範本時，您指派給 VM 的使用者名稱。

   c. 如**密碼**，輸入您在 Azure 入口網站中建立資源管理員範本時，您指派給 VM 的密碼。 使用 「 鎖 」 圖示來隱藏和安全密碼。

### <a name="create-a-vm"></a>建立 VM

部署的下一個階段是建立要用於後續部署為 「 黃金映像 」 VM。 您建立的 VM，您可以在您的 Azure DevTest 實驗室執行個體中使用針對此用途特別開發工作。 

1. 在發行定義中，選取**新增工作**。
2. 在**部署**索引標籤上，新增*Azure DevTest Labs 建立 VM*工作。 設定工作，如下所示：

   > [!NOTE]
   > 若要建立要用於後續部署 VM，請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。

   a. 如**Azure RM 訂用帳戶**，選取連線中的**可用的 Azure 服務連線**清單，或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。

   b. 如**實驗室名稱**，選取您稍早建立的執行個體的名稱。

   c. 如**範本名稱**，輸入完整路徑和名稱的範本檔案儲存到您原始程式碼儲存機制。 您可以使用 Release Management 的內建屬性，以簡化路徑，例如：

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. 如**範本參數**，輸入範本中所定義的變數的參數。 使用您定義在環境中，例如變數名稱：

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. 如**輸出變數-實驗室 VM 識別碼**，您需要新建立的 VM 識別碼後續步驟。 設定環境變數，會自動填入此識別碼中的預設名稱**輸出變數**> 一節。 您可以編輯變數，如有必要，但請記得要在後續工作中使用正確的名稱。 實驗室 VM 識別碼會寫入下列形式：

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. 執行您先前收集的詳細資料的 DevTest Labs VM 建立指令碼。 
4. 在發行定義中，選取**新增工作**然後**部署**索引標籤上，新增*Azure PowerShell*工作。 設定工作，如下所示：

   > [!NOTE]
   > 若要收集的 DevTest Labs VM 詳細資料，請參閱[部署： Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell)執行指令碼。

   a. 如**Azure 連接類型**，選取**Azure Resource Manager**。

   b. 如**Azure RM 訂用帳戶**，下方的清單中選取連接**可用的 Azure 服務連線**，或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。

   c. 如**指令碼類型**，選取**指令碼檔案**。
 
   d. 如**指令碼路徑**，輸入完整路徑和指令碼儲存到您原始程式碼儲存機制名稱。 您可以使用 Release Management 的內建屬性，以簡化路徑，例如：
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. 如**指令碼引數**，輸入已自動填入識別碼為實驗室 VM 上一項工作，例如環境變數的名稱： 
      ```
      -labVmId '$(labVMId)'
      ```
    指令碼會收集所需的值，並將它們儲存在發行定義環境變數，因此，您可以輕鬆地參考後續步驟中。

5. 將您的應用程式部署到新的 DevTest Labs VM。 您通常用來部署應用程式的工作是*Azure File Copy*和*目標電腦上的 PowerShell*。
   VM 所需的參數，這些工作的相關資訊會儲存在名為三個組態變數**labVmRgName**， **labVMIpAddress**，和**labVMFqdn**發行定義中。 如果您只想要試驗建立 DevTest Labs VM 和自訂映像，而不將應用程式部署給它，您可以略過此步驟。

### <a name="create-an-image"></a>建立映像

下一個階段是在您的 Azure DevTest Labs 執行個體中建立的新部署的 VM 映像。 您接著可以使用映像視建立 VM 的複本，每當您想要執行的開發工作或執行某些測試。 

1. 在發行定義中，選取**新增工作**。
2. 在**部署**索引標籤上，新增**Azure DevTest Labs 建立自訂映像**工作。 進行下列設定：

   > [!NOTE]
   > 若要建立映像，請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。

   a. 如**Azure RM 訂用帳戶**，請在**可用的 Azure 服務連線**清單中，從清單中，選取連接或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。

   b. 如**實驗室名稱**，選取您稍早建立的執行個體的名稱。

   c. 如**自訂映像名稱**，輸入自訂映像的名稱。

   d. （選擇性）如**描述**，輸入以便於稍後再選取正確的映像的描述。

   e. 如**來源實驗室 VM 來源實驗室 VM 識別碼**，如果您變更預設名稱自動填入的環境變數的識別碼為實驗室 VM 由稍早的工作，這裡加以編輯。 預設值是**$(labVMId)**。

   f. 如**輸出變數的自訂映像識別碼**，當您想要管理或刪除它時，您會需要新建立的映像的識別碼。 預設名稱自動填入此識別碼的環境變數中設定**輸出變數**> 一節。 如有必要，您可以編輯該變數。

### <a name="delete-the-vm"></a>刪除 VM

最後一個階段是刪除您部署 Azure DevTest Labs 執行個體中的 VM。 之後您執行的開發工作，或在已部署的 VM 上執行的測試，您需要您通常會刪除 VM。 

1. 在發行定義中，選取**新增工作**然後**部署**索引標籤上，新增*Azure DevTest Labs 刪除 VM*工作。 進行下列設定：

      > [!NOTE]
      > 若要刪除 VM，請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。

   a. 如**Azure RM 訂用帳戶**，選取連線中的**可用的 Azure 服務連線**清單，或建立更具限制性的權限連接到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[Azure 資源管理員服務端點](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm)。
 
   b. 如**實驗室 VM 識別碼**，如果您變更預設名稱自動填入的環境變數的識別碼為實驗室 VM 由稍早的工作，這裡加以編輯。 預設值是**$(labVMId)**。

2. 為發行定義中，輸入名稱，然後將它儲存。
3. 建立新版本，選取最新的組建，將它部署到單一的環境中定義。

在每個階段中，重新整理您的 DevTest Labs 執行個體，若要檢視 VM 映像，建立並再次刪除 VM 在 Azure 入口網站檢視。

您現在可以使用自訂映像建立 Vm 時所需。


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
* 了解如何[使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
* 瀏覽更多的快速入門資源管理員範本，從 DevTest Labs 自動化[公用的 DevTest Labs GitHub 儲存機制](https://github.com/Azure/azure-quickstart-templates)。
* 如有必要，請參閱[VSTS 疑難排解](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting)頁面。
 
