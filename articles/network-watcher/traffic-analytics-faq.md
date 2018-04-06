---
title: Azure 流量分析常見問題集 | Microsoft Docs
description: 取得一些關於「流量分析」常見問題的解答。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: 01d5150bff8642a1a3fe9b7ac063923916f191c0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>流量分析常見問題集

1.  使用流量分析有哪些必要條件？

    流量分析需要下列必要條件：

    - 已啟用網路監看員的訂用帳戶
    - 已針對您要監視的 NSG 啟用 NSG 流量記錄
    - 用來儲存原始 flog 記錄的 Azure 儲存體帳戶
    - 具有讀取和寫入權限的 Log Analytics (OMS) 工作區
    - 必須將 Microsoft.Network 提供者的下列動作指派給您的帳戶：

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  在哪些 Azure 區域中可使用流量分析？

    在預覽版本中，您可以在下列任何一個**支援的區域**中使用 NSG 的流量分析：美國中西部、美國東部、美國東部 2、美國中北部、美國中南部、美國中部、美國西部、美國西部 2、西歐、北歐、英國西部、英國南部、澳大利亞東部和澳大利亞東南部。 Log Analytics 工作區必須存在於美國中西部、美國東部、西歐、澳大利亞東南部或英國南部區域中。

3.  啟用了流量記錄的 NSG 是否可與我的 OMS 工作區位於不同的區域中？

    yes

4.  可以在單一工作區內設定多個 NSG 嗎？

    yes

5.  可以使用現有的 OMS 工作區嗎？

    是的，如果您選取現有的工作區，請確定它已遷移至新的查詢語言。 如果您不想升級工作區，則需要建立新的工作區。 如需有關新查詢語言的詳細資訊，請參閱 [Azure Log Analytics 升級為新的記錄搜尋](../log-analytics/log-analytics-log-search-upgrade.md)。

6.  是否可以讓我的 Azure 儲存體帳戶位於某個訂用帳戶，而讓我的 OMS 工作區位於不同的訂用帳戶中？

    yes

7.  是否可以將原始記錄儲存在不同訂用帳戶的不同儲存體帳戶中？

    編號 您可以將原始記錄儲存在已啟用 NSG 流量記錄的任何儲存體帳戶中，不過，儲存體帳戶和原始記錄必須位於相同的訂用帳戶和區域中。

8.  如果在為 NSG 設定流量分析時收到「找不到」錯誤，該如何解決？

    請選取問題 2 中所列的支援區域。 如果您選取不受支援的區域，就會收到「找不到」錯誤。

9.  我在 NSG 流量記錄底下收到 NSG 狀態為「無法載入」，接下來該怎麼做？

    為了讓流量記錄正常運作，您必須註冊 Microsoft.Insights 提供者。 如果您不確定是否已為訂用帳戶註冊 Microsoft.Insights 提供者，請取代下列命令中的「xxxxx xxxxx-xxxxxx xxxx」，然後從 PowerShell 執行下列命令：

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. 我已設定解決方案。 為什麼在儀表板上看不到任何項目？

    第一次時，儀表板最久可能需要 30 分鐘才會出現。 解決方案必須先彙總足夠資料以衍生有意義的深入解析，然後才能產生報告。 

11.  如果我收到下列訊息：「在選取的時間間隔內，我們無法在此工作區中找到任何資料。 請嘗試變更時間間隔，或是選取不同的工作區」，該如何解決？

        請嘗試下列選項：
        - 嘗試在上方列中變更時間間隔。
        - 在上方列中選取不同的 Log Analytics 工作區
        - 如果是最近才啟用的話，請嘗試於 30 分鐘過後再存取流量分析
    
        如果問題仍然存在，請在[使用者心聲 (User Voice) 論壇](https://feedback.azure.com/forums/217313-networking?category_id=195844)中提出考量。

12.  如果我收到下列訊息：「1) 正在對您的 NSG 流量記錄進行第一次分析。 此程序可能需要 20-30 分鐘才能完成。 請在一段時間過後再回來查看。 2) 如果上述步驟無效，且您的工作區屬於免費的 SKU，請在這裡檢查您的工作區使用量以進行配額驗證，或請參閱常見問題集以取得進一步資訊」，該如何解決？

        您可能會因為以下理由而收到錯誤：
        - 流量分析可能是最近才啟用的，且可能正在先彙總足夠資料以衍生有意義的深入解析，然後才能產生報告。 在此情況下，請於 30 分鐘後再試一次
        - 您的 OMS 工作區是處於免費的 SKU，並已超過配額限制。 在此情況下，您可能需要在較大容量的 SKU 中使用工作區。
    
        如果問題仍然存在，請在[使用者心聲 (User Voice) 論壇](https://feedback.azure.com/forums/217313-networking?category_id=195844)中提出考量。
    
13.  如果我收到下列訊息：「看起來我們有資源資料 (拓撲)，但沒有任何流程資訊。 於此同時，請按一下這裡以查看資源資料，並參閱常見問題集以取得進一步資訊」，該如何解決？

        您會在儀表板上看到資源資訊；不過，不會有任何流程相關的統計資料。 資源之間沒有通訊流量，所以可能不會有資料。 請稍候 60 分鐘，然後重新檢查狀態。 如果您確定資源之間有通訊流程，請在[使用者心聲 (User Voice) 論壇](https://feedback.azure.com/forums/217313-networking?category_id=195844)中提出考量。

14.  流量分析的價格為何？

        流量分析的計量方式可用來加強減少記錄檔，並且將增強型記錄檔儲存於 Log Analytics 工作區。 在預覽中，不會對於加強減少記錄檔收取流量分析的費用，不過，工作區中的資料保留只會以公佈的費率計費。 出現流量分析的價格後，就會更新此回應。

15.  如何在地理地圖檢視中使用鍵盤來巡覽？

        地理地圖頁面包含兩大區段：
    
        - **橫幅**：放在地理地圖頂端的橫幅可讓您透過各種按鈕 (如「部署」/「無部署」/「使用中」/「非使用中」/「已啟用流量分析」/「未啟用流量分析」/「來自國家/地區的流量」/「良性」/「惡意」/「允許的惡意流量」及「圖例資訊」)，來選取流量分配篩選條件。 選取定義的按鈕之後，系統就會對地圖套用個別篩選條件，例如，如果使用者選取橫幅底下的 [作用中] 篩選條件按鈕，則地圖會醒目提示您部署中的「作用中」資料中心。
        - **地圖**：放在橫幅下方的 [地圖] 區段會顯示 Azure 資料中心與國家/地區之間的流量分配。
    
        **橫幅上的鍵盤巡覽**
    
        - 根據預設，系統會在地理地圖頁面的橫幅上選擇篩選條件「Azure DC」按鈕。
        - 若要巡覽至另一個篩選條件按鈕，您可以使用 `Tab` 或 `Right arrow` 鍵來移至下一個按鈕。 若要反向巡覽，請使用 `Shift+Tab` 或 `Left arrow` 鍵。 順向巡覽方向的優先順序是由左到右，然後由上到下。
        - 按 `Enter` 或 `Down` 方向鍵可套用所選取的篩選條件。 根據選取的篩選條件和部署，系統會在 [地圖] 區段下醒目提示一或多個節點。
            - 若要在橫幅和地圖之間切換，請按 `Ctrl+F6`。
        
        **地圖上的鍵盤巡覽**
    
        - 選取橫幅上的任何篩選條件並按下 `Ctrl+F6` 後，焦點就會移至地圖檢視中的其中一個醒目提示節點 (**Azure 資料中心**或**國家/區域**)。
        - 若要巡覽至地圖中的其他醒目提示節點，您可以使用 `Tab` 或 `Right arrow` 鍵順向移動，使用 `Shift+Tab` 或 `Left arrow` 鍵反向移動。
        - 若要選取地圖中的任何醒目提示節點，請使用 `Enter` 或 `Down arrow` 鍵。
        - 一選取任何這類節點後，焦點就會移至節點的 [資訊工具方塊]。 根據預設，焦點會移至 [資訊工具方塊] 上已關閉的按鈕。 若要進一步地在 [方塊] 檢視內巡覽，請分別使用 `Right` 和 `Left arrow` 鍵來順向和反向移動。 按 `Enter` 的效果等同於在 [資訊工具方塊] 內選取焦點按鈕的效果。
        - 當焦點在 [資訊工具方塊] 時按 `Tab`，焦點會移至與選取之節點位於相同大陸的端點。 您可以使用 `Right` 和 `Left arrow` 鍵來巡覽這些端點。
        - 若要巡覽至其他流程端點/大陸的叢集，請使用 `Tab` 進行順向移動，使用 `Shift+Tab` 進行反向移動。
        - 一旦焦點位於 `Continent clusters` 後，請使用 `Enter` 或 `Down` 方向鍵來醒目提示大陸叢集內的端點。 若要巡覽端點和大陸叢集資訊方塊上的關閉按鈕，請分別使用 `Right` 或 `Left arrow` 鍵來進行順向和反向移動。 在任何端點上，您都可以使用 `Shift+L` 來切換到從選取節點到端點的連接線。 再次按下 `Shift+L` 可巡覽至所選的端點。
        
        在任何階段：
    
        - `ESC` 可將展開的選取範圍摺疊。
        - `UP Arrow` 鍵可執行與 `ESC` 相同的動作。 `Down arrow` 鍵可執行與 `Enter` 相同的動作。
        - 使用 `Shift+Plus` 可放大，使用 `Shift+Minus` 可縮小。
