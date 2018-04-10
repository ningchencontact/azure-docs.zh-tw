---
title: 在 Azure Stack 中建立供應項目 | Microsoft Docs
description: 身為雲端系統管理員，您應該了解如何在 Azure Stack 中為使用者建立供應項目。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中建立優惠

[供應項目](azure-stack-key-features.md)是一組提供者供使用者購買或訂閱的一或多個方案。 此文件將說明如何建立一個包含您在上一個步驟中[所建立方案](azure-stack-create-plan.md)的供應項目。 此供應項目可讓訂閱者佈建虛擬機器。

1. 登入 Azure Stack 系統管理員入口網站 (https://adminportal.local.azurestack.external)，然後按一下 [新增] > [租用戶供應項目 + 方案] > [供應項目]。

   ![](media/azure-stack-create-offer/image01.png)
2. 在 [新增供應項目] 窗格中，填寫 [顯示名稱] 與 [資源名稱]，然後選取新的或現有的 [資源群組]。 [顯示名稱] 是易記的供應項目名稱。 此易記名稱是使用者訂閱供應項目時唯一會看到的資訊。 因此，請務必使用可協助使用者了解該供應項目附帶內容的直覺式名稱。 只有系統管理員可以看到「資源名稱」。 它是系統管理員用來處理其他供應項目 (以 Azure Resource Manager 資源方式) 的名稱。

   ![](media/azure-stack-create-offer/image01a.png)
3. 按一下 [基本方案] 來開啟 [方案] 窗格，選取要包含在供應項目中的方案，然後按一下 [選取]。 按一下 [建立]  來建立優惠。

   ![](media/azure-stack-create-offer/image02.png)
4. 建立供應項目之後，您可以變更其狀態。 供應項目必須設定為「公用」，使用者才能在訂閱時取得完整的檢視。 供應項目可以是：
   - **公用**：使用者可以看到。
   - **私用**：只有雲端系統管理員可以看到。 在草擬方案或供應項目時，或雲端系統管理員想要[為使用者建立訂用帳戶](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)時，會相當實用。
   - **已解除**：不開放給新的訂閱者。 雲端系統管理員可以使用「已解除」來避免之後的訂用帳戶使用，但不會影響目前的訂閱者。

   > [!TIP]  
   > 對供應項目所做的變更不會立即對使用者顯示。 若要查看變更，使用者可能必須先登出並再次登入使用者入口網站，才能看見新的訂閱詳情。 

   若要變更供應項目的狀態： 

   - **1803 (含) 之後的版本**：  
     在供應項目的 [概觀] 刀鋒視窗上，按一下 [協助工具狀態]，選取您要使用的狀態 (像是「公用」)，然後按一下 [儲存]。 
 
     ![選取協助工具狀態](media/azure-stack-create-offer/change-state.png) 

     或者，在存取供應項目之後，您可以移至 [供應項目設定]，然後選取 [協助工具狀態] 以變更狀態。 

   - **1803 之前的版本**：  
     按一下 [所有資源]，搜尋您的新供應項目，按一下該新供應項目、按一下 [變更狀態]，然後按一下 [公用]。

  
   > [!NOTE] 
   > 您也可以使用 PowerShell 來建立預設供應項目、方案和配額。 如需詳細資訊，請參閱 [Azure Stack 服務管理員讀我檔案](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)。
   >


### <a name="next-steps"></a>後續步驟
[建立訂用帳戶](azure-stack-subscribe-plan-provision-vm.md)      
[佈建虛擬機器](azure-stack-provision-vm.md)
