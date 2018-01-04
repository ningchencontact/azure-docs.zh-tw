---
title: "在 Azure Site Recovery 的 azure 至 Azure 複寫架構 |Microsoft 文件"
description: "本文概要說明使用 Azure Site Recovery 服務複寫 Azure 區域之間的 Azure VM 時所用的元件和架構。"
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: b37af3462a58f4418653d0e1b2300b5805e0a864
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Azure 至 Azure 複寫架構


這篇文章說明架構時複寫、 容錯移轉和復原 Azure 虛擬機器 (Vm) 使用的 Azure 地區之間使用[Azure Site Recovery](site-recovery-overview.md)服務。

>[!NOTE]
>Site Recovery 服務的 Azure VM 複寫目前為預覽狀態。



## <a name="architectural-components"></a>架構元件

下圖提供特定區域 (在此範例中為美國東部位置) 之中 Azure VM 環境的高層級檢視。 在 Azure VM 的環境中：
- 應用程式可以在 VM 上執行，且磁碟分散於不同的儲存體帳戶。
- VM 可以包含在虛擬網路內的一個或多個子網路。


**Azure 至 Azure 複寫**

![客戶環境](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>複寫程序

### <a name="step-1"></a>步驟 1

當您啟用 Azure VM 複寫時，下列資源會自動建立在目標區域中，根據的來源地區設定。 您可以視需要自訂目標來源設定。

![啟用複寫程序，步驟 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Resource** | **詳細資料**
--- | ---
**目標資源群組** | 在容錯移轉之後複寫的 VM 所屬的資源群組。
**目標虛擬網路** | 在容錯移轉之後複寫的 VM 所在的虛擬網路。 在來源和目標的虛擬網路之間會建立網路對應，反之亦然。
**快取儲存體帳戶** | 來源 VM 變更複寫到目標儲存體帳戶之前，它們會追蹤並傳送至來源位置的快取儲存體帳戶。 這個步驟可確保在 VM 上執行的實際執行應用程式上的影響降到最低。
**目標儲存體帳戶**  | 將資料複寫至其中的目標位置儲存體帳戶。
**目標可用性設定組**  | 在容錯移轉之後複寫的 VM 所在的可用性設定組。

### <a name="step-2"></a>步驟 2

啟用複寫時，系統會在 VM 上自動安裝 Site Recovery 擴充行動服務：

1. 向 Site Recovery 註冊 VM。

2. 對於 VM 設定連續複寫。 VM 磁碟上的資料寫入會持續傳送到來源位置的快取儲存體帳戶中。

   ![啟用複寫程序，步驟 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery 永遠不會需要 VM 的輸入連線能力。 僅輸出連線所需的下列。

 - 站台復原服務 Url/IP 位址
 - Office 365 驗證 Url/IP 位址
 - 快取儲存體帳戶的 IP 位址

如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 請確認有沒有封鎖透過通訊埠 20004 Vm 之間的內部通訊的防火牆應用裝置。

> [!IMPORTANT]
如果您想要在屬於複寫群組的 Linux Vm，請確定輸出流量在連接埠 20004 手動開啟根據特定的 Linux 版本的指引。

### <a name="step-3"></a>步驟 3

連續複寫進行之後，磁碟寫入會立即傳送至快取儲存體帳戶。 Site Recovery 會處理資料，並將資料傳送到目標儲存體帳戶。 處理資料之後，目標儲存體帳戶會每隔幾分鐘產生復原點。

## <a name="failover-process"></a>容錯移轉程序

您起始容錯移轉時，系統會在目標資源群組、目標虛擬網路，目標子網路和目標可用性設定組中建立 VM。 在容錯移轉時，您可以使用任何復原點。

![容錯移轉程序](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>後續步驟

[快速複寫](azure-to-azure-quickstart.md)Azure VM 到次要區域。
