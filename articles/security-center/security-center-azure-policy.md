---
title: Azure 資訊安全中心安全性原則可以當作 Azure 原則的一部分設定，並於資訊安全中心中檢視 | Microsoft Docs
description: 此文件可協助您在 Azure 原則中設定原則，或在 Azure 資訊安全中心中檢視原則。
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334664"
---
# <a name="view-security-policies"></a>檢視安全性原則

這篇文章說明如何設定安全性原則，以及如何在資訊安全中心中檢視它們。 Azure 資訊安全中心會自動為每個上架的訂用帳戶指派[內建的安全性原則](security-center-policy-definitions.md)。 您可以在 [Azure 原則](../azure-policy/azure-policy-introduction.md)中設定它們，這也使您可以跨管理群組和多個訂用帳戶設定原則。

如需如何使用 PowerShell 設定原則的指示：請參閱[快速入門：使用 Azure RM PowerShell 模組建立原則指派，以識別不符合規範的資源](../azure-policy/assign-policy-definition-ps.md)。



## <a name="what-are-security-policies"></a>什麼是安全性原則？
安全性原則會定義工作負載的所需設定，並且協助確保符合公司或法規安全性需求。 在 Azure 原則中，您可以為 Azure 訂用帳戶定義原則，並按照工作負載的類型或資料的機密性訂定這些原則。 例如，對於使用個人識別資訊等規範資料的應用程式，其需要的安全性層級可能比工作負載還高。 若要跨訂用帳戶或針對管理群組設定原則，請在 [Azure 原則](../azure-policy/azure-policy-introduction.md)中設定。



您的安全性原則會推動您在 Azure 資訊安全中心中取得的安全性建議。 您可以監視它們的合規性，以幫助您識別潛在的弱點並減輕威脅。 如需如何決定哪個選項適合您的詳細資訊，請參閱[內建安全性原則](security-center-policy-definitions.md)的清單。

### <a name="management-groups"></a>管理群組
如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 Azure 管理群組會提供上述訂用帳戶的範圍層級。 您要將訂用帳戶整理到稱為「管理群組」的容器中，並將治理原則套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的原則。 每個目錄會都會有一個最上層管理群組，名為「根」管理群組。 這個根管理群組會建置於階層內，讓所有的管理群組和訂用帳戶摺疊於其中。 這個根管理群組可讓全域原則和 RBAC 指派在目錄層級套用。 若要設定可搭配 Azure 資訊安全中心使用的管理群組，請遵循[取得 Azure 資訊安全中心的全租用戶可見度](security-center-management-groups.md)中的指示。

> [!NOTE]
> 請務必了解管理群組和訂用帳戶的階層。 若要深入了解管理群組、根管理和管理群組存取，請參閱[使用 Azure 管理群組來組織資源](../governance/management-groups/index.md#root-management-group-for-each-directory)。
>

## <a name="how-security-policies-work"></a>安全性原則的運作方式
資訊安全中心會為每個 Azure 訂用帳戶自動建立預設安全性原則。 您可以編輯 Azure 原則中的原則，或執行下列動作：
- 建立新原則定義。
- 將原則指派給代表整個組織或組織內業務單位的各個管理群組和訂用帳戶。
- 監視原則相容性。

如需 Azure 原則的詳細資訊，請參閱[建立和管理原則來強制執行合規性](../azure-policy/create-manage-policy.md)。

Azure 原則由下列元件組成：

- **原則**是一個規則。
- **方案**是原則的集合。
- **指派**是將方案或原則套用至特定範圍 (管理群組、訂用帳戶或資源群組)。

資源會根據指派給它的原則進行評估，並根據資源符合的原則數目來接收合規比率。

## <a name="who-can-edit-security-policies"></a>誰可以編輯安全性原則？
資訊安全中心會使用角色型存取控制 (RBAC)，以提供可在 Azure 中指派給使用者、群組與服務的內建角色。 使用者開啟資訊安全中心時，只能看到與自己能夠存取之資源相關的資訊。 這表示會為使用者指派資源所屬訂用帳戶或資源群組的擁有者、參與者或讀取者角色。 除了這些角色，有兩個特定的資訊安全中心角色：

- 安全性讀取者：擁有資訊安全中心的檢視權限 (包括建議、警示、原則和健康情況)，但無法進行變更。
- 安全性管理員：擁有與安全性讀取者相同的檢視權限，而且還可以更新安全性原則，並解除建議和警示。

## <a name="edit-security-policies"></a>編輯安全性原則
您可以在 [Azure 原則](../governance/policy/tutorials/create-and-manage.md)內，編輯每個 Azure 訂用帳戶和管理群組的預設安全性原則。 若要修改安全性原則，您必須是該訂用帳戶或所在管理群組的擁有者、參與者或安全性系統管理員。

如需如何編輯 Azure 原則中的安全性原則的指示，請參閱[建立和管理原則來強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。

## <a name="view-security-policies"></a>檢視安全性原則

若要在資訊安全中心檢視安全性原則：

1. 在 [資訊安全中心] 儀表板上，選取 [安全性解決方案]。

    ![原則管理窗格](./media/security-center-policies/security-center-policy-mgt.png)

  在 [原則管理] 畫面中，您可以看到管理群組、訂用帳戶和工作區數目，以及您的管理群組結構。

  > [!NOTE]
  > - 資訊安全中心儀表板在 [訂用帳戶涵蓋範圍] 之下顯示的訂用帳戶數目，比在 [原則管理] 之下顯示的訂用帳戶數目還要多。 訂用帳戶涵蓋範圍會顯示標準、免費和「未涵蓋」的訂用帳戶數目。 「未涵蓋」訂用帳戶並未啟用資訊安全中心，而且不會顯示在 [原則管理] 之下。
  >

  資料表中的資料行會顯示：

 - **原則方案指派** – 資訊安全中心[內建的原則](security-center-policy-definitions.md)和方案，已指派給訂用帳戶或管理群組。
 - **合規性** - 管理群組、訂用帳戶或工作區的整體合規性分數。 此分數是指派的加權平均值。 單一指派中原則數目的加權平均因子，以及套用指派的資源數目。

 例如，如果訂用帳戶有兩個 VM 以及已指派五個原則的方案，則您的訂用帳戶中有 10 項評估。 如果其中一個 VM 不符合兩個原則，您訂用帳戶指派的整體合規性分數為 80%。

 - **涵蓋範圍** - 識別執行管理群組、訂用帳戶或工作區的定價層 (免費或標準)。  若要深入了解資訊安全中心的定價層，請參閱[價格](security-center-pricing.md)。
 - **設定** – 訂用帳戶具有 [編輯設定] 連結。 選取 [編輯設定] 可讓您更新每個訂用帳戶或管理群組的[資訊安全中心設定](security-center-policies-overview.md)。

2. 選取要檢視其原則的訂用帳戶或管理群組。

  - [安全性原則] 畫面反映了在您選取的訂用帳戶或管理群組上指派的原則所採取的動作。
  - 在頂端，使用提供的連結開啟套用於訂用帳戶或管理群組的每個原則**指派**。 您可以使用連結存取指派，並編輯或停用原則。 例如，如果您看到特定原則指派有效地拒絕端點保護，則可以使用該連結來存取原則，以及編輯或停用原則。
  - 在原則清單中，您可以在訂用帳戶或管理群組中查看原則的有效應用。 這表示套用至範圍之每個原則的設定會納入考量，並向您提供原則採取之動作的累加結果。 例如，如果原則在一個指派中已停用，但在另一個指派中它被設定為 AuditIfNotExist，則累加的效果就會套用 AuditIfNotExist。 更積極的效果一律會優先使用。
  - 原則的效果可以是：Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled。 如需如何套用效果的相關詳細資訊，請參閱[了解原則效果](../governance/policy/concepts/effects.md)。

   ![[原則] 畫面](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - 檢視指派的原則時，您可以看到多個指派，您可以看到每個指派如何自行設定。

## <a name="next-steps"></a>後續步驟
在此文章中，您了解到如何在資訊安全中心設定安全性原則。 如要深入了解資訊安全中心，請參閱下列文章：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)：了解如何規劃及了解 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [取得 Azure 資訊安全中心的全租用戶可見度](security-center-management-groups.md)：了解如何設定 Azure 資訊安全中心的管理群組。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)：取得使用服務常見問題的解答。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。

若要深入了解 Azure 原則，請參閱[什麼是 Azure 原則？](../azure-policy/azure-policy-introduction.md)
