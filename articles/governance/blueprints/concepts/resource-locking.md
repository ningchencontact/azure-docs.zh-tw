---
title: 了解資源鎖定
description: 了解在指派藍圖時保護資源的鎖定選項。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2e281896d45ada8010f24a1f18265a8cdd523d31
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696978"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 藍圖中的資源鎖定

如果有一種機制可維護環境一致性，那麼大規模建立一貫化的環境才真正有用。 本文說明 Azure 藍圖中資源鎖定的運作方式。

## <a name="locking-modes-and-states"></a>鎖定模式和狀態

鎖定模式適用於藍圖指派，它提供三個選項：**不要鎖定**、**唯讀**或**不要刪除**。 鎖定模式會在藍圖指派期間的成品部署過程中進行設定。 您可以藉由更新藍圖指派來設定不同的鎖定模式。
不過，鎖定模式無法變更藍圖外部。

藍圖指派中成品所建立的資源有四種狀態：**未鎖定**、**唯讀**、**無法編輯/刪除**或**無法刪除**。 每個成品類型都可以處於**未鎖定**狀態。 下表可用來判斷資源的狀態：

|Mode|成品資源類型|State|說明|
|-|-|-|-|
|不要鎖定|*|未鎖定|資源並未受到藍圖保護。 此狀態也可用於從藍圖指派外部新增至**唯讀**或**不要刪除**資源群組成品的資源。|
|唯讀|資源群組|無法編輯/刪除|此資源群組是唯讀的，而且無法修改資源群組上的標記。 **未鎖定**資源可以在這個資源群組中新增、移動、變更或刪除。|
|唯讀|非資源群組|唯讀|資源不能以任何方式更改：無法變更且無法刪除。|
|不要刪除|*|無法刪除|資源可以改變，但無法刪除。 **未鎖定**資源可以在這個資源群組中新增、移動、變更或刪除。|

## <a name="overriding-locking-states"></a>覆寫鎖定狀態

訂用帳戶中具有適當[角色型存取控制](../../../role-based-access-control/overview.md) (RBAC) 的人 (例如「擁有者」角色) 通常能夠變更或刪除任何資源。 當藍圖將鎖定作為已部署指派的一部分套用時，存取權並非如此。 如果指派是使用 [唯讀] 或 [不要刪除] 選項來設定的，則即使是訂用帳戶擁有者也無法在受保護的資源上執行封鎖的動作。

此安全性措施可保護已定義藍圖的一致性，以及設計為透過意外或以程式設計方式刪除或修改而建立的環境。

## <a name="removing-locking-states"></a>移除鎖定狀態

如果您需要修改或刪除指派所保護的資源，則可執行此動作的方式有兩種。

- 將藍圖指派更新為**未鎖定**的鎖定模式
- 刪除藍圖指派

移除指派後，則會移除藍圖所建立的鎖定。 不過，資源會留下來，而需要透過正常方式刪除。

## <a name="how-blueprint-locks-work"></a>藍圖鎖定的運作方式

如果指派選取了 [唯讀] 或 [不要刪除] 選項，則在藍圖指派期間，會將 RBAC [拒絕指派](../../../role-based-access-control/deny-assignments.md)的拒絕動作套用到成品資源。 拒絕動作會由藍圖指派的受控身分識別來新增，並且只能透過相同的受控身分識別從成品資源中移除。 此安全性措施可強制執行鎖定機制，並防止移除藍圖外部的藍圖鎖定。

> [!IMPORTANT]
> Azure Resource Manager 最多可快取 30 分鐘的角色指派詳細資料。 因此，藍圖資源上拒絕指派的拒絕動作可能不會立即完全生效。 在這段時間內，有可能會刪除藍圖鎖定所要保護的資源。

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](lifecycle.md) (英文)
- 了解如何使用[靜態和動態參數](parameters.md) (英文)
- 了解如何自訂[藍圖排序順序](sequencing-order.md)
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題