---
title: 檢視和管理服務提供者
description: 客戶可以使用 Azure 入口網站中的 [服務提供者] 頁面，檢視服務提供者、服務提供者供應項目與委派的資源的相關資訊。
ms.date: 11/15/2019
ms.topic: conceptual
ms.openlocfilehash: 453f5d3ba4ed5c49b5e71c7530298bc0a03c73ee
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463808"
---
# <a name="view-and-manage-service-providers"></a>檢視和管理服務提供者

客戶可以使用 [Azure 入口網站](https://portal.azure.com)中的 [服務提供者] 頁面，檢視服務提供者與服務提供者供應項目的相關資訊、透過 [Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)委派特定資源，以及購買額外的服務提供者供應項目。 雖然我們在此所述的是服務提供者與其客戶，但管理多個租用戶的企業也可以使用相同的程序來合併其管理體驗。

若要存取 Azure 入口網站中的 [服務提供者] 頁面，客戶可選取 [所有服務]，然後搜尋**服務提供者**並選取它。 他們也可在 Azure 入口網站頂端附近的搜尋方塊中輸入「服務提供者」來找到它。

請記住，[服務提供者] 頁面只顯示可透過 Azure 委派的資源管理存取客戶訂用帳戶或資源群組之服務提供者的相關資訊。 如果與客戶合作的其他服務提供者不是使用 Azure 委派的資源管理來存取客戶資源，則那些服務提供者的相關資訊不會在這裡顯示。

> [!NOTE]
> 服務提供者若要檢視其客戶的相關資訊，可以瀏覽至 Azure 入口網站中的 [我的客戶]。 如需詳細資訊，請參閱[檢視和管理客戶和委派的資源](view-manage-customers.md)。

## <a name="view-service-provider-details"></a>檢視服務提供者詳細資料

若要檢視與客戶合作服務提供者的相關詳細資料，他們可以選取 [服務提供者] 頁面左側的 [提供者供應項目]。

針對每個服務提供者供應項目，客戶都會看到服務提供者的名稱與相關聯的供應項目，以及客戶在上線程序中輸入的名稱。

在 [委派] 欄中，客戶會看到有多少訂用帳戶和/或資源群組，已經針對該供應項目委派至服務提供者。 服務提供者將能根據供應項目中指定的存取層級，存取及管理這些訂用帳戶和/或資源群組。

## <a name="delegate-resources"></a>委派資源

必須先委派資源，服務提供者才能存取及管理客戶的資源。 如果客戶已接受供應項目，但未委派任何資源，則他們會在 [提供者供應項目] 區段的頂端看到一個備註。 這會讓客戶知道他們必須先採取動作，服務提供者才能存取客戶的任何資源。

委派訂用帳戶或資源群組：

1. 選取包含服務提供者、供應項目與名稱的核取方塊。 然後選取畫面頂端的 [委派資源]。
1. 在 [委派資源] 頁面的 [產品/服務詳細資料] 區段中，檢閱服務提供者與供應項目的詳細資料。 若要檢閱供應項目的角色指派，請選取 [按一下此處即可查看所選供應項目的詳細資料]。
1. 在 [委派] 區段中，選取 [委派訂用帳戶] 或 [委派資源群組]。
1. 選擇您想要委派給此供應項目的訂用帳戶和/或資源群組，然後選取 [新增]。
1. 選取頁面底部的核取方塊，以確認您要授與此服務提供者您所選取資源的存取權，然後選取 [委派]。

## <a name="add-or-remove-service-provider-offers"></a>新增或移除服務提供者供應項目

客戶若要在 [提供者供應項目] 頁面新增服務提供者供應項目，可以選取 [新增供應項目]。 服務提供者必須已經為此客戶發佈供應項目。 客戶接著可以從 [私人供應項目] 畫面中選取該供應項目，然後選取 [建立]。

如果客戶想要移除服務提供者供應項目，他們可以選取該供應項目列中的垃圾桶圖示。 確認刪除之後，該服務提供者將無法再存取先前委派給該供應項目的客戶資源。

## <a name="update-service-provider-offers"></a>Update service provider offers

After a customer has added an offer, a service provider may publish an updated version of the same offer to Azure Marketplace. For example, they may want to add a new role definition. If a new version of the offer has been published, the **Provider offers** page will show an "update" icon in the row for that offer. The customer can select this icon to see the differences between the current version of the offer and the new one.

 ![Update offer icon](../media/update-offer.jpg)

After reviewing the changes, the customer can choose to update to the new version. Once they do, the authorizations and other settings specified in the new version will apply to any subscriptions and/or resource groups that have been delegated for that offer.

## <a name="view-delegations"></a>檢視委派

委派代表授與服務提供者客戶所委派資源存取權的角色指派。 若要檢視此資訊，請選取位於 [服務提供者] 頁面左側的 [委派]。

頁面頂端的篩選條件可讓您對委派資訊進行排序及分組，或是依特定客戶、供應項目或關鍵字進行篩選。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Lighthouse](../overview.md)。
- 了解服務提供者如何前往 Azure 入口網站中的 [我的客戶]，[檢視和管理客戶](view-manage-customers.md)。