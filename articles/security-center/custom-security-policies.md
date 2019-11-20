---
title: 在 Azure 資訊安全中心中建立自訂安全性原則 |Microsoft Docs
description: 由 Azure 資訊安全中心監視的 Azure 自訂原則定義。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d1ef8852916e598373c104fb98a1c09ab656fbaf
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196210"
---
# <a name="using-custom-security-policies-preview"></a>使用自訂安全性原則（預覽）

為了協助保護您的系統和環境，Azure 資訊安全中心會產生安全性建議。 這些建議是以業界最佳作法為基礎，並併入提供給所有客戶的一般預設安全性原則中。 它們也可以來自資訊安全中心的產業和法規標準知識。

透過這項預覽功能，您可以新增自己的*自訂*方案。 如果您的環境未遵循您所建立的原則，您就會收到建議。

如 Azure 原則檔中[所述，](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)當您指定自訂方案的位置時，它必須是管理群組或訂用帳戶。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>將自訂方案新增至您的訂用帳戶 

1. 從資訊安全中心的提要欄位中，開啟 [**安全性原則**] 頁面。

1. 選取您想要新增自訂方案的訂用帳戶或管理群組。

    [![選取您要為其建立自訂原則的訂用帳戶](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 當您新增自訂標準時，它會將*方案*指派給該範圍。 因此，我們建議您選取該指派所需的最寬範圍。 

1. 在 [安全性原則] 頁面的 [自訂計畫（預覽）] 底下，按一下 [**新增自訂方案**]。

    [![按一下 [新增自訂方案]](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    隨即會出現下列頁面：

    ![建立或新增原則](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在 [新增自訂計畫] 頁面中，檢查您的組織中已建立的自訂原則清單。 如果您看到您想要指派給訂用帳戶的帳戶，請按一下 [**新增**]。 如果清單中沒有符合您需求的計畫，請略過此步驟。

1. 若要建立新的自訂方案：

    1. 按一下 **[新建]** 。
    1. 輸入定義的 [位置] 和 [名稱]。
    1. 選取要包含的原則，然後按一下 [**新增**]。
    1. 輸入任何所需的參數。
    1. 按一下 [檔案]。
    1. 在 [新增自訂計畫] 頁面中，按一下 [重新整理]，您的新方案會顯示為 [可用]。
    1. 按一下 [**新增**]，並將它指派給您的訂用帳戶。

    > [!NOTE]
    > 建立新的方案需要訂用帳戶擁有者認證。 如需 Azure 角色的詳細資訊，請參閱[Azure 資訊安全中心中的許可權](security-center-permissions.md)。

1. 若要查看您的原則所產生的建議，請按一下提要欄位中的 [**建議**] 以開啟 [建議] 頁面。 建議會顯示「自訂」標籤，並在大約一小時內提供。

    [![自訂建議](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何建立自訂安全性原則。 

如需其他相關內容，請參閱下列文章： 

- [安全性原則的總覽](tutorial-security-policy.md)
- [內建安全性原則的清單](security-center-policy-definitions.md)