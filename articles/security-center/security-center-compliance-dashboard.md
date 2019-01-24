---
title: 使用 Azure 資訊安全中心改善您的法規合規性 | Microsoft Docs
description: 教學課程：了解如何使用 Azure 資訊安全中心改善您的法規合規性。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: dbff728c888e9e2f83d9a82ca9819bf9dd0b89e9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2019
ms.locfileid: "54268132"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>教學課程：改善法規合規性
---

Azure 資訊安全中心會利用法規合規性儀表板，協助您簡化達到法規合規性需求的程序。 在儀表板中，資訊安全中心會根據 Azure 環境的連續評估，提供合規性狀況的深入解析。 資訊安全中心所執行的評估會根據安全性最佳做法，來分析混合式雲端環境中的風險因素。 這些評量會對應到一組所支援標準中的合規性控制項。 在法規合規性儀表板中，您可以清楚看到環境內所有這些評估在特定標準或法規下的狀態。 當您採取建議動作並降低環境中的風險因素後，您可以看到合規性狀況已改善。

在本教學課程中，您將了解如何：

-   使用法規合規性儀表板評估您的法規合規性

-   採取建議動作來改善您的合規性狀況

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>必要條件

若要逐步執行本教學課程中涵蓋的功能，您必須有資訊安全中心的標準定價層。 您可以免費試用資訊安全中心標準層。
若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。 [將 Azure 訂用帳戶上架到資訊安全中心標準定價層](https://docs.microsoft.com/en-us/azure/security-center/security-center-get-started)快速入門會為您逐步解說如何升級至「標準」定價層。

##  <a name="assess-your-regulatory-compliance"></a>評估法規合規性

資訊安全中心會持續評估資源的組態，以識別安全性問題與弱點。 這些評估會顯示為建議，專門用於改善您的安全性防護。 在法規合規性儀表板中，您可以檢視一組合規性標準及其需求，其中支援的需求會對應到適當的安全性評量。 這可讓您根據這些評估的狀態，檢視與標準有關的合規性狀況。

法規合規性儀表板檢視可協助您將注意力放在合規性與重要標準或法規之間的差異。 此重點檢視也可讓您在一段時間內，持續監視動態雲端和混合式環境中的合規性分數。

>[!NOTE]
> 目前支援的法規標準如下：Azure CIS、PCI DSS 3.2、ISO 27001 和 SOC TSP。 其他標準會在開發時反映在儀表板中。

1.  在資訊安全中心主功能表中的 [原則與合規性] 底下，選取[法規合規性儀表板]。 <br>
在畫面頂端，您會看到合規性狀態的概觀和一組支援的合規性規章顯示在儀表板上。 您可以看到整體的合規性分數，以及每個標準評量的成功和失敗數目。

    ![電腦描述高信賴度](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  選取與您相關的合規性標準索引標籤。 您會看到該標準的所有控制項清單。 對於適用的控制項，您可以檢視與該控制項相關的成功和失敗評量詳細資料。 有些控制項呈現灰色。這些控制項沒有任何相關聯的資訊安全中心評量。 您必須分析這些控制項的需求，並自行在環境中對其進行評估。 其中有些項目可能與處理程序相關，而非技術性項目。

    ![合規性索引標籤](./media/security-center-compliance-dashboard/compliance-pci.png)

3.  選取 [所有] 索引標籤，即可查看所有相關的資訊安全中心建議和其相關標準。 此檢視可用於識別特定建議所影響的所有不同標準。 <br> 您可能可以使用此檢視對必須處理的建議排列優先順序。 例如，如果您在多個資源上看到「**對訂用帳戶上具有擁有者權限的帳戶啟用 MFA**」的建議失敗，而且關係到多個標準，那麼處理此建議將會對整體合規性分數有很大的影響。

    ![合規性分數的影響](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

## <a name="improve-your-compliance-posture"></a>改善您的合規性狀況


基於法規合規性儀表板中的資訊，您可以藉由在儀表板內直接處理建議來改善合規性狀況。

1.  按一下儀表板上顯示的任何失敗評量，即可檢視該項建議的詳細資料。 每項建議都包含一組應遵循著來解決問題的補救步驟。

2.  您可以選取特定資源以檢視更多詳細資料，並處理該資源的建議。 <br>例如，在 [Azure CIS 標準] 索引標籤上，您可以按一下建議：**需要安全傳輸至儲存體帳戶**。

    ![合規性建議](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. 當您按一下建議資訊並選取狀況不良的資源後，系統會直接將您引導至 Azure 入口網站中啟用**安全儲存體傳輸**的體驗。<br>如需有關如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

    ![合規性建議](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  在您採取行動來處理建議之後，您會看到合規性儀表板報告中產生了影響，因為合規性分數已改善。

    > [!NOTE]
    > 系統大約會每隔 12 小時執行一次評量，因此您只會在評量執行後看到合規性資料上的影響。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用資訊安全中心的法規合規性儀表板來執行下列動作：

-   檢視及監視與重要標準和法規相關的合規性狀況。

-   藉由處理相關建議來改善合規性狀態，並觀看合規性分數的改善。

法規合規性儀表板可以大幅簡化合規性程序，並大幅減少為 Azure 和混合式環境收集合規性辨識項所需的時間。

若要深入了解資訊安全中心，請參閱：

-   [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。

-   [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)--了解如何使用 Azure 資訊安全中心的建議來協助您保護 Azure 資源。

-   [改善 Azure 資訊安全中心的安全分數](security-center-secure-score.md)--了解要如何設定弱點與安全性建議的優先順序，才最能改善安全性狀況。

-   [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
