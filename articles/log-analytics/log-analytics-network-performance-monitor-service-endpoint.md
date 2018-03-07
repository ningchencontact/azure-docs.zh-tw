---
title: "Azure Log Analytics 中的網路效能監視器方案 | Microsoft Docs"
description: "網路效能監控中的服務端點管理員功能可讓您對任何具有已開啟 TCP 通訊埠的端點進行網路連線監視。"
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>服務端點監視

[網路效能監控](log-analytics-network-performance-monitor.md)中的服務端點監視功能可讓您對任何具有已開啟 TCP 通訊埠的端點進行網路連線監視。 這類端點包括網站、SaaS 應用程式、PaaS 應用程式和 SQL 資料庫。 

您可以使用**服務端點監視**來執行下列功能： 

- 從多個分公司辦公室/位置監視您應用程式和網路服務的網路連線 (例如 Office 365、Dynamics CRM、內部企業營運應用程式、SQL 資料庫等) 
- 內建測試以監視 Office365 和 Dynamics365 端點的網路連線 
- 判斷連線至端點時的回應時間、網路延遲、封包遺失 
- 判斷應用程式效能低落是因為網路還是因為應用程式提供者端的某些問題所造成 
- 檢視拓撲地圖上每個躍點所提供的延遲，識別可能導致應用程式效能低落的作用點。 


![服務端點監視](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>組態 
若要開啟網路效能監控的組態，請開啟[網路效能監控解決方案](log-analytics-network-performance-monitor.md)，然後按一下 [設定] 按鈕。

![設定網路效能監控](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>設定 OMS 代理程式以進行監視。  
在用於監視的節點上啟用下列防火牆規則，讓解決方案可以探索從您的節點到服務端點的拓撲： 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>建立服務端點監視測試 

開始建立您的測試，來監視服務端點的網路連線 

1. 按一下 [服務端點監視] 索引標籤。
2. 按一下 [新增測試] 並輸入測試名稱和描述。 
3. 選取測試類型：<br>如果您要監視回應 HTTP/S 要求之服務的連線，例如 outlook.office365.com、bing.com，請選取 [Web 測試]。<br>如果您要監視回應 TCP 要求但不會回應 HTTP/S 要求之服務的連線，例如 SQL 伺服器、TCP 伺服器、SSH 連接埠等等，請選取 [網路測試]。 
4. 如果您不想執行網路測量 (網路延遲、封包遺失、拓樸搜索)，則取消選取文字方塊。 建議您隨時檢查，以充分發揮功能。 
5. 輸入您要監視網路連線的目標 URL/FQDN/IP 位址。  
6. 輸入目標服務的連接埠號碼。 
7. 輸入您需要測試執行的頻率。 
8. 選取您要監視服務網路連線的來源節點。 

    >[!NOTE]
    > 對於以 Windows Server 為基礎的節點，功能會使用以 TCP 為基礎的要求來執行網路測量。 對於以 Windows 用戶端為基礎的節點，功能會使用以 ICMP 為基礎的要求來執行網路測量。 在某些情況下，目標應用程式會封鎖以 ICMP 為基礎的連入要求，由於這個原因，當使用以 Windows 用戶端為基礎的節點時，解決方案就無法執行網路測量。 因此，建議您在此情況下使用以 Windows Server 為基礎的節點。 

9. 如果您不需要建立所選項目的健康情況事件，則清除 [在此測試所涵蓋的目標上啟用健康情況監視]。 
10. 選擇監視條件。 您可以輸入臨界值，以設定健康狀態事件產生的自訂臨界值。 只要條件的值高於針對所選網路/子網路配對選取的臨界值時，就會產生健康狀態事件。 
11. 按一下 [儲存] 儲存組態。 

 ![服務端點監視組態](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>逐步介紹 

移至 [網路效能監控] 儀表板檢視並觀察 [服務端點監視] 頁面，以從各種已建立的測試取得健康情況摘要。  

![[服務端點監視] 頁面](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

按一下圖格向下鑽研及檢視 [測試] 頁面上的測試詳細資料。 在 [LHS] 資料表中，您可以檢視所有測試的時間點健康情況和服務回應時間的值、網路延遲及封包遺失。 您可以使用網路狀態錄製器控制項，檢視過去另一個時間的網路快照集。 在資料表中按一下您要調查的測試。 您可以從 RHS 窗格中的圖表檢視遺失、延遲和回應時間值的歷史趨勢。 按一下 [測試詳細資料] 連結，即可檢視每個節點的效能。 

![服務端點監視測試](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

在 [測試節點] 檢視中，您可以觀察來自每個節點的網路連線。 按一下效能降低的節點。  這是能觀察到應用程式執行速度緩慢的節點。 

若要判斷應用程式效能低落是因為網路還是因為應用程式提供者端的某些問題所造成，可觀察應用程式回應時間與網路延遲之間的相互關聯 

**應用程式問題：**如果回應時間突然增加，但網路延遲是一致的，則這可能表示網路運作正常，而問題是因應用程式端的問題所導致的。  

![服務端點監視的應用程式問題](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**網路問題：**如果回應時間尖峰與對應的網路延遲尖峰同時出現，則這可能表示回應時間增加是因網路延遲的增加所導致的。  

![服務端點監視網路問題](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

一旦您判定問題是因為網路後，可以按一下 [拓撲] 檢視連結，識別拓撲地圖上的問題躍點。 例如，您可以在下圖中看到，節點與應用程式端點之間的 105 毫秒延遲總計之中，有 96 毫秒是因為躍點標示為紅色。 一旦您識別出問題躍點後，可以採取矯正措施。  

![服務端點監視測試](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>診斷 

如果您觀察到異常，請執行下列步驟：

如果服務回應時間、網路遺失和延遲顯示為 NA，可能是因為下列一個或多個原因：
- 應用程式已關閉。
- 用於檢查服務之網路連線的節點已關閉。
- 測試組態中輸入的目標不正確。
- 節點沒有任何網路連線。

如果顯示有效的服務回應時間，但網路遺失和延遲顯示為 NA，可能是因為下列一個或多個原因：
- 如果用於檢查服務之網路連線的節點為 Windows 用戶端機器，則目標服務會封鎖 ICMP 要求，或是網路防火牆會封鎖源自該節點的 ICMP 要求。
- 測試組態中的 [執行網路測量] 核取方塊已取消選取。 

如果服務回應時間為 NA，但是網路遺失和延遲都是有效的，表示目標服務並非 web 應用程式。 編輯測試組態，然後選擇網路測試而不是 Web 測試作為測試類型。 

如果應用程式執行緩慢，您應判斷應用程式效能低落是因為網路還是因為應用程式提供者端的某些問題所造成。


## <a name="next-steps"></a>後續步驟
* [搜尋記錄檔](log-analytics-log-searches.md)以檢視詳細的網路效能資料記錄。
