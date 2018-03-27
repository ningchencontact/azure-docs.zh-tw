---
title: 從 Azure 新增 Azure Stack 市集項目 | Microsoft Docs
description: 說明如何將以 Azure 為基礎的 Windows Server 虛擬機器映像，新增到 Azure Stack Marketplace 中。
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
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>教學課程：從 Azure 新增 Azure Stack 市集項目

要讓使用者可以部署虛擬機器 (VM)，身為 Azure Stack 操作人員的您要做的第一件事，就是將 VM 映像新增到 Azure Stack 市集中。 依預設不會將任何項目發佈到 Azure Stack 市集中，但您可以從[專業處理過的 Azure 市集項目清單](.\.\azure-stack-marketplace-azure-items.md)下載項目；該清單中的項目經過預先測試，可在 Azure Stack 上執行。 如果您是在已連線的情況下進行操作，並且已向 Azure 註冊 Azure Stack 執行個體，請使用此選項。

在本教學課程中，您要將 Windows Server 2016 VM 映像，從 Azure 市集新增到 Azure Stack 市集中。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 新增 Windows Server VM Azure Stack 市集項目
> * 確認 VM 映像可供使用 
> * 測試 VM 映像

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>先決條件

若要完成本教學課程：

- 安裝 [Azure Stack 相容的 Azure PowerShell 模組](asdk-post-deploy.md#install-azure-stack-powershell)
- 下載 [Azure Stack 工具](asdk-post-deploy.md#download-the-azure-stack-tools)
- 使用您的 Azure 訂用帳戶[註冊 ASDK](asdk-register.md)

## <a name="add-a-windows-server-vm-image"></a>新增 Windows Server VM 映像
您可以從 Azure 市集下載 Windows Server 2016 VM 映像，將其新增到 Azure Stack 市集中。 如果您是在已連線的情況下進行操作，並且已向 Azure [註冊 Azure Stack 執行個體](asdk-register.md)，請使用此選項。

1. 登入 [ASDK 管理入口網站](https://adminportal.local.azurestack.external)。

2. 按一下 [更多服務] > [Marketplace 管理] > [從 Azure 新增]。 

   ![從 Azure 新增](media/asdk-marketplace-item/azs-marketplace.png)

3. 尋找或搜尋 **Windows Server 2016 Datacenter** 映像。

4. 選取 **Windows Server 2016 Datacenter** 映像，然後選取 [下載]。

   ![從 Azure 下載映像](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> 下載並將 VM 映像發佈到 Azure Stack 市集大約需要一小時的時間。 

下載完成後，系統便會發佈映像，並可在 [Marketplace 管理] 下使用映像。 該映像也可以在 [計算] 中取得，並用來建立新的虛擬機器。

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
> * 新增 Windows Server VM Azure Stack 市集項目
> * 確認 VM 映像可供使用 
> * 測試 VM 映像

前進到下一個教學課程，以了解如何建立 Azure Stack 訂閱詳情和方案。

> [!div class="nextstepaction"]
> [提供 Azure Stack IaaS 服務](asdk-offer-services.md)