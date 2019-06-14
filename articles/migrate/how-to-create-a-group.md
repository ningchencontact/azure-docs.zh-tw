---
title: 使用 Azure Migrate 分組機器以進行評量 | Microsoft Docs
description: 說明如何在使用 Azure Migrate 服務進行評量之前先分組機器。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544906"
---
# <a name="group-machines-for-assessment"></a>分組機器以進行評量

本文說明如何使用 [Azure Migrate](migrate-overview.md) 為評量建立機器群組。 Azure Migrate 會評估群組中的機器，檢查機器是否適合移轉至 Azure，並提供在 Azure 中執行機器的大小調整建議和成本估計。 如果您知道需要一起移轉的機器，您可以在 Azure Migrate 中，使用下列方法手動建立群組。 如果您不是很清楚需要分組在一起的機器，則可以使用 Azure Migrate 中的相依性視覺效果功能來建立群組。 [深入了解。](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> 在 Azure Government 中無法使用相依性視覺效果功能。

## <a name="create-a-group"></a>建立群組

1. 在 Azure Migrate 專案的 **概觀** 中，在 [管理] 下按一下  **群組** > **+ 群組** ，並指定群組名稱。
2. 將一台或多台機器新增至群組，並按一下  **[建立]** 。
3. 您可以選擇性地選取此項目，為群組執行新的評量。

    ![建立群組](./media/how-to-create-a-group/create-group.png)

建立群組之後，可以在 [群組]  頁面上選取群組，然後新增或移除機器來加以修改。

## <a name="next-steps"></a>後續步驟

- 了解如何使用[機器相依性對應](how-to-create-group-machine-dependencies.md)建立高度信賴群組。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
