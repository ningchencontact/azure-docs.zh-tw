---
title: Azure Lighthouse 範例和範本
description: 這些範例和 Azure Resource Manager 範本會示範如何將客戶上線，以管理 Azure 委派的資源並支援 Azure Lighthouse 案例。
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553677"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse 範例

下表包含適用於 Azure Lighthouse 的 Azure Resource Manager 主要範本連結。 您也可在 [Azure Lighthouse 範例存放庫](https://github.com/Azure/Azure-Lighthouse-samples/)中找到這些檔案及其他更多資訊。

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>讓客戶在 Azure 委派的資源管理中上線

我們提供不同的範本來解決特定的上線案例。 選擇最適合的選項，並務必修改參數檔案以反映您的環境。 如需如何在部署中使用這些檔案的詳細資訊，請參閱[讓客戶在 Azure 委派的資源管理中上線](../how-to/onboard-customer.md)。

| **範本** | **說明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | 讓客戶的訂用帳戶在 Azure 委派的資源管理中上線。 您必須針對每個訂用帳戶個別執行部署。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | 讓一或多個客戶的資源群組在 Azure 委派的資源管理中上線。 單一資源群組請使用 **rgDelegatedResourceManagement**，或使用 **multipleRgDelegatedResourceManagement** 將相同訂用帳戶中的多個資源群組上線。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | 如果您已[將受控服務發佈到 Azure Marketplace](../how-to/publish-managed-services-offers.md)，可以選擇性地使用此範本來為已接受供應項目的客戶將資源上線。 參數檔案中的 marketplace 值必須符合您在發佈供應項目時所使用的值。 |

一般來說，每個要上線的訂用帳戶都需要個別部署，但您也可以跨多個訂用帳戶部署範本。

| **範本** | **說明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 跨多個訂用帳戶部署 Azure Resource Manager 範本。 |

## <a name="azure-policy"></a>Azure 原則

這些範例示範如何針對已為 Azure 委派的資源管理上線的訂用帳戶，使用 Azure 原則。

| **範本** | **說明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | 指派原則，以將標籤新增到委派的訂用帳戶，或從委派的訂用帳戶移除標籤 (使用修改效果)。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../how-to/deploy-policy-remediation.md)。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | 指派將針對委派指派進行審核的原則。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | 指派原則，以在委派的訂用帳戶中診斷 Azure Key Vault 資源 (使用 deployIfNotExists 效果)。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../how-to/deploy-policy-remediation.md)。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | 指派數個原則，以便診斷委派的訂用帳戶，並將所有 Windows 和 Linux VN 連線到原則所建立的 Log Analytics 工作區。 如需詳細資訊，請參閱[部署可以在委派的訂用帳戶中補救的原則](../how-to/deploy-policy-remediation.md)。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | 將計畫 (多個相關的原則定義) 套用到委派的訂用帳戶。 |

## <a name="azure-monitor"></a>Azure 監視器

這些範例示範如何使用 Azure 監視器，針對為 Azure 已委派資源管理上線的訂用帳戶建立警示。

| **範本** | **說明** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | 此範本會建立 Azure 警示，並與現有的動作群組連線。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | 建立多個以 Kusto 查詢為基礎的記錄警示。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | 當使用者將訂用帳戶委派給管理租用戶時，在租用戶中部署警示。|

## <a name="additional-cross-tenant-scenarios"></a>其他跨租用戶案例

這些範例說明可在跨租使用者管理案例中執行的各種工作。

| **範本** | **說明** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | 將儲存體帳戶部署到兩個不同的資源群組中。|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | 建立 Azure 管理服務、將其連結在一起並部署其他解決方案。 針對端對端部署，請使用 **rgWithAzureMgmt.json** 範本。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | 在目標 Azure 訂用帳戶中啟用和設定 Azure 資訊安全中心。 |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | 在委派訂用帳戶中的現有 Log Analytics 工作區上部署並啟用 Azure Sentinel。 |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | 這些範本可讓您將 Log Analytics VM 擴充功能部署到您的 Windows 和 Linux VM，並與指定的 Log Analytics 工作區連線 |

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)。
- 探索 [Azure Lighthouse 範例存放庫](https://github.com/Azure/Azure-Lighthouse-samples/)。
