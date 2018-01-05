---
title: "新增或變更 Azure 系統管理員訂用帳戶角色| Microsoft Docs"
description: "說明如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/04/2018
ms.author: genli
ms.openlocfilehash: dc09f29fec78d408e1560bfa0a943f16ab50c760
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>新增或變更 Azure 訂用帳戶管理員

Azure 傳統訂用帳戶系統管理員和 Azure[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-what-is.md)是兩個系統管理 Azure 資源的存取權都一樣：

* 傳統訂用帳戶系統管理員角色提供基本的存取管理，並包含帳戶系統管理員、 服務管理員和共同管理員。
    * 當您註冊新的 Azure 訂用帳戶 」 時，則您的帳戶預設是設定為帳戶管理員和服務系統管理員。
    * 共同管理員可以將新增號後面。
* RBAC 是較新的系統提供了許多內建的角色、 範圍及自訂角色的彈性的精細存取管理。
    * 不過，只有 RBAC 角色和任何傳統訂用帳戶系統管理員角色的使用者不能管理 Azure 傳統部署。

若要確保更好的控制，並簡化存取管理，我們建議您對所有存取管理需求，使用 RBAC。 可能的話，我們建議您重新設定現有的存取原則使用 RBAC。 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>在 Azure 入口網站中新增訂用帳戶的 RBAC 擁有者管理員 

若要將某人加入為 Azure 訂用帳戶的服務管理管理員，讓它們 RBAC 擁有者角色訂用帳戶。 擁有者角色可以管理您指派之訂用帳戶中的資源，但沒有其他訂用帳戶的存取權限。

1. 請瀏覽[**訂閱**Azure 入口網站中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
2. 選取您想要提供存取權的訂用帳戶。
3. 在功能表中選取 [存取控制 (IAM)]。
4. 在**角色**方塊中，選取**擁有者**。 
5. 在**指派其存取權**方塊中，選取**Azure AD 使用者、 群組或應用程式**。 
6. 在**選取**方塊中，輸入您想要新增為擁有者使用者的電子郵件地址。 選取使用者，然後選取 [儲存]。

    ![顯示已選取 [擁有者] 角色的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-role.png)

這可讓使用者完整存取所有資源，包括委派給其他人存取的權限。 若要提供存取權，在不同的範圍，例如資源群組，請瀏覽該範圍的 IAM 功能表。 

## <a name="add-or-change-co-administrator"></a>新增或變更共同管理員

您只能將擁有者新增為共同管理員， 不能將角色為參與者和讀者等的其他使用者新增為共同管理員。

> [!TIP]
> 您只需要新增 「 擁有者 」 帳戶為共同管理員，如果使用者需要管理傳統的 Azure 部署。 我們建議使用 RBAC 用於所有其他用途。

1. 如果您尚未新增，請遵循上述指示將某人新增為擁有者。
2. **以滑鼠右鍵按一下**您剛才新增的「擁有者」使用者，然後選取 [新增為共同管理員]。 如果您沒有看到**加入為共同管理員**選項、 重新整理頁面，或其他網際網路瀏覽器再試一次。 

    ![新增共同管理員的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    若要移除共同管理員權限，請**以滑鼠右鍵按一下**「共同管理員」使用者，然後選取 [移除共同管理員]。

    ![移除共同管理員的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>變更 Azure 訂用帳戶的服務管理員

只有帳戶管理員可以變更訂用帳戶的服務管理員。 根據預設，註冊時系統會將服務管理員與帳戶管理員設為同一人。 服務系統管理員變更為不同的使用者，如果帳戶系統管理員就會失去存取 Azure 入口網站。 不過，帳戶系統管理員可隨時變更回本身的服務系統管理員使用帳戶中心。

1. 請參閱[變更服務管理員的限制](#limits)，確定您的情況是否受支援。
1. 以帳戶管理員身分登入[帳戶中心](https://account.windowsazure.com/subscriptions)。
1. 選取一個訂用帳戶。
1. 選取右側的 [編輯訂用帳戶詳細資料]。

    ![顯示帳戶中心裡 [編輯訂用帳戶] 按鈕的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. 在 [服務管理員  ] 方塊中，輸入新的服務管理員的電子郵件地址。

    ![顯示應變更服務管理員電子郵件之方塊的螢幕擷取畫面](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>變更服務管理員的限制

* 每個訂用帳戶都與 Azure AD 目錄相關聯。 若要尋找訂用帳戶相關聯的目錄，請移至[**訂閱**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，然後選取 訂用帳戶，以查看目錄。
* 如果您以公司或學校帳戶登入，可以將組織中的其他帳戶新增為服務管理員。 例如，abby@contoso.com 可以將 bob@contoso.com 新增為服務管理員，但若想新增 john@notcontoso.com 為服務管理員，則 contoso.com 目錄中必須要有 john@notcontoso.com 才能這麼做。 以公司或學校帳戶登入的使用者可以繼續將 Microsoft 帳戶使用者新增為服務管理員。

  | 登入方法 | 要將 Microsoft 帳戶使用者新增為安全性關聯 (SA) 嗎？ | 要將同一組織中的公司或學校帳戶新增為安全性關聯 (SA) 嗎？ | 要將其他組織中的公司或學校帳戶新增為安全性關聯 (SA) 嗎？ |
  | --- | --- | --- | --- |
  |  Microsoft 帳戶 |是 |否 |否 |
  |  公司或學校帳戶 |是 |是 |否 |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>變更 Azure 訂用帳戶的帳戶管理員

帳戶管理員是一開始註冊 Azure 訂用帳戶，而且它會負責為計費訂用帳戶擁有者的使用者。 若要變更訂用帳戶的帳戶管理員，請參閱[將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](billing-subscription-transfer.md)。

<a name="check-the-account-administrator-of-the-subscription"></a>

**不確定帳戶管理員是誰嗎？** 請遵循下列步驟：

1. 請瀏覽[**訂閱**Azure 入口網站中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查的訂用帳戶，然後查看 [設定]。
1. 選取 [屬性] 。 帳戶系統管理員的訂用帳戶會顯示在**帳戶管理員**方塊。  

## <a name="types-of-classic-subscription-admins"></a>類型的傳統訂用帳戶系統管理員

 帳戶系統管理員、 服務管理員和共同管理員會在 Azure 傳統訂用帳戶系統管理員角色的三種。 用來註冊 Azure 帳戶會自動設定做為帳戶管理員和服務系統管理員。 然後，您可以加入其他共同管理員。 下表描述這三個系統管理角色之間的確切差異。 

> [!TIP]
> 更好的控制和管理更細緻的存取，我們建議使用 Azure 角色型存取控制 (RBAC)，可讓使用者得以加入至多個角色。 若要進一步了解，請參閱[Azure Active Directory 角色型存取控制](../active-directory/role-based-access-control-what-is.md)。

| 傳統訂用帳戶管理員 | 限制 | 說明 |
| --- | --- | --- |
| 帳戶管理員 (AA) |每個 Azure 帳戶 1 名 |這是已註冊的 Azure 訂用帳戶，且已獲授權存取的使用者[帳戶中心](https://account.azure.com/Subscriptions)並執行各項管理工作。 這些包括能夠建立新的訂閱，請取消訂用帳戶、 變更計費訂用帳戶，並變更服務管理員。 就概念而言，帳戶管理員是訂用帳戶的計費的擁有者。 RBAC，在帳戶系統管理員未指派角色。|
| 服務管理員 (SA) |每個 Azure 訂用帳戶 1 名 |此角色經過授權，可管理 [Azure 入口網站](https://portal.azure.com)上的服務。 根據預設，新訂用帳戶的帳戶管理員也是服務管理員。 中 RBAC，擁有者角色會提供給服務管理員在訂用帳戶範圍。|
| 共同管理員 (CA) |每個訂用帳戶 200 名 |此角色的存取權限與服務管理員相同，但無法變更訂用帳戶與 Azure 目錄的關聯。 在 RBAC，擁有者角色會提供給共同管理員在訂用帳戶範圍。|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>深入了解資源存取控制和 Active Directory

* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱[了解 Azure 中的資源存取](../active-directory/active-directory-understanding-resource-access.md)。
* 如需有關 Azure Active Directory 的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../active-directory/active-directory-how-subscriptions-associated-directory.md)和[指派 Azure Active Directory 中的管理員角色](../active-directory/active-directory-assign-admin-roles-azure-portal.md)。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
