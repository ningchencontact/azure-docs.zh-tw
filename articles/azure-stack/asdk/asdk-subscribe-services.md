---
title: 在本教學課程中，您會了解如何訂閱 Azure Stack 供應項目 | Microsoft Docs
description: 本教學課程會示範如何建立新的 Azure Stack 服務訂用帳戶，並藉由建立測試虛擬機器來測試供應項目。
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
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>教學課程：建立和測試訂用帳戶
本教學課程說明如何建立含有供應項目的訂用帳戶，然後進行測試。 為了進行測試，您會以雲端系統管理員身分登入[使用者入口網站](https://portal.local.azurestack.external)，訂閱該供應項目，然後建立虛擬機器。

> [!TIP]
> 若要獲得更進階的評估體驗，您可以[為特定使用者建立訂用帳戶](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator)，然後在使用者入口網站中以使用者的身分登入。 

本教學課程會示範如何訂閱[上一個教學課程](asdk-offer-services.md)中所建立的供應項目。

您將了解：

> [!div class="checklist"]
> * 訂閱優惠 
> * 測試供應項目

## <a name="subscribe-to-an-offer"></a>訂閱優惠
若要以使用者身分訂閱供應項目，您需要登入 Azure Stack 使用者入口網站，以探索 Azure Stack 操作員已提供的服務。

1. 登入[使用者入口網站](https://portal.local.azurestack.external)，並按一下 [取得訂用帳戶]。

   ![取得訂用帳戶](media/asdk-subscribe-services/get-subscription.png)

2. 在 [顯示名稱]  欄位中，輸入您的訂閱名稱。 接著，按一下 [供應項目] 以選取 [選擇供應項目] 區段中的其中一個可用供應項目，然後按一下 [建立]。

   ![建立優惠](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > 您必須立即重新整理使用者入口網站才可開始使用您的訂用帳戶。

3. 若要檢視您剛建立的訂用帳戶，請按一下 [更多服務]、按一下 [訂用帳戶]，然後按一下您的新訂用帳戶。 在訂閱供應項目之後，請重新整理入口網站以查看新訂用帳戶中是否已納入新服務。 在此範例中，我們已新增**虛擬機器**。

   ![檢視訂用帳戶](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>測試供應項目
在登入[使用者入口網站](https://portal.local.azurestack.external)時，您可以藉由使用新的訂用帳戶功能佈建虛擬機器，來測試供應項目。 

> [!NOTE]
> 這項測試需要已在[上一個教學課程](asdk-marketplace-item.md)中新增至 Azure Stack 市集的 Windows Server 2016 資料中心 VM 映像。 

1. 登入[使用者入口網站](https://portal.local.azurestack.external)。

2. 在使用者入口網站中，按一下 [虛擬機器] > [新增] > [Windows Server 2016 Datacenter]，然後按一下 [建立]。

3. 在 [基本概念] 區段中，輸入 [名稱]、[使用者名稱] 和 [密碼]，選擇 [訂用帳戶]，建立 [資源群組] (或選取現有的)，然後按一下 [確定]。

4. 在 [選擇大小] 區段中，按一下 [A1 標準]，再按一下 [選取]。  

5. 在 [設定] 刀鋒視窗中，接受預設值並按一下 [確定]。

6. 在 [摘要] 區段中，按一下 [確定] 以建立虛擬機器。  

7. 若要查看您的新虛擬機器，請按一下 [虛擬機器]，然後搜尋新的虛擬機器並按一下其名稱。

    ![所有資源](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> 虛擬機器部署需要數分鐘的時間才能完成。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 訂閱優惠 
> * 測試供應項目

請前進到下一個教學課程，以了解如何使用範本建立 VM。

> [!div class="nextstepaction"]
> [利用範本建立虛擬機器](asdk-create-vm-template.md)