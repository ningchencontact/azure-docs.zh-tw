---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163939"
---
本文包含 Azure 虛擬機器的安全性建議。 實行這些建議可協助您依照我們的共同責任模型中所述，滿足您的安全性義務，並可改善 Web 應用程式解決方案的整體安全性。 如需 Microsoft 如何履行服務提供者責任的詳細資訊，請參閱[雲端運算的共同責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)。

本文中包含的一些建議可由 Azure 資訊安全中心自動監視。 Azure 資訊安全中心是保護 Azure 中資源的第一道防線。 它會定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會為您提供如何解決這些問題的建議。

- 如需 Azure 資訊安全中心建議的詳細資訊，請參閱[Azure 資訊安全中心中的安全性建議](../articles/security-center/security-center-recommendations.md)。
- 如需 Azure 資訊安全中心的詳細資訊，請參閱[什麼是 Azure 資訊安全中心？](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>建議

| Category | 建議 | 註解 | 資訊安全中心 |
|-|-|----|--|
| 一般 | 建立自訂 VM 映射時，請使用最新的更新 | 建立映射之前，請確定已針對作業系統和將成為映射一部分的所有應用程式安裝所有最新的更新。  | - |
| 一般 | 讓您的 Vm 保持在最新 | 您可以使用 Azure 自動化中的[更新管理](../articles/automation/automation-update-management.md)解決方案，在 Azure 中管理 Windows 和 Linux 電腦的作業系統更新 | [是](../articles/security-center/security-center-apply-system-updates.md) |
| 一般 | 備份您的虛擬機器 | [Azure 備份](../articles/backup/backup-overview.md)可協助您以最少的營運成本來保護您的應用程式資料。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 使用 Azure 備份，您執行 Windows 與 Linux 的虛擬機器會受到保護。 | - |
| 一般 | 使用多個 Vm 以獲得更高的復原能力和可用性 | 如果您的 VM 執行需要高可用性的應用程式，您應該要有多個 Vm 或[可用性設定組](../articles/virtual-machines/windows/manage-availability.md) | - |
| 一般 | 採用商務持續性和嚴重損壞修復（BCDR）策略 | Azure Site Recovery 可讓您從專為支援商務持續性需求而設計的不同選項中進行選擇。 它支援不同的複寫和故障處理案例。 如需詳細資訊，請參閱[關於 Site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| 身分識別和存取管理 | 集中化 VM 驗證 | 您可以使用[Azure AD 驗證](../articles/active-directory/develop/authentication-scenarios.md)來集中管理 Windows 和 Linux 虛擬機器的驗證。 | - |
| 資料安全性 | 將作業系統磁片加密 | [Azure 磁碟加密](../articles/security/azure-security-disk-encryption-overview.md)可協助您為 Windows 和 Linux IaaS 虛擬機器磁碟加密。 加密磁片可讓內容不需要必要的金鑰即可讀取。 磁片加密會保護儲存的資料免于未經授權的存取，如果磁片已複製，可能會發生此情況| [是](../articles/security-center/security-center-apply-disk-encryption.md) |
| 資料安全性 | 加密資料磁片 | [Azure 磁碟加密](../articles/security/azure-security-disk-encryption-overview.md)可協助您為 Windows 和 Linux IaaS 虛擬機器磁碟加密。 加密磁片可讓內容不需要必要的金鑰即可讀取。 磁片加密會保護儲存的資料免于未經授權的存取，如果磁片已複製，可能會發生此情況| -  |
| 資料安全性 | 限制已安裝的軟體 | 將已安裝的軟體限制為成功執行解決方案所需的功能，有助於減少解決方案的受攻擊面 | - |
| 資料安全性 | 使用防毒軟體/反惡意程式碼 | 在 Azure 中，您可以使用來自安全性廠商（例如 Microsoft、Symantec、Trend 微和 Kaspersky）的反惡意程式碼軟體。 此軟體可協助保護您的虛擬機器來抵禦惡意檔案、廣告軟體和其他威脅。 您可以根據應用程式工作負載來部署 Microsoft 反惡意程式碼保護，其中包含基本的預設安全性或 advanced 自訂設定。 如需有關 Azure Microsoft Antimalware 的詳細資訊，請參閱[azure 雲端服務和虛擬機器的 Microsoft Antimalware](../articles/security/azure-security-antimalware.md) | - |
| 資料安全性 | 安全地儲存金鑰和秘密 | 藉由提供應用程式擁有者安全的集中管理選項，簡化您的秘密和金鑰管理，可讓您降低意外洩露或流失的風險。 Azure Key Vault 可以安全地將您的金鑰儲存在經 FIPS 140-2 Level 2 認證的硬體安全模組（Hsm）中。 如果您需要使用 FIPs 140.2 層級3來儲存金鑰和密碼，您可以使用[Azure 專用 HSM](../articles/dedicated-hsm/overview.md) | - |
| 網路功能 | 限制對管理埠的存取 | 攻擊者會掃描公用雲端 IP 範圍以取得開放管理埠，並嘗試「輕鬆」的攻擊，例如常見密碼和已知未修補的弱點。 [即時（JIT）虛擬機器（VM）存取](../articles/security-center/security-center-just-in-time.md)可用於鎖定 Azure vm 的輸入流量、降低暴露于攻擊的風險，並在需要時輕鬆地連接到 vm。 | - |
| 網路功能 | 限制網路存取 | 網路安全性群組可讓您限制網路存取，並控制已公開端點的數目。 如需詳細資訊，請參閱[建立、變更或刪除網路安全性群組](../articles/virtual-network/manage-network-security-group.md) | - |
| 監視 | 監視您的虛擬機器 | 您可以使用[適用于 vm 的 azure 監視器](../articles/azure-monitor/insights/vminsights-overview.md)來監視 azure 虛擬機器和虛擬機器擴展集的狀態。 VM 的效能問題可能會導致服務中斷，違反可用性的安全性原則。 | - |

## <a name="next-steps"></a>後續步驟

請洽詢您的應用程式提供者，以查看是否有額外的安全性需求。 如需有關開發安全應用程式的詳細資訊，請參閱[保護開發檔](../articles/security/fundamentals/abstract-develop-secure-apps.md)。