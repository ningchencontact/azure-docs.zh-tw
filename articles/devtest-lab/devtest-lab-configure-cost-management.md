---
title: "檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢 | Microsoft Docs"
description: "了解 Azure DevTest Labs 每月估計成本趨勢圖表。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: v-craic
ms.openlocfilehash: 660180fac4f4743bc543b1babf7dbe921bf8c26b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢
研測實驗室的成本管理功能可協助您追蹤實驗室成本。 本文會示範如何使用「每月估計成本趨勢」  圖表，來檢視目前日曆月份的到目前為止的估計成本，以及目前日曆月份的預計月底成本。 本文也會顯示如何藉由設定消費目標和臨界值，當到達，觸發程序來將結果報告至您的 DevTest Labs 管理更容易實驗室成本。

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>檢視每月估計成本趨勢圖表
若要檢視「每月估計成本趨勢」圖表，請遵循下列步驟︰ 

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 視需要選取 [所有服務]，然後從清單中選取 [DevTest Labs]。 (您的實驗室可能已顯示在 [所有資源] 下方的儀表板上)。
1. 從實驗室清單中，選取所需的實驗室。  
1. 在實驗室的 [概觀] 區域中選取 [組態和原則]。   
1. 在左側**成本追蹤**，選取**成本趨勢**。

   下列螢幕擷取畫面顯示成本圖表範例。 
   
    ![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

**估計成本** 值是到目前行事曆月份為止累計的預估成本。 **預計成本** 是目前行事曆月份整個月的估計成本，是以前五天的實驗室成本來計算。

成本金額會無條件進位到下一個整數。 例如︰ 

* 5.01 會無條件進位到 6 
* 5.50 會無條件進位到 6
* 5.99 會無條件進位到 6

指出圖表上方，您會看到預設會在圖表的成本是*估計*成本使用[隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)提供率。 您也可以設定由圖表中會顯示您自己的消費目標[管理測試環境的成本目標。](#managing-cost-targets-for-your-lab)

此外，以下「未」  併入成本計算︰

* 目前不支援 CSP 和 Dreamspark 訂用帳戶，因為 Azure DevTest Labs 使用 [Azure 計費 API](../billing/billing-usage-rate-card-overview.md) 來計算實驗室成本，而 Azure 計費 API 並不支援 CSP 或 Dreamspark 訂用帳戶。
* 您的優惠費率。 目前，您無法使用 （您的訂用帳戶底下顯示），您有與交涉 Microsoft 或 Microsoft 合作夥伴服務費率。 會使用僅隨用隨付費率。
* 您的稅率
* 您的折扣
* 您的帳單貨幣。 目前，實驗室成本只會以美元貨幣顯示。

## <a name="managing-cost-targets-for-your-lab"></a>您的實驗室管理成本目標
DevTest Labs 可讓您管理所設定的消費目標使用，您便可以檢視每月估計成本趨勢圖表中更容易在您的實驗室中的成本。 DevTest Labs 也可以傳送您的通知時指定的目標消費或臨界值上限。 

1. 在您的實驗室**概觀**窗格中，選取**組態和原則**。
1. 在左側**成本追蹤**，選取**成本趨勢**。

    ![管理目標按鈕](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. 在**成本趨勢**窗格中，選取**管理目標**。

    ![管理目標按鈕](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. 在 [管理目標] 窗格中，指定您想要的消費目標和臨界值。 您也可以設定是否成本趨勢圖表上或透過 webhook 通知，會報告每個選取的閾值。

    ![管理目標窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 選擇您要追蹤的成本為目標的一段時間。
      - **每月**： 成本目標會追蹤每個月。
      - **固定**： 成本目標會追蹤您在開始日期和結束日期欄位中指定的日期範圍。 一般而言，這可能會對應與您的專案排定要執行的時間長度。
   - 指定**目標成本**。 比方說，這可能是您計劃多少花費在這個實驗室，在您定義的時段。
   - 啟用或停用任何臨界值選取您想要報告 – 增量為 25%-125%您指定**目標成本**。
      - **通知**： 當符合此臨界值時，您都會收到通知，您指定的 webhook URL。
      - **在圖表上繪製**： 中所述當符合此臨界值時，結果會繪製您可以檢視，在成本趨勢圖形上[檢視每月估計成本趨勢圖表](#viewing-the-monthly-estimated-cost-trend-chart)。
   - 如果您選擇**通知**符合臨界值時，您必須指定 webhook URL。 在成本整合區域中，選取**按一下此處以加入 integration**。

      在 [設定通知] 窗格中輸入 Webhook URL，然後選取**確定**。

       ![設定通知 窗格](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - 如果您指定**通知**，您必須定義 webhook URL。
      - 同樣地，如果您定義的 webhook URL，您必須設定**通知**至**上**成本臨界值 窗格中。
      - 您必須建立之前在這裡輸入 webhook。  

      如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章
* [讓 DevTest Labs 成本不失控的另外兩項須知](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [為何要設定成本臨界值？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>後續步驟
以下是接下來要嘗試的一些事項：

* [定義實驗室原則](devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室及其 VM 使用方式的各種原則。 
* [建立自訂映像](devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。 本文會示範如何從 VHD 檔案建立自訂的映像。
* [設定 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - DevTest Labs 支援根據 Azure Marketplace 映像建立 VM。 本文會示範在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
* [在實驗室中建立的 VM](devtest-lab-add-vm.md) -說明如何從基本映像建立 VM (可能是自訂或 Marketplace)，以及如何使用在 VM 中的成品。

