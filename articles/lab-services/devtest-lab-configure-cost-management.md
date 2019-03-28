---
title: 檢視 Azure DevTest Labs 中的每月估計實驗室成本趨勢 | Microsoft Docs
description: 了解 Azure DevTest Labs 每月估計成本趨勢圖表。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517261"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>追蹤與在 Azure DevTest Labs 中對實驗室相關聯的成本
這篇文章會提供有關如何追蹤您的實驗室的成本資訊。 它會顯示您如何檢視預估費用 trent 目前日曆月份的實驗室。 本文也說明如何檢視實驗室中的每個資源的月-日成本。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>檢視每月估計的實驗室成本趨勢 
在本節中，了解如何使用**每月估計成本趨勢**圖表，以檢視目前日曆月份行事曆月份的預估的成本與日期和預計的結束的月份成本。 您也了解如何藉由設定費用目標和閾值，到達時，觸發程序來將結果回報給您的 DevTest Labs 中管理實驗室成本。

若要檢視「每月估計成本趨勢」圖表，請遵循下列步驟︰ 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中選取您的實驗室。  
4. 選取 **組態和原則**左側功能表上。  
4. 選取 **成本趨勢**中**成本追蹤**左側功能表上的一節。 下列螢幕擷取畫面顯示成本圖表範例。 
   
    ![成本圖表](./media/devtest-lab-configure-cost-management/graph.png)

    **估計成本** 值是到目前行事曆月份為止累計的預估成本。 **預計成本** 是目前行事曆月份整個月的估計成本，是以前五天的實驗室成本來計算。

    成本金額會無條件進位到下一個整數。 例如︰ 

   * 5.01 會無條件進位到 6 
   * 5.50 會無條件進位到 6
   * 5.99 會無條件進位到 6

     如圖表上方所述，您在圖表中看到的成本預設是使用[預付型方案](https://azure.microsoft.com/offers/ms-azr-0003p/)供應項目費率的估計成本。 您也可以[管理實驗室的成本目標](#managing-cost-targets-for-your-lab)，來設定在圖表中顯示您自己的費用目標。

     下列的成本*不*納入成本計算：

   * 目前不支援 CSP 和 Dreamspark 訂用帳戶，因為 Azure DevTest Labs 使用 [Azure 計費 API](../billing/billing-usage-rate-card-overview.md) 來計算實驗室成本，而 Azure 計費 API 並不支援 CSP 或 Dreamspark 訂用帳戶。
   * 您的供應項目費率。 目前，您無法使用您與 Microsoft 或 Microsoft 合作夥伴協議的供應項目費率 (顯示於您的訂用帳戶下方)。 只會使用預付型方案費率。
   * 您的稅率
   * 您的折扣
   * 您的帳單貨幣。 目前，實驗室成本只會以美元貨幣顯示。

### <a name="managing-cost-targets-for-your-lab"></a>管理您實驗室的成本目標
DevTest Labs 可讓您更輕鬆地管理您實驗室中的成本，方法是設定費用目標，以便您可在「每月估計成本趨勢」圖表中進行檢視。 當觸達指定的目標目標或閾值上限時，DevTest Labs 也可以傳送通知給您。 

1. 在 **成本趨勢**頁面上，選取**管理目標**。

    ![[管理目標] 按鈕](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. 在 **管理目標**頁面上，指定費用目標和閾值。 您也可以設定要在成本趨勢圖表上報告每個選取的閾值，還是透過 webhook 通知來報告每個選取的閾值。

    ![[管理目標] 窗格](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 選擇您需要追蹤成本目標的時間週期。
      - **每月**：會追蹤每個月的成本目標。
      - **固定**： 您指定的開始和結束日期的日期範圍內會追蹤成本目標。 一般而言，這些值會代表您的專案執行排定的時間長度。
   - 指定**目標成本**。 比方說，多少您計劃所定義的時段內花在這個實驗室。
   - 選取要啟用還是停用您需要報告的任何閾值 – 以 25% 增量計算 – 最高為您指定之**目標成本**的 125%。
      - **通知**:當符合此臨界值時，您會收到通知您指定的 webhook url。
      - **Vykreslit v grafu**:當符合此臨界值時，結果會繪製在成本趨勢圖表，您可以檢視，檢視每月估計成本趨勢圖表中所述。
   - 如果您選擇在符合閾值時 [通知]，就必須指定 webhook URL。 在成本整合區域中，選取 [按一下這裡可新增整合]。 請輸入**Webhook URL**設定通知 窗格，然後選取**確定**。

       ![[設定通知] 窗格](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - 如果您指定 [通知]，就必須定義 webhook URL。
     - 同樣地，如果您定義 webhook URL，就必須將 [成本閾值] 窗格中的 [通知] 設定為 [開啟]。
     - 在這裡輸入 webhook 之前，您必須先建立 webhook。  

       如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

## <a name="view-cost-by-resource"></a>資源檢視成本 
在實驗室中的每月成本趨勢功能可讓您查看多少就有花在目前的日曆月份中。 它也會顯示投影的直到月底，根據您過去七天的費用支出。 若要協助您了解為什麼在實驗室中消費達到臨界值在初期，您可以使用**依資源的成本**會顯示的月-日成本的功能**每個資源**資料表中。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。  
4. 選取 **組態和原則**左側功能表上。
5. 選取 **依資源的成本**中**成本追蹤**左側功能表上的一節。 您會看到與實驗室相關聯的每個資源相關聯的成本。 

    ![依資源區分的成本](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

這項功能可協助您輕鬆地識別的資源成本，最多，讓您可以採取動作，以降低實驗室消費。 例如，VM 的成本根據 VM 的大小。 愈多個 VM 的大小是成本。 以便您可以向 VM 擁有者，以了解為何需要這類的 VM 大小，您可以輕鬆找到大小的 VM 和擁有者，以及是否有機會降低大小。

[自動關機原則](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown)可協助您降低成本，您可以在一天的特定時間關閉實驗室 Vm。 不過，實驗室使用者可以選擇退出關機原則，這樣會增加執行中 VM 的成本。 若要查看如果它已被選擇外自動關機原則的資料表中，您可以選取 VM。 如果是這樣，您可以連絡 VM 擁有者，以尋找原因 VM 具有已選擇外的原則。
 
## <a name="next-steps"></a>後續步驟
以下是接下來要嘗試的一些事項：

* [定義實驗室原則](devtest-lab-set-lab-policy.md) - 了解如何設定用來管理您實驗室及其 VM 使用方式的各種原則。 
* [建立自訂映像](devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。 本文會示範如何從 VHD 檔案建立自訂的映像。
* [設定 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - DevTest Labs 支援根據 Azure Marketplace 映像建立 VM。 本文會示範在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
* [在實驗室中建立 VM](devtest-lab-add-vm.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立 VM，以及如何使用 VM 中的構件。

