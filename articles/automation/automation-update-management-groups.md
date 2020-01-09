---
title: 搭配 Azure 更新管理使用動態群組
description: 本文說明動態群組如何與 Azure 自動化更新管理搭配使用。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75420404"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

更新管理提供以 Azure 或非 Azure Vm 的動態群組為目標來進行更新部署的功能。 這些群組會在部署階段進行評估，因此您不需要編輯部署來新增機器。

## <a name="azure-machines"></a>Azure 機器

這些群組會由查詢定義，當更新部署開始時，就會評估該群組的成員。 動態群組無法與傳統 Vm 搭配使用。 定義查詢時，可以搭配使用下列專案來填入動態群組：

* 訂閱
* 資源群組
* 位置
* 標籤

![選取群組](./media/automation-update-management/select-groups.png)

若要預覽動態群組的結果，請按一下 [預覽] 按鈕。 此預覽會顯示該時間的群組成員資格，在此範例中，我們搜尋的是 **Role** 標記等於 **BackendServer** 的機器。 如果有更多機器新增此標記，系統就會將它們都新增至針對該群組進行的所有未來部署。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 機器

針對非 Azure 機器，已儲存的搜尋（也稱為電腦群組）是用來建立動態群組。 若要瞭解如何建立已儲存的搜尋，請參閱[建立電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 建立群組之後，您可以從已儲存的搜尋清單中加以選取。 按一下 [**預覽**] 以在當時已儲存的搜尋中預覽電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

建立動態群組之後，您可以[建立更新部署](automation-tutorial-update-management.md)
