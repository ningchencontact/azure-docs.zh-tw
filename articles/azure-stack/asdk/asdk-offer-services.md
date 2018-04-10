---
title: 在本教學課程中，您要建立 Azure Stack 訂閱詳情 | Microsoft Docs
description: 了解如何建立包含方案和配額的 Azure Stack 訂閱詳情。
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
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8bcc2f3077e79ff83ac2e90db0bb0fa53ae83adc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>教學課程：提供 Azure Stack IaaS 服務
身為 Azure Stack 雲端系統管理員，您可以建立供應項目，以供您的使用者 (有時稱為租用戶) 訂閱。 利用其訂用帳戶，使用者可以接著取用 Azure Stack 服務。

本教學課程會示範如何建立訂閱詳情，讓使用者可以根據您在[上一個教學課程](asdk-marketplace-item.md)中所上傳的 Azure Stack 市集 Windows Server 2016 Datacenter 映像，來建立虛擬機器。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立優惠
> * 建立方案
> * 設定配額
> * 將訂閱詳情設為公用

在 Azure Stack 中，能透過訂用帳戶、供應項目與方案，為使用者提供服務。 使用者可以訂閱多個供應項目。 訂閱詳情可以包含一或多項方案，而方案則可有一或多項服務，如下圖所示：

![訂用帳戶、供應項目與方案](media/asdk-offer-services/sop.png)

身為提供服務的 Azure Stack 操作人員，系統會先提示您建立訂閱詳情、然後建立方案，最後建立配額。 建立訂閱詳情後，Azure Stack 使用者便可透過使用者入口網站來加以訂閱。

## <a name="create-an-offer"></a>建立優惠
**訂閱詳情**是一組 Azure Stack 操作人員提供給使用者購買或訂閱的一或多項方案。

1. 以雲端系統管理員身分登入 [Azure Stack 入口網站](https://adminportal.local.azurestack.external)。

2. 按一下 [新增] > [訂閱詳情 + 方案] > [訂閱詳情]。

   ![新增供應項目](media/asdk-offer-services/new-offer.png)

2. 在 [新增供應項目] 區段中，填寫 [顯示名稱] 與 [資源名稱]，然後選取新的或現有的 [資源群組]。 顯示名稱是使用者看到的訂閱詳情公用易記名稱。 只有雲端操作員可以看到資源名稱；系統管理員會使用這名稱，將訂閱詳情當作 Azure Resource Manager 資源來使用。

   ![顯示名稱](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>建立方案
輸入基本的訂閱詳情資訊後，您至少必須新增一項基本方案到訂閱詳情中。 

**方案**讓 Azure Stack 操作人員可以將服務和相關聯的配額分組，以提供給使用者。

1. 按一下 [基本方案]，然後在 [方案] 區段中，按一下 [新增] 以將新方案新增到供應項目中。

   ![新增方案](media/asdk-offer-services/new-plan.png)

2. 在 [新增方案] 區段中，填寫 [顯示名稱] 與 [資源名稱]。 顯示名稱是使用者看到的方案公用易記名稱。 只有雲端操作員可以看到資源名稱；雲端操作員會使用這名稱，將方案當作 Azure Resource Manager 資源來使用。

   ![方案顯示名稱](media/asdk-offer-services/plan-display-name.png)

3. 接下來，請選取要包含在方案中的服務。 若要提供 IaaS 服務，請按一下 [服務]，選取 [Microsoft.Compute]、[Microsoft.Network] 及 [Microsoft.Storage]，然後按一下 [選取]。

   ![方案服務](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>設定配額
現在，訂閱詳情已具有包含服務的方案，您必須為每項方案設定配額。 

**配額**會決定在提供的方案中所包含的每項服務，使用者可耗用多少資源。

1. 按一下 [配額]，然後選取您要建立配額的服務。 

   一開始，我們先為計算服務建立配額。 在命名空間清單中，選取 [Microsoft.Compute] 命名空間，然後按一下 [建立新的配額]。
   
   ![建立新的配額](media/asdk-offer-services/create-quota.png)

2. 請在 [建立配額] 區段中，鍵入配額名稱並為配額設定想要的參數，然後按一下 [確定]。

   ![配額名稱](media/asdk-offer-services/quota-properties.png)

3. 針對 **Microsoft.Compute** 服務，選取您所建立的配額。

   ![選取配額](media/asdk-offer-services/set-quota.png)

4. 針對網路與儲存體服務重複步驟 1-3，然後按一下 [配額] 區段中的 [確定]。 接下來，按一下 [新方案] 區段中的 [確定]，完成方案設定。 

   ![所有配額皆已設定](media/asdk-offer-services/all-quotas-set.png)

5. 按一下 [方案] 區段中的 [建立]，完成建立訂閱詳情。 成功建立訂閱詳情後，您會看到通知；在清單上也會將該訂閱詳情列為可用的訂閱詳情。

   ![已建立訂閱詳情](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>將訂閱詳情設為公用
您必須將訂閱詳情設為公用，讓使用者在選擇要訂閱的項目時可以看見。 

供應項目可以是：
- **公用**：所有使用者都可以看到。
- **私用**：只有雲端系統管理員可以看到。 在草擬方案或訂閱詳情時，或雲端系統管理員想要為使用者建立訂用帳戶時，會相當實用。
- **已解除**：不開放給新的訂閱者。 雲端系統管理員可以使用「已解除」來避免之後的訂用帳戶使用，但不會影響目前的訂閱者。

> [!TIP]
> 對訂閱詳情所做的變更不會立即對使用者顯示。 若要查看變更，使用者可能必須先登出並再次登入[使用者入口網站](https://portal.local.azurestack.external)，才能看見新的訂閱詳情。

若要將新的訂閱詳情設為公用： 
   - 1803 (含) 之後的版本： 
     1. 在儀表板功能表上，按一下 [供應項目]，然後按一下您建立的供應項目。

     2. 按一下 [協助工具狀態]，然後按一下 [公開]。

        ![變更狀態](media/asdk-offer-services/change-state.png)

     3. 現在 Azure Stack 入口網站中會提供該訂閱詳情。


   - 1803 之前的版本：  
     1. 在儀表板功能表上，按一下 [供應項目]，然後按一下您建立的供應項目。

     2. 按一下 [變更狀態]，然後按一下 [公開]。

        ![公開映像](media/asdk-offer-services/set-public.png)

     3. 現在 Azure Stack 入口網站中會提供該訂閱詳情。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立優惠
> * 建立方案
> * 設定配額
> * 將訂閱詳情設為公用

前進到下一個教學課程，以了解如何訂閱您剛剛以 Azure Stack 使用者身分所建立的訂閱詳情。

> [!div class="nextstepaction"]
> [訂閱提供項目](asdk-subscribe-services.md)