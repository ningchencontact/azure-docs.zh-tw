---
title: 使用安全性原則 | Microsoft Docs
description: 本文說明如何在 Azure 資訊安全中心中使用安全性原則。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 4550532d36753d9b8ed472193bc833855ddd34c9
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314924"
---
# <a name="working-with-security-policies"></a>使用安全性原則

這篇文章說明如何設定安全性原則，以及如何在資訊安全中心中檢視它們。 Azure 資訊安全中心會自動為每個上架的訂用帳戶指派[內建的安全性原則](security-center-policy-definitions.md)。 您可以在 [Azure 原則](../governance/policy/overview.md)中設定它們，這也使您可以跨管理群組和多個訂用帳戶設定原則。

如需有關如何使用 PowerShell 設定原則的指示，請參閱[快速入門：使用 Azure PowerShell 模組建立原則指派，以識別不符合規範的資源](../governance/policy/assign-policy-powershell.md)。

>[!NOTE]
> 資訊安全中心已開始進行其與 Azure 原則的整合。 現有客戶會自動遷移至 Azure 原則中新的內建方案，而不是資訊安全中心先前的安全性原則。 除了 Azure 原則中新方案的目前狀態以外，這項變更不會影響您的資源或環境。

## <a name="what-are-security-policies"></a>什麼是安全性原則？
安全性原則會定義工作負載的所需設定，並且協助確保符合公司或法規安全性需求。 在 Azure 原則中，您可以為 Azure 訂用帳戶定義原則，並按照工作負載的類型或資料的機密性訂定這些原則。 例如, 使用受規範資料的應用程式 (例如個人資料或客戶資料) 可能需要比其他工作負載更高的安全性等級。 若要跨訂用帳戶或針對管理群組設定原則，請在 [Azure 原則](../governance/policy/overview.md)中設定。

您的安全性原則會推動您在 Azure 資訊安全中心中取得的安全性建議。 您可以監視它們的合規性，以幫助您識別潛在的弱點並減輕威脅。 如需如何決定哪個選項適合您的詳細資訊，請參閱[內建安全性原則](security-center-policy-definitions.md)的清單。

當您啟用資訊安全中心時，內建到資訊安全中心的安全性原則會反映在 Azure 原則中，並作為資訊安全中心類別下的內建方案。 內建方案會自動指派給所有註冊資訊安全中心的訂用帳戶 (免費或標準層)。 內建方案僅包含稽核原則。


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

如需 Azure 原則的詳細資訊，請參閱[建立和管理原則來強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。

Azure 原則由下列元件組成：

- **原則**是一個規則。
- **方案**是原則的集合。
- **指派**是將方案或原則套用至特定範圍 (管理群組、訂用帳戶或資源群組)。

## <a name="view-security-policies"></a>檢視安全性原則

若要在資訊安全中心檢視安全性原則：

1. 在 [資訊安全中心]  儀表板上，選取 [安全性解決方案]  。

    ![原則管理窗格](./media/security-center-policies/security-center-policy-mgt.png)

   在 [原則管理]  畫面中，您可以看到管理群組、訂用帳戶和工作區數目，以及您的管理群組結構。

   > [!NOTE]
   > 資訊安全中心儀表板在 [訂用帳戶涵蓋範圍]  之下顯示的訂用帳戶數目，比在 [原則管理]  之下顯示的訂用帳戶數目還要多。 訂用帳戶涵蓋範圍會顯示標準、免費和「未涵蓋」的訂用帳戶數目。 「未涵蓋」訂用帳戶並未啟用資訊安全中心，而且不會顯示在 [原則管理]  之下。
   >

2. 選取要檢視其原則的訂用帳戶或管理群組。

   - [安全性原則]  畫面反映了在您選取的訂用帳戶或管理群組上指派的原則所採取的動作。
   - 在頂端，使用提供的連結開啟套用於訂用帳戶或管理群組的每個原則**指派**。 您可以使用連結存取指派，並編輯或停用原則。 例如，如果您看到特定原則指派有效地拒絕端點保護，則可以使用該連結來存取原則，以及編輯或停用原則。
   - 在原則清單中，您可以在訂用帳戶或管理群組中查看原則的有效應用。 這表示套用至範圍之每個原則的設定會納入考量，並向您提供原則採取之動作的累加結果。 例如，如果原則在一個指派中已停用，但在另一個指派中它被設定為 AuditIfNotExist，則累加的效果就會套用 AuditIfNotExist。 更積極的效果一律會優先使用。
   - 原則的效果可以是：Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled。 如需如何套用效果的相關詳細資訊，請參閱[了解原則效果](../governance/policy/concepts/effects.md)。

   ![[原則] 畫面](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> 檢視指派的原則時，您可以看到多個指派，您可以看到每個指派如何自行設定。

## <a name="edit-security-policies"></a>編輯安全性原則
您可以在 [Azure 原則](../governance/policy/tutorials/create-and-manage.md)內，編輯每個 Azure 訂用帳戶和管理群組的預設安全性原則。 若要修改安全性原則, 您必須是訂用帳戶或包含管理群組的擁有者或安全性系統管理員。

如需如何編輯 Azure 原則中的安全性原則的指示，請參閱[建立和管理原則來強制執行合規性](../governance/policy/tutorials/create-and-manage.md)。

您可以透過 Azure 原則入口網站、透過 REST API 或使用 Windows PowerShell 編輯安全性原則。 下列範例提供使用 REST API 編輯的指示。


## <a name="disable-security-policies"></a>停用安全性原則
如果預設的安全性原則正在產生與您的環境無關的建議, 您可以停用傳送建議的原則定義來停止它。
如需有關建議的進一步資訊, 請參閱[管理安全性建議](security-center-recommendations.md)。

1. 在資訊安全中心中, 從 [**原則 & 相容性**] 區段中, 按一下 [**安全性原則**]。

   ![原則管理](./media/tutorial-security-policy/policy-management.png)

2. 按一下您要停用建議的訂用帳戶或管理群組。

   > [!Note]
   > 請記住, 管理群組會將其原則套用至其訂用帳戶。 因此, 如果您停用訂用帳戶的原則, 而且訂用帳戶屬於仍使用相同原則的管理群組, 則您會繼續收到原則建議。 原則仍然會從管理層級套用, 而且仍會產生建議。

1. 按一下 [指派的原則]。

   ![停用原則](./media/tutorial-security-policy/security-policy.png)

1. 在 [**參數**] 區段中, 搜尋叫用您想要停用之建議的原則, 然後從下拉式清單中選取 [**已停用**]

   ![停用原則](./media/tutorial-security-policy/disable-policy.png)
1. 按一下 [儲存]  。
   > [!Note]
   > 停用原則變更最多可能需要12小時才會生效。


### <a name="configure-a-security-policy-using-the-rest-api"></a>使用 REST API 設定安全性原則

Azure 資訊安全中心與 Azure 原則的原生整合可讓您充分利用 Azure 原則的 REST API 來建立原則指派。 下列指示會引導您完成建立原則指派，以及自訂現有的指派。 

Azure 原則中的重要概念： 

- **原則定義**是一種規則 

- **方案**是原則定義的集合 (規則) 

- **指派**是將方案或原則應用至特定範圍 (管理群組、訂用帳戶等) 

資訊安全中心有內建的方案，其中包含所有其安全性原則。 若要評估 Azure 資源上的資訊安全中心原則，您應該建立管理群組的指派，或您想要評估的訂用帳戶。  

內建的方案都有預設啟用的所有資訊安全中心原則。 您可以選擇停用內建方案的特定原則，例如，您可以藉由將原則的效果參數值變更為**已停用**來套用所有資訊安全中心的原則，除了 **Web 應用程式防火牆**。 

### <a name="api-examples"></a>API 範例

在下列範例中，更換這些變數：

- **{scope}** 輸入您要套用原則的管理群組名稱或訂用帳戶名稱。
- **{policyAssignmentName}** 輸入[相關原則指派的名稱](#policy-names)。
- **{name}** 輸入您的名稱，或已核准原則變更的系統管理員名稱。

此範例將示範如何對於訂用帳戶或管理群組指派內建的資訊安全中心方案
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

此範例將示範如何對於已停用下列原則的訂用帳戶，指派內建的資訊安全中心方案： 

- 系統更新 (「systemUpdatesMonitoringEffect」) 

- 安全性設定 (「systemConfigurationsMonitoringEffect」) 

- 端點保護 (「endpointProtectionMonitoringEffect」) 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
此範例會示範如何移除指派：
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

### 原則名稱參考 <a name="policy-names"></a>

|資訊安全中心中的原則名稱|Azure 原則中顯示的原則名稱 |原則效果參數名稱|
|----|----|----|
|SQL 加密 |在 Azure 資訊安全中心中監視未加密的 SQL 資料庫 |sqlEncryptionMonitoringEffect| 
|SQL 稽核 |在 Azure 資訊安全中心中監視未加密的 SQL 資料庫 |sqlAuditingMonitoringEffect|
|系統更新 |在 Azure 資訊安全中心中監視缺少的系統更新 |systemUpdatesMonitoringEffect|
|儲存體加密 |稽核 Blob 未加密的儲存體帳戶 |storageEncryptionMonitoringEffect|
|JIT 網路存取 |監視 Azure 資訊安全中心中可能的網路 Just-In-Time (JIT) 存取 |jitNetworkAccessMonitoringEffect |
|自適性應用程式控制 |在 Azure 資訊安全中心中監視可能的應用程式允許清單 |adaptiveApplicationControlsMonitoringEffect|
|網路安全性群組 |在 Azure 資訊安全中心中監視寬鬆的網路存取 |networkSecurityGroupsMonitoringEffect| 
|安全性設定 |在 Azure 資訊安全中心中監視 OS 弱點 |systemConfigurationsMonitoringEffect| 
|端點保護 |在 Azure 資訊安全中心中監視缺少的 Endpoint Protection |endpointProtectionMonitoringEffect |
|磁碟加密 |在 Azure 資訊安全中心中監視未加密的 VM 磁碟 |diskEncryptionMonitoringEffect|
|弱點評估 |在 Azure 資訊安全中心中監視 VM 的弱點 |vulnerabilityAssessmentMonitoringEffect|
|Web 應用程式防火牆 |在 Azure 資訊安全中心中監視未受保護的 Web 應用程式 |webApplicationFirewallMonitoringEffect |
|新一代防火牆 |在 Azure 資訊安全中心中監視未保護的網路端點| |


### <a name="who-can-edit-security-policies"></a>誰可以編輯安全性原則？
資訊安全中心會使用角色型存取控制 (RBAC)，以提供可在 Azure 中指派給使用者、群組與服務的內建角色。 使用者開啟資訊安全中心時，只能看到與自己能夠存取之資源相關的資訊。 這表示會為使用者指派資源所屬訂用帳戶或資源群組的擁有者、參與者或讀取者角色。 除了這些角色，有兩個特定的資訊安全中心角色：

- 安全性讀取者：擁有資訊安全中心的檢視權限 (包括建議、警示、原則和健康情況)，但無法進行變更。
- 安全性系統管理員：擁有與安全性讀取者相同的檢視權限，而且還可以更新安全性原則，並解除建議和警示。



## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何在 Azure 原則中編輯安全性原則。 如要深入了解資訊安全中心，請參閱下列文章：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)：了解如何規劃和了解 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
* [在 Azure 資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理和回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀態。
* [取得 Azure 資訊安全中心的全租用戶可見性](security-center-management-groups.md)：了解如何設定 Azure 資訊安全中心的管理群組。
* [Azure 資訊安全中心常見問題](security-center-faq.md)：取得有關使用服務常見問題的答案。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。

若要深入了解 Azure 原則，請參閱[什麼是 Azure 原則？](../governance/policy/overview.md)
