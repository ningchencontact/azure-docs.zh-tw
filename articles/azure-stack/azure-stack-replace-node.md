---
title: 更換 Azure Stack 整合系統上的縮放單位節點 | Microsoft Docs
description: 了解如何更換 Azure Stack 整合系統上的實體縮放單位節點。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.openlocfilehash: 9d53aa879c39eb68597a402133a7ff16737f4f65
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716305"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>更換 Azure Stack 整合系統上的縮放單位節點

*適用於：Azure Stack 整合式系統*

本文說明更換 Azure Stack 整合式系統上實體電腦 (也稱為縮放單位節點) 的一般程序。 實際的縮放單位節點更換步驟將因原始設備製造商 (OEM) 硬體廠商而異。 如需您系統專屬的詳細步驟，請參閱廠商的現場可更換單元 (FRU) 文件。

下列流程圖顯示更換整個縮放單位節點的一般 FRU 程序。

![更換節點程序的流程圖](media/azure-stack-replace-node/replacenodeflow.png)

*根據硬體的實體條件，可能不需要此動作。

> [!Note]  
> 如果關機作業確實失敗，建議您使用清空作業，接著再使用停止作業。 如需更多詳細資料，請參閱可用的節點作業  

## <a name="review-alert-information"></a>檢閱警示資訊

如果縮放單位節點已關閉，您將會收到下列重大警示：

- 節點並未連接到網路控制站
- 無法存取節點以供虛擬機器放置
- 縮放單位節點已離線

![縮放單位已關閉的警示清單](media/azure-stack-replace-node/nodedownalerts.png)

如果您開啟「縮放單位節點已離線」警示，警示描述會包含無法存取的縮放單位節點。 您也可能會在於硬體生命週期主機上執行之 OEM 特定的監視解決方案中收到其他警示。

![節點離線警示的詳細資料](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>縮放單位節點更換程序

提供下列步驟以作為縮放單位節點更換程序的高階概觀。 如需您系統專屬的詳細步驟，請參閱 OEM 硬體廠商的 FRU 文件。 請勿在未參考您 OEM 提供之文件的情況下依照這些步驟執行。

1. 使用**關機**動作來正常關閉縮放單位節點。 根據硬體的實體條件，可能不需要此動作。 

2. 在關機動作失敗的罕見情況下，使用[清空](azure-stack-node-actions.md#drain)動作，讓縮放單位節點進入維護模式。 根據硬體的實體條件，可能不需要此動作。

   > [!NOTE]  
   > 在任何情況下，同一時間都只能將一個節點停用並關閉電源，才不會中斷 S2D (儲存空間直接存取)。

3. 在縮放單位節點處於維護模式之後，請使用[停止](azure-stack-node-actions.md#stop)動作。 根據硬體的實體條件，可能不需要此動作。

   > [!NOTE]  
   > 在關閉電源動作無法運作的罕見情況下，請改用基礎板管理控制器 (BMC) Web 介面。

4. 更換實體電腦。 一般而言，這由您的 OEM 硬體廠商來完成。
5. 使用[修復](azure-stack-node-actions.md#repair)動作，將實體電腦新增至縮放單位。
6. 使用具有特殊權限的端點來[檢查虛擬磁碟修復狀態](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)。 利用新的資料磁碟機，根據系統負載與已耗用的空間而定，完整的儲存體修復作業可能需要數小時的時間。
7. 當修復動作完成之後，驗證已自動關閉所有作用中警示。

## <a name="next-steps"></a>後續步驟

- 如需有關在已開啟系統電源的情況下更換實體磁碟的資訊，請參閱[更換磁碟](azure-stack-replace-disk.md)。 
- 如需有關更換需要關閉系統電源之硬體元件的資訊，請參閱[更換硬體元件](azure-stack-replace-component.md)。
