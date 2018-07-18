---
title: 什麼是 Azure 資訊安全中心？| Microsoft Docs
description: 了解 Azure 資訊安全中心、其主要功能及運作方式。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
ms.locfileid: "29395644"
---
# <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
Azure 資訊安全中心為混合式雲端工作負載提供統一的安全性管理和進階威脅防護。 使用資訊安全中心，您可以在工作負載中套用安全性原則、限制暴露於威脅的可能性，以及偵測和回應攻擊。

為何使用資訊安全中心？

- **集中式原則管理** – 集中管理所有混合式雲端工作負載的安全性原則，確保符合公司或法規的安全性需求。
- **連續安全性評估** – 監視機器、網路、儲存體和資料服務以及應用程式的安全性，以找出潛在的安全性問題。
- **的建議** – 運用已排定優先順序和可操作的安全性建議，在攻擊者入侵之前修正安全性弱點。
- **進階雲端防禦** – 透過 Just-In-Time 存取管理連接埠及允許清單設定來降低威脅，以控制在虛擬機器上執行的應用程式。
- **已排定優先順序的警示和事件** - 藉由已排定優先順序的安全性警示和事件，首先專注處理最嚴重的威脅。
- **整合式安全性解決方案** - 收集、搜尋和分析各種來源 (包括已連線的合作夥伴解決方案) 的安全性資料。

[資訊安全中心 - 概觀] 可讓您快速檢視您 Azure 與非 Azure 工作負載的安全性狀態、探索並評定工作負載的安全性，並識別和降低風險。 內建儀表板可供立即深入掌握需要注意的安全性警示和弱點。

![概觀][1]

## <a name="centralized-policy-management"></a>集中式管理原則
安全性原則會定義工作負載的所需設定，並且協助確保符合公司或法規安全性需求。 在資訊安全中心，您可定義原則，並按照工作負載的類型或資料的敏感性量身打造這些原則。

資訊安全中心原則包含下列元件：

- **資料收集**：決定代理程式佈建和安全性[資料收集](security-center-enable-data-collection.md)設定。
- **安全性原則**：藉由編輯[安全性原則](security-center-policies.md)，決定何者控制資訊安全中心監視和建議。
- **電子郵件通知**：決定安全性連絡人和[電子郵件通知](security-center-provide-security-contact-details.md)設定。
- **定價層**：定義免費或標準[定價選取項目](security-center-pricing.md)。 您選擇的一層將決定範圍中的資源可用的資訊安全中心功能。

![安全性原則][2]

如需詳細資訊，請參閱[安全性原則概觀](security-center-policies-overview.md)。

## <a name="continuous-security-assessment"></a>持續安全性評估
資訊安全中心會分析計算資源、虛擬網路、儲存體和資料服務以及應用程式的安全性狀態。 連續評估可協助您找出潛在的安全性問題，例如遺漏安全性更新或已公開網路連接埠的系統。 選取 [保護] 區段中的圖格來檢視詳細資訊，包括資源清單和任何已找出的弱點清單。

![安全性健康情況監視][3]

如需詳細資訊，請參閱[安全性健康情況監視](security-center-monitoring.md)。

## <a name="actionable-recommendations"></a>可操作的建議
資訊安全中心會分析 Azure 和非 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 已設定優先順序的安全性建議清單會引導您完成處理安全性問題的程序。

![建議][4]

如需詳細資訊，請參閱[管理安全性建議](security-center-recommendations.md)。

## <a name="just-in-time-vm-access"></a>Just-In-Time 虛擬機器存取
利用 Just-In-Time 控制存取減少網路攻擊面，以管理 Azure 虛擬機器上的連接埠，大幅減少暴力密碼破解攻擊和其他網路攻擊。

![Just-In-Time 虛擬機器存取][5]

指定使用者如何連線到虛擬機器的規則。 如有需要，可以從資訊安全中心或透過 PowerShell 要求存取權。 只要要求符合規則，系統會針對要求的時間自動授與存取權。

如需詳細資訊，請參閱[使用 Just-In-Time 管理虛擬機器存取](security-center-just-in-time.md)。

## <a name="adaptive-application-controls"></a>自適性應用程式控制
藉由套用依特定 Azure 工作負載調整並由機器學習服務提供的允許清單建議，來封鎖惡意程式碼和其他不想要的應用程式。

![自適性應用程式控制][6]

檢閱並按一下以套用資訊安全中心所產生的建議應用程式允許清單設定規則，或編輯已經設定的規則。

如需詳細資訊，請參閱[自適性應用程式控制](security-center-adaptive-application.md)。

## <a name="prioritized-alerts-and-incidents"></a>已排定優先順序的警示和事件
資訊安全中心會使用進階分析和全球威脅情報，偵測傳入的攻擊和缺口後的活動。 系統會設定警示的優先順序並分組成為事件，協助您先將焦點放在最嚴重的威脅。 您也可以建立自己的自訂安全性警示。

![已排定優先順序的警示和事件][7]

您可以透過視覺化、互動式調查體驗，快速評定攻擊的範圍和影響，並使用預先定義或隨選查詢更深入探索安全性資料。

如需詳細資訊，請參閱[管理和回應安全性警示](security-center-managing-and-responding-alerts.md)。

## <a name="integrate-your-security-solutions"></a>整合安全性解決方案
在資訊安全中心，您可以收集、搜尋和分析各種來源 (包括已連線的合作夥伴解決方案，例如網路防火牆和其他 Microsoft 服務) 的安全性資料。

![整合安全性解決方案][8]

如需詳細資訊，請參閱[整合安全性解決方案](security-center-partner-integration.md)。

## <a name="next-steps"></a>後續步驟

- 若要開始使用資訊安全中心，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 資訊安全中心的定價層已經由 Azure 訂用帳戶啟用。 若要利用進階安全性管理和威脅偵測功能，您必須升級為標準定價層。 標準層的前 60 天免費。 如需詳細資訊，請參閱[資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。
- 如果您已準備好立即啟用資訊安全中心的標準層，[快速入門：將 Azure 訂用帳戶上架到資訊安全中心的標準層](security-center-get-started.md)會逐步引導您完成步驟。


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
