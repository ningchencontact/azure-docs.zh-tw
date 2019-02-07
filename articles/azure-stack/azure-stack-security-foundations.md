---
title: 了解 Azure Stack 安全性控制措施
description: 身為服務管理員，您應了解適用於 Azure Stack 的安全性控制措施
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: efa97c18a63954239475338c85f2145b8c6c6ac6
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767264"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure Stack 基礎結構安全性狀態

*適用於：Azure Stack 整合式系統*

安全性考量和符合法規規範是使用混合式雲端的主要因素。 Azure Stack 專為這些情況所設計。 本文說明適用於 Azure Stack 的安全性控制項。

Azure Stack 中有兩個並存的安全性狀態層。 第一層是 Azure Stack 基礎結構，所含項目從硬體元件一直到 Azure Resource Manager。 第一層包含「系統管理員」和「租用戶」入口網站。 第二層則由租用戶所建立、部署和管理的工作負載所組成。 第二層包含虛擬機器和 App Service 網站等項目。

## <a name="security-approach"></a>安全性方法

Azure Stack 的安全性狀態是針對防禦新式威脅而設計的，且建置方式符合主要規範標準的需求。 因此，Azure Stack 基礎結構的安全性狀態是建立在兩個主要前提上：

 - **假想缺口**  
從假設系統已經被入侵的情況出發，將焦點放在「偵測及限制缺口影響」與僅嘗試防止攻擊之比較。 
 - **預設強化**  
因為基礎結構會在定義完善的硬體和軟體上執行，所以 Azure Stack 預設會「啟用、設定及驗證所有安全性功能」。

由於 Microsoft 是以整合系統的形式提供 Azure Stack，因此會由 Microsoft 定義 Azure Stack 基礎結構的安全性狀態。 就像在 Azure 中一樣，租用戶需負責定義其租用戶工作負載的安全性狀態。 本文件提供有關 Azure Stack 基礎結構之安全性狀態的基礎知識。

## <a name="data-at-rest-encryption"></a>待用資料加密
所有 Azure Stack 基礎結構和租用戶待用資料都會以 BitLocker 加密。 此加密可為 Azure Stack 儲存體元件實際遺失或遭竊的情況提供防護。 如需詳細資訊，請參閱 [Azure Stack 中的待用資料加密](azure-stack-security-bitlocker.md)。

## <a name="data-in-transit-encryption"></a>傳輸中資料加密
Azure Stack 基礎結構元件使用以 TLS 1.2 加密的通道進行通訊。 加密憑證會由基礎結構自行管理。 

所有外部基礎結構端點 (例如 REST 端點或 Azure Stack 入口網站) 都支援使用 TLS 1.2 進行安全通訊。 針對這些端點，必須提供加密憑證 (不論是來自協力廠商還是您的企業「憑證授權單位」)。 

雖然自我簽署的憑證可以用於這些外部端點，但 Microsoft 強烈建議不要使用這類憑證。 

## <a name="secret-management"></a>祕密管理
Azure Stack 基礎結構使用許多祕密 (例如密碼) 來運作。 這些祕密大多數都會自動輪換，因為它們是「群組受控服務」帳戶，每隔 24 小時會輪換一次。

針對其餘不是「群組受控服務」帳戶的祕密，您可以使用「具特殊權限的端點」中的指令碼以手動方式輪換。

## <a name="code-integrity"></a>程式碼完整性
Azure Stack 使用最新的 Windows Server 2016 安全性功能。 其中一個是 Windows Defender Device Guard，此功能提供應用程式允許清單功能，可確保只有經授權的程式碼會在 Azure Stack 基礎結構內執行。 

已授權的程式碼是由 Microsoft 或 OEM 夥伴簽署。 已簽署的授權程式碼包含在 Microsoft 所定義之策略中所指定的允許軟體清單中。 換句話說，只有獲核准在 Azure Stack 基礎結構中執行的軟體才能執行。 系統會封鎖任何執行未經授權程式碼的嘗試並產生稽核。

Device Guard 原則也會防止協力廠商代理程式或軟體在 Azure Stack 基礎結構中執行。

## <a name="credential-guard"></a>Credential Guard
Azure Stack 中的另一個 Windows Server 2016 安全性功能就是 Windows Defender Credential Guard，此功能可用來防止 Azure Stack 基礎結構認證遭受「傳遞雜湊」(Pass-the-Hash) 和「傳遞票證」(Pass-the-Ticket) 攻擊。

## <a name="antimalware"></a>反惡意程式碼
Azure Stack 中的每個元件 (Hyper-V 主機和虛擬機器兩者) 都受到「Windows Defender 防毒軟體」保護。

在已連線的情況下，一天當中會套用防毒定義及引擎更新多次。 在連線中斷的情況下，則會隨每月 Azure Stack 更新套用反惡意程式碼軟體更新。 如需詳細資訊，請參閱[更新 Azure Stack 上的 Windows Defender 防毒軟體](azure-stack-security-av.md)。

## <a name="constrained-administration-model"></a>限制型管理模型
Azure Stack 中的管理是透過三個進入點來控制，每個進入點都有特定的目的： 
1. [系統管理員入口網站](azure-stack-manage-portals.md)針對日常管理作業提供點選體驗。
2. Azure Resource Manager 會透過 PowerShell 和 Azure CLI 所使用的 REST API 公開「系統管理員入口網站」的所有管理作業。 
3. 針對特定的低階作業 (例如資料中心整合或支援案例)，Azure Stack 公開了一個稱為[具特殊權限的端點](azure-stack-privileged-endpoint.md)的 PowerShell 端點。 這個端點只公開一組已加入白名單中的 Cmdlet，並且經常受到稽核。

## <a name="network-controls"></a>網路控制措施
Azure Stack 基礎結構隨附多層的網路「存取控制清單」(ACL)。 ACL 可防止使用者對基礎結構元件進行未經授權的存取，並將基礎結構通訊僅限於其運作所需的路徑。 

網路 ACL 會在三個層級強制執行：
1.  機架頂端 (Top of Rack) 交換器
2.  軟體定義網路
3.  操作系統防火牆的主機和虛擬機器

## <a name="regulatory-compliance"></a>法規遵循

Azure Stack 已通過第三方獨立的稽核公司正式評量。 因此，可以取得 Azure Stack 基礎結構如何滿足幾個主要合規性標準之適用控制項的相關文件。 由於標準包括一些與數個人員以及處理程序相關的控制項，因此該文件不是 Azure Stack 的認證。 相反地，客戶也可以使用這份文件來快速啟動他們的認證程序。

評量包含下列標準：

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) 處理支付卡產業的問題。
- [CSA 雲端控制矩陣](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview)是跨多個標準的完整對應，，包括 FedRAMP Moderate、ISO27001、HIPAA、HITRUST、ITAR、NIST SP800-53 和其他項目。
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) 適用於政府客戶。

可以在 [Microsoft 服務信任入口網站](https://servicetrust.microsoft.com/ViewPage/Blueprint)上找到合規性文件。 合規性指南是受保護的資源，需要您使用 Azure 雲端服務認證登入。

## <a name="next-steps"></a>後續步驟

- [了解如何在 Azure Stack 中輪替使用祕密](azure-stack-rotate-secrets.md)
- [適用於 Azure Stack 的 PCI-DSS 和 CSA-CCM 文件](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [適用於 Azure Stack 的 DoD 和 NIST 文件](https://servicetrust.microsoft.com/ViewPage/Blueprint)
