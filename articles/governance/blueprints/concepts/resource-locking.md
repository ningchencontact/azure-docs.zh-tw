---
title: 了解 Azure 藍圖中的資源鎖定
description: 了解在指派藍圖時保護資源的鎖定選項。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139437"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 藍圖中的資源鎖定

如果有一種機制可維護環境一致性，那麼大規模建立一貫化的環境才真正有用。 本文說明 Azure 藍圖中資源鎖定的運作方式。

## <a name="locking-modes-and-states"></a>鎖定模式和狀態

鎖定模式適用於藍圖指派，它只有兩個選項：[無] 或 [所有資源]。 鎖定模式是在藍圖指派期間設定的，一旦指派成功套用至訂用帳戶，就無法變更。

藍圖指派中成品所建立的資源有三種狀態：[未鎖定]、[唯讀] 或 [無法編輯 / 刪除]。 每個成品都可以是 [未鎖定] 狀態。 但是，非資源群組成品具有 [唯讀] 狀態，而資源群組具有 [無法編輯 / 刪除] 狀態。 這項差異是如何管理這些資源的重要區別。

[唯讀] 狀態完全依照定義：資源不能以任何方式更改 - 無法變更且無法刪除。 由於資源群組的「容器」性質，[無法編輯 / 刪除] 更難以分辨。 資源群組物件是唯讀的，但可以變更資源群組內的非鎖定資源。

## <a name="overriding-locking-states"></a>覆寫鎖定狀態

訂用帳戶中具有適當[角色型存取控制](../../../role-based-access-control/overview.md) (RBAC) 的人 (例如「擁有者」角色) 通常能夠變更或刪除任何資源。 當藍圖將鎖定作為已部署指派的一部分套用時，存取權並非如此。 如果使用 [鎖定] 選項設定了指派，則甚至訂用帳戶擁有者都不能變更內含的資源。

此安全性措施可保護已定義藍圖的一致性，以及設計為透過意外或以程式設計方式刪除或修改而建立的環境。

## <a name="removing-locking-states"></a>移除鎖定狀態

如果有必要刪除指派所建立的資源，則刪除它們的方法是先移除指派。 移除指派後，則會移除藍圖所建立的鎖定。 不過，資源會留下來，而需要透過正常方式刪除。

## <a name="how-blueprint-locks-work"></a>藍圖鎖定的運作方式

如果指派選取 [鎖定] 選項，則在指派藍圖期間會將RBAC 角色 `denyAssignments` 套用到成品資源。 該角色由藍圖指派的受控身分識別新增，並且只能透過相同的受控身分識別從成品資源中刪除。 此安全性措施可強制執行鎖定機制，並防止移除藍圖外部的藍圖鎖定。 只有藉由移除藍圖指派才能移除角色和鎖定，其只能由具有適當權限的人員執行。

> [!IMPORTANT]
> Azure Resource Manager 最多可快取 30 分鐘的角色指派詳細資料。 因此，藍圖資源上的 `denyAssignments` 可能不會立即完全生效。 在這段時間內，有可能會刪除藍圖鎖定所要保護的資源。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](lifecycle.md) (英文)
- 了解如何使用[靜態和動態參數](parameters.md) (英文)
- 了解如何自訂[藍圖排序順序](sequencing-order.md)
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題