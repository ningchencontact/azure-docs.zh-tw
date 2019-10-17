---
title: 搭配 Azure 更新管理使用動態群組
description: 說明動態群組如何與更新管理搭配使用
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377742"
---
# <a name="use-dynamic-groups-with-update-management"></a>搭配更新管理使用動態群組

更新管理提供以 Azure 或非 Azure Vm 的動態群組為目標來進行更新部署的功能。 這些群組會在部署階段進行評估，因此您不需要編輯部署來新增機器。

## <a name="azure-machines"></a>Azure 機器

這些群組會由查詢定義，當更新部署開始時，就會評估該群組的成員。 動態群組無法與傳統 Vm 搭配使用。 定義查詢時，可以搭配使用下列專案來填入動態群組：

* Subscription
* 資源群組
* 位置
* Tags

![選取群組](./media/automation-update-management/select-groups.png)

若要預覽動態群組的結果，請按一下 [預覽] 按鈕。 此預覽會顯示該時間的群組成員資格，在此範例中，我們搜尋的是 **Role** 標記等於 **BackendServer** 的機器。 如果有更多機器新增此標記，系統就會將它們都新增至針對該群組進行的所有未來部署。

![預覽群組](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 機器

針對非 Azure 機器，已儲存的搜尋也稱為「電腦群組」，用來建立動態群組。 若要瞭解如何建立已儲存的搜尋，請參閱[建立電腦群組](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 建立群組之後，您可以從已儲存的搜尋清單中加以選取。 按一下 [**預覽**] 以在當時已儲存的搜尋中預覽電腦。

![選取群組](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>後續步驟

建立動態群組之後，您可以[建立更新部署](automation-tutorial-update-management.md)
