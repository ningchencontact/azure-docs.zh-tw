---
title: Azure 原則和 Azure 藍圖的安全性合規性
description: 確保符合性並對澳大利亞政府機關的 Azure 原則和 Azure 藍圖強制執行安全性, 因為它與 .ASD 的 ISM 和必要的8相關
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571740"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Azure 原則和 Azure 藍圖的安全性合規性

在您的 IT 環境內強制執行治理的挑戰, 不論是內部部署、雲端原生或混合式環境, 都是針對所有組織都存在。 您必須準備好強大的技術治理架構, 以確保您的 Microsoft Azure 環境符合設計、法規和安全性需求。

針對澳大利亞政府機關, 他們在評估風險時所要考慮的重要控制是在[澳大利亞網路安全中心 (ACSC) 資訊安全手冊](https://acsc.gov.au/infosec/ism/index.htm)(ISM) 中。 ISM 中詳述的大部分控制項都需要有效管理和強制執行技術管理。 您必須具備適當的工具, 才能在您的環境中評估和強制執行設定。

Microsoft Azure 提供兩項免費的服務, 協助您解決這些挑戰, Azure 原則和 Azure 藍圖。

## <a name="azure-policy"></a>Azure 原則

Azure 原則可讓您應用組織 IT 治理的技術元素。 Azure 原則包含持續成長的內建原則程式庫。 每個原則都會對目標 Azure 資源強制執行規則和效果。

一旦將原則指派給資源, 就可以評估該原則的整體合規性, 並在必要時加以補救。

此內建 Azure 原則的程式庫可讓組織快速地強制執行在 ACSC ISM 中找到的控制項類型。 控制項的範例包括:

* 監視虛擬機器是否有遺失的系統更新
* 使用更高的多重要素驗證許可權來審核帳戶
* 識別未加密的 SQL 資料庫
* 監視自訂 Azure 角色型存取控制 (RBAC) 的使用
* 限制可在其中建立資源的 Azure 區域

如果內建的 Azure 原則定義不符合治理或法規控制措施, 則可以建立和指派自訂定義。 所有 Azure 原則定義皆以 JSON 定義, 並遵循標準[定義結構](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)。 現有的 Azure 原則定義也可以複製並用來形成自訂原則定義的基礎。

將個別的 Azure 原則指派給資源, 特別是在複雜環境中, 或在具有嚴格法規需求的環境中, 可能會為您的系統管理員建立大量額外負荷。 為了協助解決這些挑戰, 可以將一組 Azure 原則群組在一起, 以形成一個 Azure 原則的計畫。 原則計畫是用來結合相關的 Azure 原則, 並將其當做群組一起套用時, 構成特定安全性或合規性目標的基礎。 Microsoft 正在新增內建的 Azure 原則計畫定義, 包括為了符合特定法規需求而設計的定義:

![法規合規性政策計畫](media/regulatory-initiatives.png)

所有 Azure 原則和方案都會指派給指派範圍。 此範圍定義于 Azure 訂用帳戶、Azure 管理群組或 Azure 資源群組層級。 一旦指派必要的 Azure 原則或原則方案, 組織就能夠在所有新建立的 Azure 資源上強制執行設定需求。

指派新的 Azure 原則或計畫將不會影響現有的 Azure 資源。 Azure 原則可以;不過, 讓組織能夠查看現有 Azure 資源的相容性。 已識別為不符合規範的任何資源, 都可以在組織的判斷中進行補救

### <a name="azure-policy-and-initiatives-in-action"></a>Azure 原則和行動計畫

可以在 [Azure 入口網站的原則] 區段中的 [定義] 節點底下找到可用的內建 Azure 原則和計畫定義:

![內建 Azure 原則定義](media/policy-definitions.png)

使用內建定義的程式庫, 您可以快速搜尋符合組織需求的原則, 檢查原則定義, 並將原則指派給適當的資源。 例如, ISM 針對所有具特殊許可權的使用者, 以及可存取重要資料存放庫的所有使用者, 都需要多重要素驗證 (MFA)。 在 Azure 原則您可以在 Azure 原則定義之間搜尋「MFA」:

![Azure MFA 原則](media/mfa-policies.png)

一旦識別出適當的原則之後, 您就可以將原則指派給所需的範圍。 如果沒有符合您需求的內建原則, 您可以複製現有的原則, 並進行所需的變更:

![複製現有的 Azure 原則](media/duplicate-policy.png)

Microsoft 也提供[GitHub](https://github.com/Azure/azure-policy)上的 Azure 原則範例集合作為「快速入門」, 讓您建立自訂的 Azure 原則。 這些原則範例可以直接複製到 Azure 入口網站內的 Azure 原則編輯器中。

建立 Azure 原則的計畫時, 您可以排序可用原則定義的清單, 其中包括內建和自訂, 並新增必要的定義。

例如, 您可以針對所有與 Windows 虛擬機器相關的原則, 搜尋可用的 Azure 原則定義清單。 然後, 這些定義會套用至設計來強制執行建議的虛擬機器強化做法的計畫:

![Azure 原則的清單](media/initiative-definitions.png)

將 Azure 原則或原則方案指派給指派範圍時, 您可能會排除 azure 管理群組或 Azure 資源群組, 以將 Azure 資源從原則的影響中排除。

### <a name="real-time-enforcement-and-compliance-assessment"></a>即時強制執行和合規性評估

符合下列條件時, 會執行範圍內 Azure 資源的 Azure 原則合規性掃描:

* 指派 Azure 原則或 Azure 原則方案時
* 當現有 Azure 原則或計畫的範圍變更時
* 透過 API 的隨選, 最多每小時10次掃描
* 每24小時一次-預設行為

對資源進行變更後15分鐘就會執行單一 Azure 資源的原則合規性掃描。

您可以透過原則合規性儀表板在 Azure 入口網站內檢查資源的 Azure 原則合規性總覽:

![Azure 原則合規性分數](media/simple-compliance.png)

整體資源合規性百分比圖是針對所有指派的 Azure 原則, 所有範圍內已部署資源的相容性匯總。 這可讓您識別不符合規範的環境中的資源, 並設計計畫以最佳補救這些資源。

原則合規性儀表板也會包含每個資源的變更歷程記錄。 如果資源被識別為不再符合指派的原則, 而且未啟用自動修復, 您可以查看進行變更的人員、變更的內容, 以及對該資源進行變更的時間。

## <a name="azure-blueprints"></a>Azure 藍圖

Azure 藍圖藉由結合 Azure 原則來擴充其功能:

* Azure RBAC
* Azure 資源群組
* [Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

藍圖可讓您建立環境設計, 從 Resource Manager 範本部署 Azure 資源、設定 RBAC, 以及藉由指派 Azure 原則來強制執行和審核設定。 藍圖會形成可編輯和 redeployable 的環境範本。 建立藍圖之後, 即可將它指派給 Azure 訂用帳戶。 一旦指派之後, 將會建立藍圖內定義的所有 Azure 資源, 並套用 Azure 原則。 您可以從 Azure 入口網站中的 Azure 藍圖主控台, 監視 Azure 藍圖中所定義資源的部署和設定。

已編輯的 Azure 藍圖必須在 Azure 入口網站中重新發佈。 每次重新發佈藍圖時, 藍圖的版本號碼都會遞增。 版本號碼可讓您判斷哪一個特定版本的藍圖已部署至組織的 Azure 訂用帳戶。 如有需要, 您可以將目前指派的藍圖版本更新為最新版本。

使用 Azure 藍圖部署的資源, 可以在部署時使用[Azure 資源鎖定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)來設定。 資源鎖定會防止意外修改或刪除資源。

Microsoft 正在開發各種產業和法規需求的 Azure 藍圖範本。 您可以在 Azure 入口網站或服務信任入口網站的 [ [Azure 安全性與合規性藍圖](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/)] 頁面中, 查看可用 Azure 藍圖定義的目前程式庫。

### <a name="azure-blueprint-artifacts"></a>Azure 藍圖成品

若要建立 Azure 藍圖, 您可以從空白的藍圖範本開始, 或使用其中一個現有的範例藍圖做為起點。 您可以將成品新增至藍圖, 以設定為部署的一部分:

![Azure 藍圖成品](media/blueprint-artifacts.png)

這些成品可能包括 Azure 資源群組和資源, 以及相關聯的 Azure 原則和原則計畫, 以強制執行您的環境所需的設定, 以符合您的法規需求, 例如, ISM 控制項系統強化。

這些成品也可以使用參數來設定。 當藍圖已指派給 Azure 訂用帳戶並已部署時, 就會提供這些值。 參數可讓您建立單一藍圖, 並用來將資源部署到不同的環境, 而不需要編輯基礎藍圖。

Microsoft 正在開發 Azure PowerShell 和 CLI Cmdlet 來建立和管理 Azure 藍圖, 其目的在於藍圖可透過 CI/CD 管線由組織維護及部署。

## <a name="next-steps"></a>後續步驟

本文說明如何使用 Azure 原則和 Azure 藍圖來強制執行治理與安全性。 既然您已在高層級公開, 請瞭解如何更詳細地使用每個服務:

* [Azure 原則總覽](https://docs.microsoft.com/azure/governance/policy/overview)
* [Azure 藍圖總覽](https://azure.microsoft.com/services/blueprints/)
* [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Azure 原則範例存放庫](https://github.com/Azure/azure-policy)
* [Azure 原則定義結構](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
