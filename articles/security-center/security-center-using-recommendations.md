---
title: 使用 Azure 資訊安全中心建議增強安全性 | Microsoft Docs
description: " 了解如何使用「Azure 資訊安全中心」中的安全性原則和建議，來協助降低安全性攻擊的危害。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 60bb1c3b81ef990993a2ce659a2b189c9d8a0eba
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967965"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>使用 Azure 資訊安全中心建議增強安全性
您可以設定安全性原則，然後實作 Azure 資訊安全中心提供的建議，以降低發生重大安全性事件的機會。 本文說明如何使用資訊安全中心的安全性原則和建議，以協助減少安全性攻擊。 

資訊安全中心會自動執行來分析您的 Azure 資源的安全性狀態的連續掃描。 當資訊安全中心發現潛在的安全性弱點時會建立建議，引導您完成設定所需安全性控制項的程序。 資訊安全中心會更新其建議，在 24 小時內，但有下列例外狀況：

- 作業系統安全性組態的建議事項會在 48 小時內更新
- 端點保護問題的建議會在 8 小時內更新

## <a name="scenario"></a>案例
此案例示範如何使用資訊安全中心，透過監控資訊安全中心建議並採取動作，以協助降低發生安全性事件的機會。 此案例使用虛構公司 Contoso，以及資訊安全中心[規劃與操作指南](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)中出現的角色。 在此案例中，我們著重於下列人物的角色：

![案例角色](./media/security-center-using-recommendations/scenario-roles.png)

Contoso 最近將一些內部部署資源移轉至 Azure。 Contoso 想要保護其資源，並減少雲端中其資源的弱點。

## <a name="use-azure-security-center"></a>使用 Azure 資訊安全中心
來自 Contoso IT 安全性部門的 David，已經選擇將 Contoso 訂用帳戶上的資訊安全中心上架到 Azure 資訊安全中心，以防止和偵測安全性弱點。 

資訊安全中心會自動分析 Contoso Azure 資源的安全性狀態並套用預設安全性原則。 當資訊安全中心發現潛在的安全性弱點時，它會根據安全性原則中設定的控制來提出**建議**。 

David 會在其所有訂用帳戶中執行 Azure 安全性標準層，以取得整套可用的建議和安全性功能。 Jeff 也會將其尚未遷移至雲端的現有內部部署伺服器上架，以便在其 [Windows](quick-onboard-windows-computer.md) 和 [Linux](quick-onboard-linux-computer.md) 伺服器上利用資訊安全中心的混合式支援。

Jeff 是雲端工作負載擁有者。 Jeff 負責根據 Contoso 的安全性原則，套用安全性控制項。 

Jeff 會執行下列工作：

- 監視資訊安全中心提供的安全性建議
- 評估安全性建議，並決定是否應該套用或解除
- 套用安全性建議

### <a name="remediate-threats-using-recommendations"></a>利用建議補救威脅
在日常監視活動中，Jeff 會登入 Azure 並開啟資訊安全中心。 

1. Jeff 會選取其工作負載訂用帳戶。

2. Jeff 會檢查其**安全分數**，整體了解訂用帳戶的安全狀態，而他看到他的分數為 548。

3. Jeff 必須決定要先處理哪些建議。 所以 Jeff 會按一下安全分數，並根據建議可改善其[安全分數影響](security-center-secure-score.md)的程度，開始處理建議。

4. Jeff 有大量已連線的 VM 和伺服器，因此決定著重於 [計算和應用程式]。

5. 當 Jeff 按一下 [計算和應用程式] 時，他會看到一份建議並根據安全分數影像處理建議。

6. Jeff 有許多網際網路面向 VM，而且因為其連接埠已公開，所以他會擔心攻擊者可能會取得伺服器的控制權。 因此 Jeff 會選擇使用 (**Just-In-Time VM 存取**)[security-center-just-in-time.md]。

Jeff 繼續瀏覽高優先權和中優先順序的建議，然後進行實作決策。 針對每項建議，Jeff 會查看資訊安全中心所提供的詳細資訊，以了解哪些資源受到影響、安全分數影響為何、每項建議的用意，以及如何緩和每個問題的補救步驟。

## <a name="conclusion"></a>結論
監控資訊安全中心的建議有助於在發生攻擊之前消除安全性弱點。 當您補救建議時，您的安全分數和工作負載的安全性狀態都會改善。 資訊安全中心會自動探索您所部署的新資源、根據您的安全性原則進行評估，並提供新的建議來保護它們。


## <a name="next-steps"></a>後續步驟
請確定您已備妥監視程序，您會在監視過程中定期檢查資訊安全中心的建議，以確保資源在一段時間內保持安全無虞。

本案例示範如何使用資訊安全中心的安全性原則和建議，以協助減少安全性攻擊。 請參閱[事件回應案例](security-center-incident-response.md)，以了解如何在攻擊發生前備妥事件回應計劃。

了解如何以[事件回應](security-center-incident-response.md)來回應威脅。
