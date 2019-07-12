---
title: 在 遠端桌面-Azure 診斷的圖形效能問題
description: 本文說明如何使用遠端桌面通訊協定工作階段中的 RemoteFX 圖形計數器來診斷效能問題與中 Windows 虛擬桌面的圖形。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: v-chjenk
ms.openlocfilehash: a139542bf9272336784ac96d667d65caa1ed96ff
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607339"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>診斷圖形效能問題，在 遠端桌面

當系統不會如預期般執行時，務必找出問題的來源。 這篇文章可協助您找出並修正遠端桌面通訊協定 (RDP) 工作階段期間的圖形與相關的效能瓶頸。

## <a name="find-your-remote-session-name"></a>尋找您的遠端工作階段名稱

您將需要您的遠端工作階段名稱來識別圖形效能計數器。 請遵循本節中的指示來識別您的 Windows 虛擬桌面預覽遠端工作階段名稱。

1. 從遠端工作階段中開啟 Windows 命令提示字元。
2. 執行**qwinsta**命令。
    - 如果您的工作階段裝載在多重工作階段虛擬機器 (VM):每個計數器名稱的尾碼是相同的尾碼，您的工作階段名稱，例如"rdp-tcp 37。 」
    - 如果您的工作階段裝載在支援虛擬的圖形處理單元 (vGPU) 的 VM:計數器會儲存在的伺服器，而不是在您的 VM。 計數器執行個體的 VM 名稱，而不是數字在中包含工作階段名稱，例如"Win8 Enterprise VM。 」

>[!NOTE]
> 雖然計數器會在其名稱中的 RemoteFX，它們會包含在 vGPU 情況下的遠端桌面的圖形。

## <a name="access-performance-counters"></a>存取效能計數器

RemoteFX 圖形中的效能計數器協助您藉由協助您追蹤之類的編碼時間的畫面格偵測瓶頸，並略過畫面格。

決定您的遠端工作階段名稱之後，遵循這些指示來為遠端工作階段中收集的 RemoteFX 圖形效能計數器。

1. 選取 **開始** > **系統管理工具** > **效能監視器**。
2. 中**Performance Monitor**對話方塊方塊中，展開**監視工具**，選取**效能監視器**，然後選取**新增**。
3. 在 **新增計數器** 對話方塊中，從**可用的計數器**清單中，展開 RemoteFX 圖形的 效能計數器物件。
4. 選取要監視的計數器。
5. 在 **選取執行個體的物件**清單中，選取要監視的所選計數器，然後選取特定的執行個體**新增**。 若要選取所有可用的計數器執行個體，請選取**所有執行個體**。
6. 選取 新增計數器後,**確定**。

選取的效能計數器會出現在 [效能監視器] 畫面。

>[!NOTE]
>每個使用中的工作階段主機上有自己的每個效能計數器執行個體。

## <a name="diagnosis"></a>診斷

圖形相關的效能問題通常分為四個類別：

- 低畫面播放速率
- 隨機拖延
- 高輸入的延遲
- 不佳的畫面格品質

著手解決低畫面播放速率、 隨機的延遲和高的輸入的延遲。 下一節會告訴您哪些效能計數器來測量每個類別目錄。

### <a name="performance-counters"></a>效能計數器

本節可協助您找出瓶頸。

第一次檢查輸出 Frames/Second 計數器。 它會測量開放給用戶端的框架數目。 如果此值小於輸入 Frames/Second 計數器，正在略過畫面格。 若要識別瓶頸，請使用略過/秒計數器的框架。

有三種類型的畫面格已略過/秒計數器：

- 框架已略過/秒 （沒有足夠的網路資源）
- 框架已略過/秒 （不足的用戶端資源）
- 框架已略過/秒 （伺服器資源不足）

較高的值，任何已略過/秒計數器表示問題出資源的畫面格會追蹤計數器。 比方說，如果用戶端無法解碼，並出現的畫面格伺服器 」 的相同費率提供框架，畫面格已略過/秒 （用戶端資源不足） 的計數器會比較高。

如果輸出 Frames/Second 計數器符合輸入 Frames/Second 計數器，您仍有不尋常的延隔時間尚未或停止，則問題可能是編碼的平均時間。 編碼是單一工作階段 (vGPU) 案例中的伺服器和多重工作階段的案例中的 VM 上，就會發生同步處理程序。 編碼的平均時間應該低於 33 毫秒。 如果編碼方式的平均時間都會低於 33 毫秒，但您仍會有效能問題，可能有問題的應用程式或您使用的作業系統。

如需有關如何診斷應用程式相關問題的詳細資訊，請參閱 <<c0> [ 的使用者輸入延遲，效能計數器](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters)。

RDP 支援 33 ms 平均編碼時間，因為它支援的輸入的畫面播放速率，最多 30 框架/秒。 請注意，33 ms 的最大支援的畫面播放速率。 在許多情況下，使用者經歷的畫面播放速率會比較低，取決於來源的 rdp 提供框架的頻率。 例如，工作，例如觀賞影片需要完整的輸入畫面播放速率為 30 框架/秒，但較少的耗用大量資源的工作，例如不常編輯的 word 文件不需要這類高比率的每秒的輸入畫面的良好的使用者經驗。

您可以使用畫面格品質計數器來診斷框架品質問題。 此計數器表示輸出畫面格品質的來源畫面格品質的百分比。 品質遺失可能是因為 RemoteFX，或可能會繼承圖形的來源。 如果 RemoteFX 造成品質遺失，則問題可能是將較高精確度的內容傳送的網路或伺服器資源不足。

## <a name="mitigation"></a>緩和

如果伺服器資源會造成瓶頸的原因，請嘗試下列動作，才能改善效能的其中一個：

- 減少每個主機的工作階段數目。
- 增加的記憶體，並計算伺服器上的資源。
- 卸除連接的解析。

如果網路資源而造成瓶頸，請嘗試下列動作，才能改善網路可用性，每個工作階段的其中一個：

- 減少每個主機的工作階段數目。
- 卸除連接的解析。
- 使用較高頻寬網路。

如果用戶端資源而造成瓶頸，執行下列一或兩個以改善效能的下列事項：

- 安裝最新的遠端桌面用戶端。
- 增加的記憶體，並計算用戶端電腦上的資源。

> [!NOTE]
> 我們目前不支援來源 Frames/Second 計數器。 現在，來源 Frames/Second 計數器一定會設定為 0。

## <a name="next-steps"></a>後續步驟

- 若要建立 Azure 虛擬機器 GPU 最佳化，請參閱[設定的圖形處理器 (gpu) 加速 Windows 虛擬桌面預覽環境](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu)。
- 如需疑難排解和提報的追蹤記錄的概觀，請參閱 <<c0> [ 疑難排解概觀、 意見反應和支援](https://docs.microsoft.com/azure/virtual-desktop/troubleshoot-set-up-overview)。
- 若要深入了解預覽服務，請參閱[Windows Desktop 預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
