---
title: Azure 資訊安全中心 (事件) 中的雲端智慧警示相互關聯 |Microsoft Docs
description: 本主題說明融合如何使用雲端智慧警示相互關聯來產生安全性事件, Azure 資訊安全中心。
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
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295852"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure 資訊安全中心 (事件) 中的雲端智慧警示相互關聯

資訊安全中心使用先進的分析與威脅情報, 持續分析混合式雲端工作負載, 以警示您惡意活動的相關資訊。

隨著威脅涵蓋範圍的廣度成長, 以及偵測到警鈴的暴露風險增加的需求, 安全性分析師可能會很難將不同的警示分級, 並找出實際的攻擊。 資訊安全中心透過將不同的警示和低精確度信號相互關聯至安全性事件, 協助分析師解決警示疲勞和診斷攻擊的情形。

融合是技術和分析後端, 可資訊安全中心事件, 讓它能夠將不同的警示和內容相關的信號相互關聯。 融合的運作方式是查看每個資源上的訂用帳戶所回報的不同信號, 並尋找使用共用內容資訊來顯示攻擊進展或信號的常見模式, 以指出整合的回應程式應為對其採取。

融合分析結合了安全性網域知識與 AI 來分析警示, 並在發生新的攻擊模式時進行探索。 

資訊安全中心利用 MITRE 攻擊對照表, 將警示與其認知意圖產生關聯, 以協助將安全性網域知識正規化。 此外, 藉由使用針對攻擊的每個步驟所收集的資訊, 資訊安全中心可以排除似乎是攻擊步驟的活動, 但卻不會。  

由於攻擊通常會在不同的租使用者之間發生, 資訊安全中心可以結合 AI 演算法來分析每個訂用帳戶上報告的攻擊順序, 以將其識別為普遍的警示模式, 而不只是與每個訂閱相關聯另一方面.

在調查事件的過程中, 分析師通常需要額外的內容, 才能觸達關於威脅本質的結論, 以及如何加以緩和。 例如, 即使偵測到網路異常, 又不知道網路上發生什麼事或目標資源, 就很難以瞭解接下來要採取哪些動作。 為了提供協助, 安全性事件可以包含構件、相關事件和資訊。 適用于安全性事件的其他資訊會根據偵測到的威脅類型和環境設定而有所不同。 

![安全性事件詳細資料](./media/security-center-alerts-cloud-smart/security-incident.png)

若要進一步瞭解安全性事件, 請參閱[如何處理 Azure 資訊安全中心中的安全性事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。

