---
title: Azure 資訊安全中心的安全分數 | Microsoft Docs
description: " 使用 Azure 資訊安全中心的安全分數，設定安全性建議的優先權。 "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/5/2018
ms.author: rkarlin
ms.openlocfilehash: 3a377441758fcd7dd91deefb5cae91579e881498
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007040"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>改善您 Azure 資訊安全中心的安全分數


有這麼多提供安全優勢的服務，通常很難知道首先要採取哪些步驟來保護和加強您的工作負載。 Azure 安全分數會檢閱您的安全性建議，並為您排列這些建議的優先順序，讓您了解要先執行哪些建議。 這可協助您找出最嚴重的資訊安全漏洞，以便優先安排調查。 安全分數是一個可協助您評估工作負載安全性狀態的工具。

![安全分數儀表板](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>安全分數的計算

「資訊安全中心」會模擬安全分析師的工作，檢閱您的安全性建議並套用進階演算法來判斷每項建議的重要性。
「Azure 資訊安全中心」會不斷檢閱您的作用中建議並根據這些建議計算您的安全分數，建議的分數則是從將對工作負載安全性產生最大影響的嚴重性和安全性最佳做法導出。

「資訊安全中心」也會為您提供一個**整體安全分數**。 

**整體安全分數**是您所有建議分數的累計。 您可以在訂用帳戶或管理群組中檢視整體安全分數，具決於您選取的項目。 分數將根據所選訂用帳戶和這些訂用帳戶使用中的建議而有所不同。

 
若要檢查哪些建議會對您的安全分數產生最大影響，您可以檢視「資訊安全中心」儀表板中前 3 個最具影響力的建議，或使用 [安全分數影響] 資料行來排序建議清單刀鋒視窗中的建議。


檢視您的整體安全分數：

1. 在 Azure 資訊安全中心儀表板上按一下 [資訊安全中心]，然後按一下 [建議]。
2. 您可以在頂端看到每個所選訂用帳戶的安全分數，代表每個原則的分數。 
2. 在列出建議的下表中，您可以看到每個建議都有代表**安全分數影響**的資料行。 此數字代表如果您遵循建議，將會改善多少整體安全分數。 例如，在下面畫面中，如果您**補救容器安全性設定中的漏洞**，您的安全分數將增加 35 分。

   ![安全分數](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>個別安全分數

此外，若要檢視個別安全分數，您可以在個別建議刀鋒視窗中找到這些分數。  

**建議安全分數**會根據您狀況良好資源與總資源之間的比率來計算。 如果狀況良好的資源數目等於資源總數，您會獲得建議的最高安全分數 50。 若要讓您的安全分數接近最高分，請遵循建議來修正狀況不良的資源。

[建議的影響] 可讓您了解如果套用建議步驟，您的安全分數會提升多少。 例如，如果您的安全分數為 42 且 [建議的影響] 為 +3，則執行建議中所述的步驟可將您的安全分數提升至 45。

該建議會顯示未執行補救步驟時，工作負載會面臨哪些威脅。

![個別建議安全分數](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>後續步驟
本文說明如何使用 Azure 資訊安全中心的**安全分數**來改善安全性狀態。 若要深入了解資訊安全中心，請參閱：

* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。


