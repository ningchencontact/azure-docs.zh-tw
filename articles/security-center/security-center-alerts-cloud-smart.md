---
title: 雲端中 Azure 資訊安全中心 （事件） 的智慧警示相互關聯 |Microsoft Docs
description: 本主題說明 Azure 資訊安全中心的融合使用雲端智慧警示相互關聯來產生安全性事件的方式。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571773"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>雲端中 Azure 資訊安全中心 （事件） 的智慧警示相互關聯

資訊安全中心會持續分析混合式雲端工作負載使用進階的分析和威脅情報來警示您關於惡意活動。

威脅的涵蓋範圍廣泛的成長並需要偵測甚至一點表示入侵增加時，它可以是一項挑戰給安全性分析師分級不同的警示，並找出實際的攻擊。 資訊安全中心可協助分析師處理警示的疲勞症和診斷攻擊發生時，藉由不同的警示與低精確度訊號關聯到安全性事件。

Fusion 是一種技術和分析的後端推動資訊安全中心的事件，讓它與不同的警示和內容放在一起的訊號相關聯。 融合的運作方式是查看不同的訊號回報的訂用帳戶資源，而且應該尋找常見的模式，將會顯示攻擊的進度，或透過共用的內容資訊，指出統一的回應程序向發出信號取得它們。

融合 analytics 結合安全性領域的知識，利用 AI 分析，探索新的攻擊模式發生時發出警示。 

資訊安全中心會利用其認知的意圖，相關聯警示 MITRE 攻擊矩陣協助定形安全性領域的知識。 此外，透過每個步驟的攻擊所收集的資訊，資訊安全中心可以排除，似乎是步驟的攻擊，但不是活動。  

由於攻擊通常會發生跨不同的租用戶，資訊安全中心可以結合分析攻擊序列所報告的每個訂用帳戶，它們識別為普遍警示的模式，而不只是要順帶一提每個相關聯的 AI 演算法其他。

在事件的調查，期間分析師通常需要額外的內容，才能觸達，關於威脅，以及如何減少這種情況的本質。 比方說，即使偵測到網路異常，而不需要了解什麼其他情況在網路上或在目標資源方面很難了解後續要採取哪些動作。 為了幫助，安全性事件可以包含的成品、 相關的事件和資訊。 供安全性事件的其他資訊會根據偵測到的威脅類型和環境的設定而有所不同。 

![安全性事件詳細資料](./media/security-center-alerts-cloud-smart/security-incident.png)

若要進一步了解安全性事件，請參閱[如何處理 Azure 資訊安全中心的安全性事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

