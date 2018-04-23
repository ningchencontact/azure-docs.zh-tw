---
title: 從本機來源新增 Azure Stack 市集項目 | Microsoft Docs
description: 說明如何將本機作業系統映像新增至 Azure Stack Marketplace。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 815dc055e19a38a61cbb3e927c3d7e92393b17dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>教學課程：從本機來源新增 Azure Stack 市集項目

要讓使用者可以部署虛擬機器 (VM)，身為 Azure Stack 操作人員的您要做的第一件事，就是將 VM 映像新增到 Azure Stack 市集中。 根據預設，系統不會將任何項目發佈至 Azure Stack 市集，但是您可以上傳 VM ISO 映像以提供給使用者使用。 如果您是在中斷連線或連線能力有限的情況下部署 Azure Stack，請使用此選項。

在本教學課程中，您會從 Windows Server 評估版頁面下載 Windows Server 2016 VM 映像，並將其上傳至 Azure Stack 市集。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 新增 Windows Server 2016 VM 映像
> * 確認 VM 映像可供使用 
> * 測試 VM 映像

## <a name="prerequisites"></a>先決條件

若要完成本教學課程：

- 安裝 [Azure Stack 相容的 Azure PowerShell 模組](asdk-post-deploy.md#install-azure-stack-powershell)
- 下載 [Azure Stack 工具](asdk-post-deploy.md#download-the-azure-stack-tools)
- 從 Windows Server 評估版頁面下載 [Windows Server 2016 虛擬機器 ISO 映像](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)

## <a name="add-a-windows-server-vm-image"></a>新增 Windows Server VM 映像
您可以藉由使用 PowerShell 新增先前所下載的 ISO 映像，將 Windows Server 2016 映像發佈至 Azure Stack 市集。 

如果您是在中斷連線或連線能力有限的情況下部署 Azure Stack，請使用此選項。

1. 使用下列命令，匯入 Azure Stack 工具目錄中所包含的 Azure Stack `Connect` 和 `ComputeAdmin` PowerShell 模組：

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. 根據您是使用 Azure Active Directory (Azure AD) 或 Active Directory 同盟服務 (AD FS) 來部署您的 Azure Stack 環境，在您的 ASDK 主機電腦上執行下列其中一個指令碼：

  - **Azure AD 部署**的命令： 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - **AD FS 部署**的命令：
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Connect-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. 將 Windows Server 2016 映像新增到 Azure Stack 市集。 (以您下載的 Windows Server 2016 ISO 路徑取代 *fully_qualified_path_to_ISO*。)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>確認市集項目是否可用
請使用下列步驟，確認新的 VM 映像在 Azure Stack 市集項目中是否可用。

1. 登入 [ASDK 管理入口網站](https://adminportal.local.azurestack.external)。

2. 選取 [更多服務] > [Marketplace 管理]。 

3. 確認已成功新增 Windows Server 2016 Datacenter VM 映像。

   ![從 Azure 下載的映像](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>測試 VM 映像
身為 Azure Stack 操作人員，您可以使用[管理員入口網站](https://adminportal.local.azurestack.external)來建立測試 VM，驗證映像已成功變為可用。 

1. 登入 [ASDK 管理入口網站](https://adminportal.local.azurestack.external)。

2. 按一下 [新增] > [計算] > [Windows Server 2016 Datacenter] > [建立]。  
 ![從市集映像建立虛擬機器](media/asdk-marketplace-item/new-compute.png)

3. 請在 [基本] 刀鋒視窗中，輸入下列資訊，然後按一下 [確定]：
  - **名稱**：test-vm-1
  - **使用者名稱**：AdminTestUser
  - **密碼**：AzS-TestVM01
  - **訂用帳戶**：接受預設的提供者訂用帳戶
  - **資源群組**：test-vm-rg
  - **位置**：本機

4. 在 [選擇大小] 刀鋒視窗中，按一下 [A1 標準]，再按一下 [選取]。  

5. 在 [設定] 刀鋒視窗中，接受預設值並按一下 [確定]

6. 在 [摘要] 刀鋒視窗中，按一下 [確定]，以建立虛擬機器。  
> [!NOTE]
> 虛擬機器部署需要數分鐘的時間才能完成。

7. 若要檢視新的虛擬機器，請按一下 [虛擬機器]，然後搜尋 **test-vm-1** 並按一下其名稱。
    ![第一個顯示的測試 VM 映像](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>清除資源
在您成功登入虛擬機器，並確認驗證測試映像可正常運作後，請刪除測試資源群組。 這會釋出可用於單一節點 ASDK 安裝的有限資源。

若不再需要，請遵循下列步驟來移除資源群組和所有相關資源：

1. 登入 [ASDK 管理入口網站](https://adminportal.local.azurestack.external)。
2. 按一下 [資源群組] > [test-vm-rg] > [刪除資源群組]。
3. 鍵入 **test-vm-rg** 作為資源群組名稱，然後按一下 [刪除] 。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 新增 Windows Server 2016 VM 映像
> * 確認 VM 映像可供使用 
> * 測試 VM 映像

前進到下一個教學課程，以了解如何建立 Azure Stack 訂閱詳情和方案。

> [!div class="nextstepaction"]
> [提供 Azure Stack IaaS 服務](asdk-offer-services.md)
