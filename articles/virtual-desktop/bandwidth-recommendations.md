---
title: 遠端會話的頻寬建議-Azure
description: 遠端會話的可用頻寬建議。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: helohr
ms.openlocfilehash: 1a88cfb2fcd5f0b607e7a2b43ae833789a584e34
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695311"
---
# <a name="bandwidth-recommendations-for-remote-sessions"></a>遠端會話的頻寬建議

使用遠端 Windows 會話時，您網路的可用頻寬會大幅影響您的體驗品質。 不同的應用程式和顯示解析度需要不同的網路設定，因此請務必確定您的網路已設定為符合您的需求。 本文說明每個工作負載的建議頻寬。

>[!NOTE]
>下列建議適用于低於 0.1% 遺失的網路。

## <a name="applications"></a>應用程式

下表列出順暢使用者體驗的最低需求。 

|工作負載        |範例應用程式                                                                                           |建議的頻寬|
|----------------|--------------------------------------------------------------------------------------------------------------|---------------------|
|任務型工作者     |Microsoft Word、Outlook、Excel、Adobe Reader                                                                  |1.5 @ no__t-0Mbps             |
|Office 背景工作   |Microsoft Word、Outlook、Excel、Adobe Reader、PowerPoint、相片 Viewer                                        |3 @ no__t-0Mbps               |
|知識工作者|Microsoft Word，Outlook，Excel，Adobe Reader，PowerPoint，相片檢視器，JAVA                                  |5 @ no__t-0Mbps               |
|Power worker    |Microsoft Word，Outlook，Excel，Adobe Reader，PowerPoint，相片檢視器，JAVA，CAD/CAM，插圖/發行|15 @ no__t-0Mbps              |

請記住，放在網路上的壓力取決於您的應用程式工作負載的輸出畫面播放速率和顯示解析度。 如果畫面播放速率或顯示器解析度增加，頻寬需求也會上升。 例如，具有高解析度顯示的輕量工作負載需要比一般或低解析度的輕量工作負載更多的可用頻寬。

其他案例可能會根據您的使用方式變更其頻寬需求，例如：

- 語音或視訊會議
- 即時通訊
- 串流4K 影片

請務必使用登入 .VSI 之類的模擬工具，在您的部署中負載測試這些案例。 會改變負載大小、執行壓力測試，並在遠端會話中測試常見的使用者案例，以進一步瞭解您的網路需求。 

## <a name="display-resolutions"></a>顯示解析度

不同的顯示解析度需要不同的可用頻寬。 下表列出我們建議的頻寬，這是在一般顯示器解析度上，以每秒30個畫面的畫面播放速率（fps）提供的流暢使用者體驗。 請記住，涉及 30 fps （例如讀取靜態文字）的畫面播放速率的案例，需要較少的可用頻寬。 

|一般顯示器解析度為 30 fps    |建議的頻寬|
|-----------------------------------------|---------------------|
|大約1024× 768 px                      |1.5 Mbps             |
|大約1280× 720 px                      |3 Mbps               |
|大約1920× 1080 px                     |5 Mbps               |
|約3840× 2160 px （4K）                |15 Mbps              |
