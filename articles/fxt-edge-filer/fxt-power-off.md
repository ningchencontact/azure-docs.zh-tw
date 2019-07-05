---
title: 如何關閉 Microsoft Azure FXT 邊緣篩選單元
description: 啟動和安全關閉 Azure FXT 邊緣篩選節點的程序
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 8d779547040da9855409d3408827af2e4acd17a7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542866"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地關閉 Azure FXT 邊緣篩選硬體電源

雖然您可以使用的實體電源按鈕來切換在個別節點上，您不應該使用它來關閉在正常情況下的單位。

Azure FXT 邊緣篩選節點是用來做為叢集的一部分之後，您應該使用叢集的控制面板軟體關閉硬體。 

> [!NOTE] 
> 若要避免可能發生資料遺失或損毀，請一律使用控制台中的軟體來關閉 Azure FXT 邊緣篩選。 請勿用於實體電源按鈕關閉除非 Microsoft 客戶服務及支援指示要執行這項操作。
> 
> 在發生電力的緊急狀況，中斷連接電源線，或使用您的資料中心的電力中斷連線機制。

## <a name="shut-down-a-node-from-the-control-panel"></a>關閉節點從 控制台

請遵循下列指示來安全地關閉 Azure FXT 邊緣篩選節點的電源：

1. 登入叢集的 [控制台]。 (在中的指示[開啟 [設定] 頁面](fxt-cluster-create.md#open-the-settings-pages))
1. 按一下 **設定**索引標籤，然後載入**叢集** > **FXT 節點**頁面。
1. 叢集節點的清單中，找出您的想来關閉。 按一下 **關閉電源**按鈕及其**動作**資料行。 
1. 請稍候數分鐘。 節點將會關閉，然後關閉電源。

## <a name="next-steps"></a>後續步驟

* 了解的狀態 Led 和中的其他指標[監視 Azure FXT 邊緣篩選硬體狀態](fxt-monitor.md)。
* 在中所提供的深入了解 Azure FXT 邊緣篩選 power[連接電源纜線](fxt-network-power.md#connect-power-cables)。
