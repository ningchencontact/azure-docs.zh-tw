---
title: Azure 安全性控制-清查和資產管理
description: 安全性控制清查和資產管理
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930068"
---
# <a name="security-control-inventory-and-asset-management"></a>安全性控制：清查和資產管理

清查和資產管理建議著重于解決與主動管理（清查、追蹤和更正）所有 Azure 資源相關的問題，因此只會授與授權的資源存取權，以及未授權和未受管理的資源已識別並已移除。

## <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.1 | 1.1、1.2、1.3、1.4、9.1、12。1 | 客戶 |

使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源（例如計算、儲存體、網路、埠及通訊協定等）。  請確定您的租使用者中有適當（讀取）許可權，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

如何使用 Azure Resource Graph 建立查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看您的 Azure 訂用帳戶：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.2 | 1.5 | 客戶 |

將標籤套用至 Azure 資源，提供中繼資料以邏輯方式將其組織成分類法。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.3 | 1.6 | 客戶 |

使用標記、管理群組，以及個別的訂用帳戶（適當時）來組織和追蹤資產。 定期協調清查，並確保未經授權的資源會及時從訂用帳戶中刪除。

如何建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.4 | 2.1 | 客戶 |

定義已核准的 Azure 資源，以及適用于計算資源的已核准軟體。

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：未核准的 Azure 資源的監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.5 | 2.3、2.4 | 客戶 |

請使用 Azure 原則來限制可在您的訂用帳戶中建立的資源類型。

使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。 &nbsp;確保所有存在於環境中的 Azure 資源都已核准。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.6 | 2.3/2。4 | 客戶 |

使用 Azure 虛擬機器清查，自動收集虛擬機器上所有軟體的相關資訊。 [軟體名稱]、[版本]、[發行者] 和 [重新整理時間] 可從 Azure 入口網站取得。 若要取得安裝日期和其他資訊的存取權，請啟用來賓層級診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

如何啟用 Azure 虛擬機器清查：

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.7 | 2.5 | 客戶 |

使用 Azure 資訊安全中心的檔案完整性監視（變更追蹤）和虛擬機器清查來識別安裝在虛擬機器上的所有軟體。 您可以執行自己的程式來移除未經授權的軟體。 您也可以使用協力廠商解決方案來識別未經核准的軟體。

如何使用檔案完整性監視：

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

瞭解 Azure 變更追蹤：

https://docs.microsoft.com/azure/automation/change-tracking

如何啟用 Azure 虛擬機器清查：

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.8 | 2.6 | 客戶 |

使用 Azure 資訊安全中心的彈性應用程式控制，以確保只有授權的軟體會執行，且所有未經授權的軟體都會遭到封鎖而無法在 Azure 虛擬機器上執行。

如何使用 Azure 資訊安全中心適應性應用程式控制：

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.9 | 2.6 | 客戶 |

使用 Azure 原則來限制您可以在環境中布建的服務。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何拒絕具有 Azure 原則的特定資源類型：

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10：執行核准的應用程式清單

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.1 | 2.7 | 客戶 |

使用 Azure 資訊安全中心的彈性應用程式控制，指定規則可能或不適用的檔案類型。

如果這不符合需求，請執行協力廠商解決方案。

如何使用 Azure 資訊安全中心適應性應用程式控制：

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11：限制使用者透過腳本與 Azure Resource Manager 互動的能力

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.11 | 2.8 | 客戶 |

使用 Azure 條件式存取，藉由設定 &quot;Microsoft Azure Management&quot; 應用程式的 &quot;封鎖存取&quot;，限制使用者與 Azure Resource Manager 互動的能力。

如何設定條件式存取以封鎖對 Azure Resource Manager 的存取：

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.12 | 2.8 | 客戶 |

使用作業系統特定設定或協力廠商資源，以限制使用者在 Azure 計算資源內執行腳本的能力。

例如，如何在 Windows 環境中控制 PowerShell 腳本執行：

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實際或邏輯上隔離高風險應用程式

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 6.13 | 2.9 | 客戶 |

商務營運所需，但可能對組織產生較高風險的軟體，應該在自己的虛擬機器和/或虛擬網路中隔離，並使用 Azure 防火牆或網路安全性群組進行充分的保護。

如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立具有安全性設定的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>後續步驟

請參閱下一個安全性控制：[安全](security-control-secure-configuration.md)設定
