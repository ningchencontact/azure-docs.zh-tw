---
title: 使用 Azure 移轉的伺服器移轉的 HYPER-V 移轉如何運作？ | Microsoft Docs
description: 提供 Azure 移轉的伺服器移轉的 HYPER-V 移轉的概觀
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811709"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-v VM 複寫如何運作？

本文提供的架構和您使用 Azure Migrate Server 移轉工具移轉 HYPER-V Vm 時所使用的程序的概觀。

[Azure Migrate](migrate-services-overview.md)提供一個中央中樞，來追蹤探索、 評估和移轉您的內部部署應用程式和工作負載和私密/公開金鑰雲端 Vm 至 Azure。 中樞會提供 Azure Migrate 的工具，來評估和移轉，以及第三方獨立軟體廠商 (ISV) 供應項目。

## <a name="agentless-migration"></a>無代理程式的移轉

Azure Migrate Server 移轉工具提供無代理程式的複寫在內部部署 HYPER-V Vm，使用適用於 HYPER-V 的移轉工作流程。 您只能在 HYPER-V 主機或叢集節點上安裝軟體代理程式。 不需要安裝在 HYPER-V Vm 上。

## <a name="server-migration-and-azure-site-recovery"></a>Server 移轉和 Azure Site Recovery

Azure 移轉的伺服器移轉是在內部部署工作負載移轉及雲端式 Vm 至 Azure 的工具。 Site Recovery 是嚴重損壞修復工具。 工具會共用一些常見的技術元件，用於資料複寫，但有不同的用途。 


## <a name="architectural-components"></a>架構元件

![架構](./media/hyper-v-replication-architecture/architecture.png)



**元件** | **部署** | 
--- | --- 
**複寫提供者** | Microsoft Azure Site Recovery 提供者是安裝在 HYPER-V 主機上，並向 Azure 移轉的伺服器移轉。<br/> 提供者會協調 HYPER-V Vm 複寫。
**復原服務代理程式** | Microsoft Azure 復原服務代理程式會處理資料複寫。 它適用於將資料從 HYPER-V Vm 複寫至 Azure 的提供者。<br/> 複寫的資料上傳至您的 Azure 訂用帳戶的儲存體帳戶。 Server 移轉工具的處理程序複寫的資料，並將其套用至訂用帳戶中的複本磁碟。 複本磁碟用來建立 Azure Vm，當您移轉。

- 元件會安裝單一安裝程式檔案，從 Azure 移轉的伺服器移轉下載入口網站中。
- 提供者和應用裝置使用輸出 HTTPS 連接埠 443 連線進行通訊與 Azure 移轉的伺服器移轉。
- 來自提供者和代理程式的通訊都是安全且加密。


## <a name="replication-process"></a>複寫程序

1. 當您啟用 HYPER-V VM 複寫時，就會開始初始複寫。
2. HYPER-V VM 快照時。
3. 在 VM 上的 Vhd 會是-逐一複寫，直到它們全部複製到 Azure。 初始複寫時間取決於 VM 大小和網路頻寬。
4. 使用 HYPER-V 複本，並儲存在記錄檔 （hrl 檔案），會追蹤在初始複寫期間發生的磁碟變更。
    - 記錄檔位於與磁碟相同的資料夾。
    - 每個磁碟有相關聯的 hrl 檔案傳送至次要的儲存體。
    - 當初始複寫正在進行時，快照和記錄檔會取用磁碟資源。
4. 初始複寫完成之後，刪除 VM 快照集，並會開始差異複寫。
5. Hrl 檔案中追蹤累加的磁碟上的變更。 復原服務代理程式複寫記錄檔定期上傳至 Azure 儲存體帳戶。


## <a name="performance-and-scaling"></a>效能及延展性

適用於 HYPER-V 的複寫效能會受到這些因素包括在 Azure 中的 VM 大小、 虛擬機器的資料變更率 （變換）、 記錄檔案儲存體的複寫資料上傳頻寬和目標儲存體在 HYPER-V 主機上的可用空間。

- 如果您複寫多部電腦，在此同時，使用[Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) hyper-v，以協助最佳化複寫。
- 規劃您的 HYPER-V 複寫和分散根據容量的 Azure 儲存體帳戶的複寫。

### <a name="control-upload-throughput"></a>控制項上傳輸送量

您可以限制用來將資料上傳至 Azure 的每部 HYPER-V 主機上的頻寬總量。 請小心。 如果您將有可能會影響它的值太低，複寫和延遲移轉。


1. 登入 HYPER-V 主機或叢集節點。
2. 執行**C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**，以開啟 Windows Azure 備份 MMC 嵌入式管理單元。
3. 在嵌入式管理單元中，選取 [變更內容]  。
4. 在 **節流**，選取**啟用網際網路頻寬使用節流來執行備份作業**。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影響上傳效率

如果您有備用的頻寬進行複寫，並想要增加上傳，您可以增加，如下所示的上傳工作中，已配置的執行緒數目：

1. 您可以開啟 登錄，Regedit。
2. 瀏覽至 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 金鑰
3. 增加用於複寫的每個 VM 的資料上傳的執行緒數目的值。 預設值為 4 和最大值為 32。 




## <a name="next-steps"></a>後續步驟

試試看[HYPER-V 移轉](tutorial-migrate-hyper-v.md)使用 Azure 移轉的伺服器移轉。
