---
title: 了解如何稽核的虛擬機器內容
description: 了解 Azure 原則如何使用 Rego 和開放的原則代理程式來管理 Azure Kubernetes Service 上的叢集。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453901"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>了解 Azure Kubernetes 服務的 Azure 原則

與整合 azure 原則[Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) 叢集上套用大規模項目，並確保集中式且一致的方式。
藉由延伸使用[閘道管理員](https://github.com/open-policy-agent/gatekeeper)，則_許可控制站 webhook_的[開放的原則代理程式](https://www.openpolicyagent.org/)(OPA)，Azure 原則可讓您管理和合規性報告您的 Azure 資源和 AKS 叢集中從一個位置的狀態。

> [!NOTE]
> 供 AKS 使用 azure 原則處於有限預覽狀態，而且僅支援內建原則定義。

## <a name="overview"></a>概觀

若要啟用與 AKS 叢集搭配 AKS 使用 Azure 原則，採取下列動作：

- [選擇加入的預覽功能](#opt-in-for-preview)
- [安裝 Azure 原則的附加元件](#installation-steps)
- [Aks 指派原則定義](#built-in-policies)
- [等候驗證](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>選用功能的預覽

安裝 Azure 原則附加元件或之前啟用的任何服務功能，必須啟用您的訂用帳戶**Microsoft.ContainerService**資源提供者和**Microsoft.PolicyInsights**資源提供者，然後以加入預覽核准。 若要加入預覽，請遵循下列步驟在 Azure 入口網站中或使用 Azure CLI:

- Azure 入口網站：

  1. 註冊**Microsoft.ContainerService**並**Microsoft.PolicyInsights**資源提供者。 如需步驟，請參閱[資源提供者和類型](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)。

  1. 藉由按一下 [所有服務]  然後搜尋並選取 [原則]  ，在 Azure 入口網站中啟動 Azure 原則服務。

     ![搜尋所有服務中的原則](../media/rego-for-aks/search-policy.png)

  1. 選取 **加入預覽**Azure 原則頁面的左邊。

     ![加入的原則，AKS 預覽](../media/rego-for-aks/join-aks-preview.png)

  1. 選取您想加入預覽的訂用帳戶的資料列。

  1. 選取  **opt-in**訂用帳戶清單頂端的按鈕。

- Azure CLI：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Azure 原則的附加元件

_Azure 原則的附加元件_如 Kubernetes 連線到閘道管理員許可控制站的 Azure 原則服務。 附加元件，安裝到_azure 原則_命名空間，會制定下列函數：

- 檢查與 Azure 原則指派至 AKS 叢集
- 下載並快取原則的詳細資訊，包括_rego_原則定義，做為**configmaps**
- 執行完整掃描合規性檢查，以在 AKS 叢集上
- 稽核報告和合規性詳細資料回 Azure 原則

### <a name="installing-the-add-on"></a>安裝附加元件

#### <a name="prerequisites"></a>必要條件

您安裝在 AKS 叢集中的附加元件之前，必須先安裝預覽延伸模組。 完成這個步驟是使用 Azure CLI:

1. 您需要 Azure CLI 2.0.62 版或更新版本安裝並設定。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

1. AKS 叢集都必須是版本_1.10_或更高版本。 您可以使用下列指令碼來驗證您的 AKS 叢集版本：

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. 安裝新版_0.4.0_ Azure CLI 的 preview 延伸模組的 AKS， `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 如果您先前已安裝_aks 預覽_擴充功能，安裝任何更新使用`az extension update --name aks-preview`命令。

#### <a name="installation-steps"></a>安裝步驟

一旦完成必要條件，請在您想要管理 AKS 叢集中安裝 Azure 原則附加元件。

- Azure 入口網站

  1. 啟動 AKS 服務在 Azure 入口網站中的，依序按一下**所有的服務**，然後搜尋並選取**Kubernetes 服務**。

  1. 選取其中一個您的 AKS 叢集。

  1. 選取 **原則 （預覽）** Kubernetes 服務頁面的左邊。

     ![從 AKS 叢集的原則](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主頁面中，選取**啟用附加元件** 按鈕。

     ![啟用 AKS 附加元件的 Azure 原則](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 如果**啟用附加元件**按鈕呈現灰色，訂用帳戶尚未加入至 [預覽]。 請參閱[opt-in preview](#opt-in-for-preview)的所需的步驟。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>驗證並回報頻率

附加元件會檢查使用 Azure 原則的原則指派中變更每隔 5 分鐘。 在此重新整理週期內，附加元件中移除所有_configmaps_中_azure 原則_命名空間再重新建立_configmaps_閘道管理員使用。

> [!NOTE]
> 雖然_叢集系統管理員_可能有權限_azure 原則_命名空間，它已不建議或支援命名空間中進行變更。 重新整理週期期間，所做的任何手動變更都會遺失。

每隔 5 分鐘，叢集的完整掃描的附加元件呼叫。 收集由閘道管理員嘗試變更的完整掃描和即時的評估的詳細資料，在叢集中之後, 附加元件回報的結果加入 Azure 原則以納入[合規性詳細資料](../how-to/get-compliance-data.md)喜歡的任何 Azure 原則指派。 在稽核循環期間，會傳回只適用於作用中的原則指派的結果。

## <a name="policy-language"></a>原則的語言

管理 AKS 的 Azure 原則語言結構之後，現有的原則。 效果_EnforceRegoPolicy_用來管理您的 AKS 叢集，並採用_詳細資料_OPA 和閘道管理員所使用的特定屬性。 如需詳細資訊和範例，請參閱 < [EnforceRegoPolicy](effects.md#enforceregopolicy)效果。

做為一部分_details.policy_在原則定義中，Azure 原則的屬性會將 rego 原則的 URI 傳遞至附加元件。 Rego 是 OPA 和閘道管理員來驗證或變動的要求到 Kubernetes 叢集的支援的語言。 藉由支援現有的標準 Kubernetes 管理，Azure 原則讓您能夠重複使用現有的規則和配對它們與 Azure 原則的報表體驗整合的雲端合規性。 如需詳細資訊，請參閱 <<c0> [ 什麼是 Rego？](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)。

## <a name="built-in-policies"></a>內建原則

若要尋找管理 AKS 使用 Azure 入口網站的內建原則，請遵循下列步驟：

1. 在 Azure 入口網站中啟動 Azure 原則服務。 選取 **所有的服務**中的左的窗格，然後搜尋並選取**原則**。

1. 在 Azure 原則分頁左窗格中，選取**定義**。

1. 從 [類別] 下拉式清單方塊中，使用**全**清除篩選條件，然後選取**Kubernetes 服務**。

1. 選取原則定義，然後選取**指派** 按鈕。

> [!NOTE]
> 指派 Azure 原則，AKS 定義時**範圍**必須包含 AKS 叢集資源。

或者，使用[入口網站指派原則-](../assign-policy-portal.md)尋找和指派 AKS 原則的快速入門。 搜尋 Kubernetes 原則定義，而不是範例 '稽核 vm'。

## <a name="logging"></a>記錄

### <a name="azure-policy-add-on-logs"></a>Azure 原則的附加元件的記錄檔

為 Kubernetes 控制站/容器，Azure 原則的附加元件會在 AKS 叢集中保留記錄檔。 記錄檔中公開**Insights** AKS 叢集中的頁面。 如需詳細資訊，請參閱 <<c0> [ 了解 AKS 叢集與 Azure 監視器適用於容器的效能](../../../azure-monitor/insights/container-insights-analyze.md)。

### <a name="gatekeeper-logs"></a>閘道管理員記錄檔

若要啟用新的資源要求的閘道管理員記錄檔，請遵循[啟用和檢閱主要節點會記錄在 AKS 的 Kubernetes](../../../aks/view-master-logs.md)。
以下是新的資源要求檢視拒絕的活動時，查詢範例：

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

若要檢視從閘道管理員容器的記錄，請遵循[啟用和檢閱主要節點會記錄在 AKS 的 Kubernetes](../../../aks/view-master-logs.md) ，並檢查_kube apiserver_選項**的診斷設定**  窗格。

## <a name="remove-the-add-on"></a>移除附加元件

若要移除您的 AKS 叢集的 Azure 原則附加元件，請使用 Azure 入口網站或 Azure CLI:

- Azure 入口網站

  1. 啟動 AKS 服務在 Azure 入口網站中的，依序按一下**所有的服務**，然後搜尋並選取**Kubernetes 服務**。

  1. 選取您要停用 Azure 原則的附加元件的 AKS 叢集。

  1. 選取 **原則 （預覽）** Kubernetes 服務頁面的左邊。

     ![從 AKS 叢集的原則](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 在主頁面中，選取**停用附加元件** 按鈕。

     ![停用 AKS 附加元件的 Azure 原則](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>後續步驟

- 檢閱範例[「 Azure 原則範例](../samples/index.md)。
- 檢閱[原則定義結構](definition-structure.md)。
- 檢閱[了解原則效果](effects.md)。
- 了解如何[以程式設計方式建立原則](../how-to/programmatically-create.md)。
- 了解如何[取得合規性資料](../how-to/getting-compliance-data.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/index.md)來檢閱何謂管理群組。