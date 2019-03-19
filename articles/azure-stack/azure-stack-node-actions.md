---
title: Azure Stack 中的縮放單位節點動作 | Microsoft Docs
description: 了解如何在 Azure Stack 整合式系統上檢視節點狀態，以及使用開啟電源、關閉電源停用及繼續等節點動作。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 24c500251c8e91b7542c5c9d3e77676205c88c1f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090962"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure Stack 中的縮放單位節點動作

*適用於：Azure Stack 整合式系統*

本文說明如何檢視縮放單位的狀態。 您可以檢視單位的節點。 您可以執行節點動作，例如開啟電源、關閉電源、關機、清空、繼續及修復。 一般而言，您會在於現場進行組件更換時，或協助將節點復原時，使用這些節點動作。

> [!Important]  
> 本文所述的所有節點動作應該都一次以一個節點為目標。

## <a name="view-the-node-status"></a>檢視節點狀態

在系統管理員入口網站中，您可以檢視縮放單位及其相關節點的狀態。

若要檢視縮放單位的狀態：

1. 在 [區域管理] 圖格上，選取區域。
2. 在左側的 [基礎結構資源] 下，選取 [縮放單位]。
3. 在結果中，選取縮放單位。
4. 從左側的 [一般] 底下，選取 [節點]。

   檢視下列資訊：

   - 個別節點的清單
   - 操作狀態 (請參閱下方清單)
   - 電源狀態 (例如執行中或已停止)
   - 伺服器模型
   - 基礎板管理控制器 (BMC) 的 IP 位址
   - 核心總數
   - 記憶體量總計

![縮放單位的狀態](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>節點操作狀態

| 狀態 | 說明 |
|----------------------|-------------------------------------------------------------------|
| 執行中 | 節點正有效參與縮放單位。 |
| 已停止 | 節點無法使用。 |
| 新增中 | 節點正在新增至縮放單位。 |
| 修復中 | 正在修復節點。 |
| 維護  | 節點已暫停，且沒有作用中的使用者工作負載正在執行。 |
| 需要補救 | 偵測到需要修復節點的錯誤。 |

## <a name="scale-unit-node-actions"></a>縮放單位節點動作

當您檢視縮放單位節點的相關資訊時，您也可以執行節點動作，例如：
 - 啟動和停止 (視目前的電源狀態而定)
 - 停用和繼續 (視作業狀態而定)
 - 修復
 - Shutdown

節點的作業狀態會決定哪些選項可供使用。

您必須安裝 Azure Stack PowerShell 模組。 這些 Cmdlet 位於 **Azs.Fabric.Admin** 模組中。 若要安裝或確認「適用於 Azure Stack 的 PowerShell」安裝，請參閱[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

## <a name="stop"></a>Stop

**停止**動作會關閉節點電源。 此動作就像您按下電源按鈕一樣。 它不會傳送關機信號給作業系統。 針對計劃性停止作業，請一律先嘗試關機作業。 

當節點處於擱置狀態，而且不再回應要求，通常會使用此動作。

若要執行停止動作，請開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

在停止動作無法運作的罕見情況下，請重試作業，如果第二次也失敗，請改用 BMC Web 介面。

如需詳細資訊，請參閱[Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode)。

## <a name="start"></a>Start

**啟動**動作會開啟節點電源。 此動作就像您按下電源按鈕一樣。 
 
若要執行啟動動作，請開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

在啟動動作無法運作的罕見情況下，請重試作業，如果第二次也失敗，請改用 BMC Web 介面。

如需詳細資訊，請參閱[Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode)。

## <a name="drain"></a>清空

**清空**動作會將所有作用中工作負載移至該特定縮放單位中的其餘節點。

這個動作通常用於現場更換組件期間，例如，更換整個節點。

> [!Important]
> 請確定您是在計劃性維護時段且已通知使用者的情況下，在節點上使用清空作業。 在某些情況下，使用中的工作負載可能會導致中斷。

若要執行清空動作，請開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

如需詳細資訊，請參閱 [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode)。

## <a name="resume"></a>繼續

**繼續**動作會將已停用的節點繼續執行，並將其標示為可供放置工作負載。 先前已在節點上執行的工作負載不會容錯回復。 (如果您在節點上使用清空作業，請務必關閉電源)。 當您重新開啟節點的電源時，系統不會將它標示為可供放置工作負載。 準備就緒時，您必須使用繼續動作將節點標記為使用中。)

若要執行繼續動作，請開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

如需詳細資訊，請參閱 [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode)。

## <a name="repair"></a>修復

**修復**動作會修復節點。 只針對下列其中一個案例使用它：
 - 完整節點更換 (不論有無新資料磁碟)
 - 硬體元件失敗並取代之後 (如果現場可更換單元 (FRU) 文件中有建議)。

> [!Important]  
> 當您需要更換節點或個別硬體元件時，請參閱 OEM 硬體廠商的 FRU 文件，以了解確切的步驟。 FRU 文件會指定在更換硬體元件之後是否需要執行修復動作。 

執行修復動作時，您需要指定 BMC IP 位址。 

若要執行修復動作，請開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

  ```PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

**關機**動作會先將所有作用中工作負載移至該相同縮放單位中的其餘節點。 然後，此動作會以正常程序關閉縮放單位節點。

啟動已關閉的節點之後，您必須執行[繼續](#resume)動作。 先前已在節點上執行的工作負載不會容錯回復。

如果關機作業失敗，請嘗試使用[清空](#drain)作業，接著再使用關機作業。

若要執行關機動作，請開啟已提升權限的 PowerShell 提示字元，然後執行下列 Cmdlet：

  ```PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Stack 網狀架構系統管理員模組，請參閱 [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0)。
