---
title: Azure 安全性控制-資料保護
description: 安全性控制資料保護
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934508"
---
# <a name="security-control-data-protection"></a>安全性控制：資料保護

資料保護建議著重于解決與加密相關的問題、存取控制清單、身分識別型存取控制，以及資料存取的審核記錄。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護機密資訊的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.1 | 13.1 | 客戶 |

使用標記來協助追蹤儲存或處理敏感資訊的 Azure 資源。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感資訊的系統

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.2 | 13.2 | 客戶 |

針對開發、測試和生產環境，執行不同的訂用帳戶和/或管理群組。 資源應該以 VNet/子網分隔，並以適當的方式標記，並由 NSG 或 Azure 防火牆保護。 儲存或處理敏感性資料的資源應該充分地隔離。 針對虛擬機器儲存或處理敏感性資料，請在不使用時，執行原則和程式將其關閉。

如何建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立具有安全性設定的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆來設定警示或警示和拒絕：

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的機密資訊傳輸

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.3 | 13.3 | 客戶 |

在網路周邊部署自動化工具，監視未經授權的機密資訊傳輸，並在警示資訊安全性專業人員時封鎖這類傳輸。

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有機密資訊

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.4 | 14.4 | 共用 |

加密傳輸中的所有機密資訊。 請確定任何連線到您 Azure 資源的用戶端都能協商 TLS 1.2 或更高版本。

遵循 Azure 資訊安全中心的待用加密建議和傳輸中的加密（如果適用）。

瞭解使用 Azure 傳輸的加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用 active discovery 工具來識別敏感性資料

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.5 | 14.5 | 客戶 |

當您的特定服務在 Azure 中沒有可用的功能時，請使用協力廠商的 active discovery 工具來識別組織的技術系統所儲存、處理或傳輸的所有機密資訊，包括位於網站或遠端服務提供者，並更新組織的敏感性資訊清查。

使用 Azure 資訊保護來識別 Office 365 檔中的機密資訊。

使用 Azure SQL 資訊保護來協助分類和標籤儲存在 Azure SQL 資料庫中的資訊。

如何執行 Azure SQL 資料探索：

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

如何執行 Azure 資訊保護：

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.6 | 14.6 | 客戶 |

使用 Azure AD RBAC 來控制對資料和資源的存取，否則請使用服務特定的存取控制方法。

瞭解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

如何在 Azure 中設定 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用以主機為基礎的資料遺失防護來強制存取控制

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.7 | 14.7 | 客戶 |

執行協力廠商工具（例如自動化的主機型資料遺失防護解決方案），即使資料是從系統複製而來，也可以對資料強制執行存取控制。

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密機密資訊待用

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.8 | 14.8 | 客戶 |

在所有 Azure 資源上使用靜態加密。 Microsoft 建議讓 Azure 管理您的加密金鑰，但在某些情況下，您可以選擇管理您自己的金鑰。 

瞭解 Azure 中的待用加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

如何設定客戶管理的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：對重要 Azure 資源的變更進行記錄和警示

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 4.9 | 14.9 | 客戶 |

使用 Azure 監視器搭配 Azure 活動記錄，以針對重要的 Azure 資源進行變更時，建立警示。

如何建立 Azure 活動記錄事件的警示：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>後續步驟

請參閱下一個安全性控制：[弱點管理](security-control-vulnerability-management.md)
