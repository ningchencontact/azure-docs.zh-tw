---
title: 監視 Azure FXT 邊緣篩選
description: 如何監視 Azure FXT 邊緣篩選混合式儲存體快取的硬體狀態
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827449"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>監視 Azure FXT 邊緣篩選硬體狀態

Azure FXT 邊緣篩選混合式儲存體快取系統有多個可協助系統管理員了解運作硬體底座內建的狀態燈號。

## <a name="system-health-status"></a>系統健康狀態

若要監視在較高層級的快取作業，使用 [軟體] 控制台中**儀表板**頁面上，如中所述[控制面板儀表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>硬體狀態 Led

本章節將說明各種內建於 Azure FXT 邊緣篩選硬體的狀態燈號。

### <a name="hard-drive-status-leds"></a>硬碟機狀態 Led

![圖片的硬碟機前端，水平的具有 2 的圖說文字標籤 （左上的角），1 （左下角） 和 3 （右側）](media/fxt-monitor/fxt-drive-callouts.png)

每個磁碟機載具有兩個的狀態 Led： 活動指示器 （1） 和狀態指示器 (2)。 

* 活動 LED (1) 號誌時磁碟機正在使用中。  
* 狀態 LED (2) 會指出磁碟機的條件，使用下表中的程式碼。

| 磁碟機狀態 LED 狀態              | 意義  |
|-------------------------------------|----------------------------------------------------------|
| 會閃爍綠色兩次每秒鐘      | 用來識別磁碟機*或* <br> 準備移除磁碟機  |
| 關閉 （光環）                         | 系統無法完成啟動*或* <br>磁碟機已準備好移除 |
| 會閃爍綠色、 琥珀色，並關閉       | 磁碟機故障預測   |
| 四次每秒鐘會閃爍琥珀色 | 失敗的磁碟機   |
| 持續的綠燈                         | 磁碟機在線上 |

磁碟機 (3) 的右邊會標示為的磁碟機容量及其他資訊。

磁碟機與磁碟機之間的空間會印有磁碟機編號。 在 Azure FXT Edge Filer 中，磁碟機 0 是左上方的磁碟機，磁碟機 1 則在磁碟機 0 的正下方。 編號會繼續在此模式中。 

![FXT 底座中某個硬碟機擴充插槽的相片，其中顯示了磁碟機編號和容量標籤](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左的控制台

左邊前端的控制台中有各種不同的狀態 LED 指示器 （1） 和反白顯示的大型系統健康情況指標 (2)。 

![左側的狀態 面板中，標記在左側，而 2 標記淺右邊的大型系統健康情況指示器狀態指標的 1](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制面板狀態指標 

左邊的狀態指示器顯示實心琥珀色燈光在該系統中是否發生錯誤。 下表描述錯誤的可能原因和解決方案。 

如果這些解決方案中，在嘗試之後仍有錯誤[連絡支援人員](fxt-support-ticket.md)取得協助。 

| 圖示 | 描述 | 錯誤狀況 | 可能的解決方案 |
|----------------|---------------|--------------------|----------------------|
| ![磁碟機圖示](media/fxt-monitor/fxt-hd-icon.jpg) | 磁碟機狀態 | 磁碟機錯誤 | 檢查系統事件記錄檔，來判斷磁碟機是否有錯誤，或 <br>執行適當的線上診斷測試;重新啟動系統並執行內嵌的診斷 (ePSA)，或 <br>如果磁碟機設定 RAID 陣列中，重新啟動系統，並輸入主機配接器設定公用程式 |
|![溫度圖示](media/fxt-monitor/fxt-temp-icon.jpg) | 溫度狀態 | 熱錯誤-例如，風扇故障或周圍溫度 je mimo rozsah | 請檢查下列可定址的條件： <br>冷卻風扇遺漏或已失敗 <br>系統的封面，空氣死亡記憶體模組空白，或後填滿括號會移除 <br>周圍溫度過長 <br>外部氣流阻礙 |
|![電力圖示](media/fxt-monitor/fxt-electric-icon.jpg) | 電源狀態 | 電力的錯誤-例如電壓超出範圍，無法 psu 已運轉或失敗的電壓 regulator |  檢查系統事件記錄檔或針對特定問題的系統訊息。 如果 psu 已運轉問題，請檢查 psu 已運轉狀態 LED，並如有需要請重新連接 psu 已運轉。 | 
|![記憶體圖示](media/fxt-monitor/fxt-memory-icon.jpg) | 記憶體狀態 | 記憶體錯誤 | 請檢查系統事件記錄檔或失敗的記憶體; 位置的系統訊息重新連接記憶體模組。 |
|![PCIe 圖示](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 狀態 | PCIe 卡錯誤 | 重新啟動系統;更新 PCIe 卡驅動程式;重新安裝智慧卡 |


### <a name="system-health-status-indicator"></a>系統健全狀況狀態指標

大型的亮燈的按鈕右邊的 [左] 控制台指出整體系統狀態，並也做為系統識別碼模式中的單元定位器燈號。

按下的系統健全狀況和系統識別碼模式和系統健康狀態模式之間切換 [ID] 按鈕。

|系統健全狀況狀態的狀態 | 條件 |
|-------------------------------------------|-----------------------------------------------|
| 藍色實線 | 正常的作業： 系統會開啟運作正常，且系統識別碼模式不是作用中。 <br/>如果您想要切換至 [系統識別碼模式，請按系統健康狀態和 ID] 按鈕。 |
| 閃爍藍色 | 系統識別碼模式才有作用。 如果您想要切換至 系統健康情況的模式，請按系統健康狀態和系統 ID 按鈕。 |
| Solid 琥珀色 | 系統會在保全的模式。 如果問題持續發生，請[請連絡 Microsoft 客戶服務及支援](fxt-support-ticket.md)。 |
| 閃爍琥珀色 | 系統錯誤。 檢查系統事件記錄檔中特定的錯誤訊息。 如需系統韌體和監視系統元件的代理程式所產生的事件和錯誤訊息資訊，請參閱 qrl.dell.com 的錯誤程式碼查詢頁面。 |


