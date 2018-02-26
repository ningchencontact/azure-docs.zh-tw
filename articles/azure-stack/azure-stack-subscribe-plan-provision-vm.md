---
title: "在 Azure Stack 中訂閱供應項目 | Microsoft Docs"
description: "在 Azure Stack 中建立供應項目的訂用帳戶"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>在 Azure Stack 中建立供應項目的訂用帳戶

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

當您[建立供應項目](azure-stack-create-offer.md)後，使用者必須要有此供應項目的訂用帳戶才能開始使用。   
- 若是身為雲端操作員，您可以從系統管理員入口網站為使用者建立訂用帳戶。  您建立的訂用帳戶可用於公用和私用供應項目。
- 若是身為租用戶使用者，您可以在利用使用者入口網站時訂閱公用供應項目。  

下列各節將為雲端操作員提供建立使用者訂用帳戶時的指引，以及如何以使用者的身分訂閱供應項目。

## <a name="create-a-subscription-as-a-cloud-operator"></a>以雲端操作員的身分建立訂用帳戶
雲端操作員可使用系統管理員入口網站，來為使用者建立供應項目的訂用帳戶。  您可以為自己的目錄租用戶成員建立訂用帳戶。  若已啟用[多租用戶](azure-stack-enable-multitenancy.md)，您也可以為其他目錄租用戶中的使用者建立訂用帳戶。

您可以為公用和私用供應項目建立訂用帳戶。  如果不想讓租用戶建立他們自己的訂用帳戶，您可以將所有供應項目設為私用，然後代表您的租用戶建立訂用帳戶。 此方法常用於將 Azure Stack 與外部帳務系統或服務類別目錄系統整合時。

為使用者建立訂用帳戶之後，該使用者就可以登入使用者入口網站，並找到他們已訂閱供應項目。  

### <a name="to-create-the-subscription-for-a-user"></a>為使用者建立訂用帳戶
1.  在系統管理員入口網站中，移至**使用者訂用帳戶。**
2.  選取 [新增]來開啟 [新增使用者訂用帳戶] 窗格。 您需在此指定下列詳細資料：  

  a. **顯示名稱** – 用於識別訂用帳戶的易記名稱，這會顯示為「使用者訂用帳戶名稱」。

  b. **使用者** – 從適用此訂用帳戶的可用目錄租用戶中指定使用者。 使用者名稱會顯示為「擁有者」。  使用者名稱的格式取決於您的身分識別解決方案。 例如︰   

     -  **Azure AD：**  &lt;user1>@&lt;contoso.onmicrosoft.com>

     -   **AD FS：**  &lt;user1>@&lt;azurestack.local>     

  c.    **目錄租用戶** – 選取使用者帳戶所屬的目錄租用戶。 如果您未啟用多租用戶，則只有您的本機目錄租用戶可使用。

3.  選取 [供應項目] 來開啟 [供應項目] 窗格，然後為此訂用帳戶選擇**供應項目**。 由於您是在為使用者建立訂用帳戶，您可以選取私用或公用供應項目。

4.  選取 [建立] 以建立訂用帳戶。 [使用者訂用帳戶] 窗格隨即會顯示新的訂用帳戶。  稍後，當使用者登入使用者入口網站時，他們就可以檢視有關此訂用帳戶的詳細資料。

### <a name="to-make-an-add-on-plan-available"></a>提供附加方案  
雲端操作員可以隨時將附加方案新增至先前建立的訂用帳戶：   
1.  在系統管理員入口網站中，移至 [更多服務] > [使用者訂用帳戶]，然後選取您要變更的訂用帳戶。   

2.  選取 [附加元件] > [新增] 來開啟 [新增方案] 窗格。  

3.  選取您想要新增為附加元件的方案。  然後，主控台就會顯示與訂用帳戶相關聯的方案。




## <a name="create-a-subscription-as-a-user"></a>以使用者身分建立訂用帳戶
若您是使用者，登入使用者入口網站後，即可在目錄租用戶 (組織) 中尋找公用供應項目和附加方案，並加以訂閱。 如果 Azure Stack 環境支援[多租用戶](azure-stack-enable-multitenancy.md)，則您可以從遠端目錄租用戶訂閱供應項目。

### <a name="to-subscribe-to-an-offer"></a>訂閱供應項目
1. [登入](azure-stack-connect-azure-stack.md) Azure Stack 使用者入口網站 ( https://portal.local.azurestack.external )，然後按一下 [取得訂用帳戶]。

   ![取得訂用帳戶](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. 在 [顯示名稱] 欄位中，輸入您的訂用帳戶名稱、按一下 [供應項目]、按一下 [選擇供應項目] 窗格中的其中一個供應項目，然後按一下 [建立]。

   ![建立優惠](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 若要檢視您建立的訂用帳戶，請按一下 [更多服務]、按一下 [訂用帳戶]，然後按一下您的新訂用帳戶。  

訂閱供應項目之後，請重新整理入口網站以查看哪些服務是新訂用帳戶的一部分。

### <a name="to-subscribe-to-an-add-on-plan"></a>訂閱附加方案
如果供應項目有附加方案，您可以隨時將該方案新增至您的訂用帳戶。  

1. 在使用者入口網站中，選取 [更多服務] > [訂用帳戶]，然後選取您要變更的訂用帳戶。

2. 選取 [新增方案] 按鈕，然後再選取您想要的附加方案。 如果無法使用 [新增方案]，則表示與此訂用帳戶相關聯的供應項目沒有附加方案。



## <a name="next-steps"></a>後續步驟
[佈建虛擬機器](azure-stack-provision-vm.md)
