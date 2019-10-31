---
title: Azure 時間序列深入解析預覽版中的資料模型 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽版中的資料模型。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 81c2c2af78f5f066e1b27e14fa774df04d7c5868
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063989"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽版中的資料模型

本文說明如何使用 Azure 時間序列深入解析 Preview 中的時間序列模型。 它詳細說明數個常見資料案例。

若要深入了解如何使用該更新，請參閱 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。

## <a name="types"></a>類型

### <a name="create-a-single-type"></a>建立單一類型

1. 移至時間序列模型選取器面板，然後從功能表中選取 [**類型**]。 折迭面板以將焦點放在時間序列模型類型上。

    [![「類型」窗格](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. 選取 [+ 新增]。
1. 輸入與類型相關的所有詳細資料，然後選取 [**建立**]。 此動作會在環境中建立類型。

    [新增類型的 ![選取專案](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>大量上傳一或多個類型

1. 選取 [上傳 JSON]。
1. 選取包含類型承載的檔案。
1. 選取 [上傳]。

    [![選取一或多個類型的大量上傳](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>編輯單一類型

1. 選取類型，然後選取 [編輯]。 
1. 進行必要的變更，然後選取 [儲存]。

    [編輯類型的 ![選取專案](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>刪除類型

1. 選取類型，然後選取 [刪除]。
1. 如果沒有執行個體與該類型相關聯，系統就會刪除它。

    [![[刪除] 按鈕](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>階層

### <a name="create-a-single-hierarchy"></a>建立單一階層

1. 移至時間序列模型選取器面板，**然後從功能表**中選取 [階層]。 折迭面板以將焦點放在時間序列模型階層。

    [![[階層] 窗格](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. 選取 [+ 新增]。

    [![[新增] 按鈕](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. 在右窗格中選取 [ **+ 新增層級**]。

    [![[新增層級] 按鈕](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. 請輸入階層的詳細資訊，並選取 [建立]。

    [![階層詳細資料和 [建立] 按鈕](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>大量上傳一或多個階層

1. 選取 [上傳 JSON]。
1. 選取包含階層承載的檔案。
1. 選取 [上傳]。

    [大量上傳階層的 ![選項](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>編輯單一階層

1. 選取階層，然後選取 [編輯]。
1. 進行必要的變更，然後選取 [儲存]。

    [編輯單一階層的 ![選取專案](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>刪除階層

1. 選取階層，然後選取 [**刪除**]。 
1. 如果沒有執行個體與該階層相關聯，系統就會刪除它。

    [![[刪除] 按鈕](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>執行個體

### <a name="create-a-single-instance"></a>建立單一執行個體

1. 移至時間序列模型選取器面板，然後從功能表中選取 [**實例**]。 折迭面板以將焦點放在時間序列模型實例上。

    [![「實例」窗格](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. 選取 [新增]。

    [新增實例的 ![選取專案](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. 輸入執行個體詳細資料，選取類型和階層關聯，然後選取 [建立]。

### <a name="bulk-upload-one-or-more-instances"></a>大量上傳一或多個執行個體

1. 選取 [上傳 JSON]。
1. 選取包含執行個體承載的檔案。

    [針對一或多個實例的大量上傳 ![選取專案](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. 選取 [上傳]。

### <a name="edit-a-single-instance"></a>編輯單一執行個體

1. 選取執行個體，然後選取 [編輯]。 
1. 進行必要的變更，然後選取 [儲存]。

    [編輯單一實例 ![選取專案](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>後續步驟

- 如需時間序列模型的詳細資訊，請參閱[資料模型](./time-series-insights-update-tsm.md)化。

- 若要深入了解預覽版，請參閱[在 Azure 時間序列深入解析預覽版總管中將資料視覺化](./time-series-insights-update-explorer.md)。

- 若要瞭解支援的 JSON 圖形，請閱讀[支援的 json 圖形](./time-series-insights-send-events.md#supported-json-shapes)。
