---
title: 建立 Azure AD 使用者帳戶
description: 本文說明如何在 Azure 自動化中為 Runbook 建立 Azure AD 使用者帳戶認證，以在 Azure 中進行驗證。
keywords: azure active directory 使用者, azure 服務管理, azure ad 使用者帳戶
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 07fb9284372020ba2aadb3758d0ab46682b50831
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>使用 Azure 傳統部署和 Resource Manager 驗證 Runbook
本文說明要為針對 Azure 傳統部署模型或 Azure Resource Manager 資源執行的 Azure 自動化 Runbook 設定 Azure AD 使用者帳戶，所必須執行的步驟。 雖然這仍是您 Azure Resource Manager 型 Runbook 支援的驗證身分識別，但建議的方法是使用 Azure 執行身分帳戶。       

## <a name="create-a-new-azure-active-directory-user"></a>建立新的 Azure Active Directory 使用者
1. 以您想要管理的 Azure 訂用帳戶的服務系統管理員身分登入 Azure 入口網站。
2. 選取 [Azure Active Directory] > [使用者和群組] > [所有使用者] > [新增使用者]。
3. 輸入使用者的詳細資料，例如 [名稱] 和 [使用者名稱]。  
4. 請記下使用者的完整名稱和暫時密碼。
5. 選取 [目錄角色]。
6. 指派全域或受限的管理員角色。
7. 登出 Azure，然後使用您剛才建立的帳戶登入。系統會提示您變更使用者的密碼。

## <a name="create-an-automation-account-in-the-azure-portal"></a>在 Azure 入口網站中建立自動化帳戶
在本節中，您會執行下列步驟以在 Azure 入口網站中建立 Azure 自動化帳戶，以便與您用來在 Azure Resource Manager 模式中管理資源的 Runbook 搭配使用。  

1. 以您想要管理的 Azure 訂用帳戶的服務系統管理員身分登入 Azure 入口網站。
2. 選取 [自動化帳戶] 。
3. 選取 [新增] 。<br><br>![加入自動化帳戶](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. 在 [新增自動化帳戶] 刀鋒視窗的 [名稱] 方塊中，輸入新的自動化帳戶的名稱。
5. 如果您有多個訂用帳戶，請為新的自動化帳戶指定其中一個訂用帳戶，並指定新的或現有的 [資源群組] 和 Azure 資料中心的 [位置]。
6. 在 [建立 Azure 執行身分帳戶] 選項中選取 [是] 這個值，然後按一下 [建立] 按鈕。  
   
    > [!NOTE]
    > 如果您選取選項 [否] 以選擇不要建立執行身分帳戶，則會在 [新增自動化帳戶] 刀鋒視窗中看到一則警告訊息。 雖然會建立帳戶並將其指派給訂用帳戶中的 [參與者]  角色，但帳戶在訂用帳戶的目錄服務內不會有對應的驗證身分識別，因此在訂用帳戶中沒有存取資源。 這會導致參考此帳戶的任何 Runbook 無法進行驗證，並針對 Azure Resource Manager 資源執行工作。
    > 
    >

    <br>![加入自動化帳戶警告](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. 在 Azure 建立自動化帳戶時，您可以在功能表的 [通知]  底下追蹤進度。

認證建立完成後，您需要建立一個認證資產，讓自動化帳戶與稍早建立的 AD 使用者帳戶產生關聯。 請記住，您只建立了自動化帳戶，但它並未與驗證身分識別產生關聯。 執行 [Azure 自動化中的認證資產](automation-credentials.md#creating-a-new-credential-asset)一文所述的步驟，並以**網域\使用者**格式輸入 [使用者名稱] 值。

## <a name="use-the-credential-in-a-runbook"></a>在 Runbook 中使用認證
您可以在 Runbook 中使用 [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) 活動來擷取認證，然後將它搭配 [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) 來連接到您的 Azure 訂用帳戶。 如果認證是多個 Azure 訂用帳戶的管理員，則您也應該使用 [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) 來指定正確的一個。 這會在以下的 PowerShell 範例中顯示，通常會出現在大部分 Azure 自動化 Runbook 的頂端。

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

請在您 Runbook 中的任何[檢查點](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints)後重複這幾行。 如果 Runbook 暫止然後在另一個背景工作上繼續執行，則它必須重新執行驗證。

## <a name="next-steps"></a>後續步驟
* 在接下來的 [Azure 自動化 Runbook 類型](automation-runbook-types.md)

