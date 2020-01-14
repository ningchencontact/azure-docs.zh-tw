---
title: Azure 安全性控制-安全設定
description: 安全性控制安全設定
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934303"
---
# <a name="security-control-secure-configuration"></a>安全性控制：安全設定

建立、執行及主動管理（追蹤、報告、更正） Azure 資源的安全性設定，以防止攻擊者利用易受攻擊的服務和設定。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.1 | 5.1 | 客戶 |

使用 Azure 原則或 Azure 資訊安全中心來維護所有 Azure 資源的安全性設定。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.2 | 5.1 | 客戶 |

使用 Azure 資訊安全中心建議 &quot;修復虛擬機器&quot; 上安全性設定中的弱點，以維護所有計算資源的安全性設定。

如何監視 Azure 資訊安全中心建議：

https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何補救 Azure 資訊安全中心建議：

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.3 | 5.2 | 客戶 |

使用 Azure 原則 [拒絕] 和 [在不存在時部署]，在您的 Azure 資源上強制執行安全設定。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 原則效果：

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.4 | 5.2 | 共用 |

基礎作業系統映射是由 Microsoft 所管理和維護。

不過，您可以使用 Azure Resource Manager 範本和/或 Desired State Configuration，套用組織所需的安全性設定。

如何從 Azure Resource Manager 範本建立 Azure 虛擬機器：

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

瞭解 Azure 虛擬機器的 Desired State Configuration：

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.5 | 5.3 | 客戶 |

如果使用自訂的 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 來安全地儲存和管理您的程式碼。

如何將程式碼儲存在 Azure DevOps：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 檔：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.6 | 5.3 | 客戶 |

如果使用自訂映射，請使用 RBAC 來確保只有授權的使用者可以存取影像。 針對容器映射，請將其儲存在 Azure Container Registry 中，並利用 RBAC 來確保只有授權的使用者可以存取影像。

瞭解 Azure 中的 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

瞭解 Container Registry 的 RBAC：

https://docs.microsoft.com/azure/container-registry/container-registry-roles

如何在 Azure 中設定 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.7 | 5.4 | 客戶 |

使用 Azure 原則來警示、審查及強制執行系統組態。 此外，開發處理常式和管線來管理原則例外狀況。

如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.8 | 5.4 | 客戶 |

使用適用于 Linux 的 Windows compute 或 Linux Chef 擴充功能的 Azure 計算延伸模組（例如 PowerShell Desired State Configuration）。

如何在 Azure 中安裝虛擬機器擴充功能：

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.9 | 5.5 | 客戶 |

使用 Azure 資訊安全中心來執行 Azure 資源的基準掃描

如何修復 Azure 資訊安全中心中的建議：

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.1 | 5.5 | 客戶 |

使用 Azure 資訊安全中心來執行適用于容器的 OS 和 Docker 設定的基準掃描。

瞭解 Azure 資訊安全中心容器建議：

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密 

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.11 | 13.1 | 客戶 |

搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。

如何與 Azure 受控識別整合：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault：

https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用受控識別提供 Key Vault 驗證：

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.12 | 4.1 | 客戶 |

在 Azure AD 中使用受控識別，以自動管理的身分識別提供 Azure 服務。 受控識別可讓您向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而您的程式碼中沒有任何認證。

如何設定受控識別：

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證暴露

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 7.13 | 13.3 | 客戶 |

執行認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

如何設定認證掃描器：

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>後續步驟

請參閱下一個安全性控制：[惡意程式碼防護](security-control-malware-defense.md)
