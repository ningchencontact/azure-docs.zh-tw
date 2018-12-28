---
title: Azure 時間序列深入解析資料模型 - Azure 時間序列深入解析預覽版中的資料模型 | Microsoft Docs
description: 了解 Azure 時間序列深入解析預覽版中的資料模型。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: e68bc01d2c0781333454fa753992d0136fac0c06
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269085"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Azure 時間序列深入解析預覽版中的資料模型

本文件描述如何透過 Azure 時間序列深入解析預覽版使用時間序列模型。 它詳細說明數個常見資料案例。

若要深入了解如何使用該更新，請參閱 [Azure 時間序列深入解析預覽版總管](./time-series-insights-update-explorer.md)。

## <a name="types"></a>類型

### <a name="create-a-single-type"></a>建立單一類型

1. 移至時間序列模型選取器面板，然後從功能表選取 [Types] \(類型\)。 摺疊面板以將焦點放在時間序列模型類型。

    ![入口網站_一][1]

1. 選取 [新增] 。
1. 輸入與類型有關的所有詳細資料，並選取 [建立]。 此動作會在環境中建立類型。

    ![入口網站_二][2]

### <a name="bulk-upload-one-or-more-types"></a>大量上傳一或多個類型

1. 選取 [上傳 JSON]。
1. 選取包含類型承載的檔案。
1. 選取 [上傳] 。

    ![入口網站_三][3]

### <a name="edit-a-single-type"></a>編輯單一類型

選取類型，然後選取 [編輯]。 進行必要的變更，然後選取 [儲存]。

![入口網站_四][4]

### <a name="delete-a-type"></a>刪除類型

選取類型，然後選取 [刪除]。 如果沒有執行個體與該類型相關聯，系統就會刪除它。

![入口網站_五][5]

## <a name="hierarchies"></a>階層

### <a name="create-a-single-hierarchy"></a>建立單一階層

1. 移至時間序列模型選取器面板，然後從功能表選取 [Hierarchies] \(階層\)。 摺疊面板以將焦點放在時間序列模型階層。

    ![入口網站_六][6]

1. 選取 [新增] 。

    ![入口網站_七][7]

1. 在右窗格中選取 [新增層級]。

    ![入口網站_八][8]

1. 請輸入階層的詳細資訊，並選取 [建立]。

    ![入口網站_九][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>大量上傳一或多個階層

1. 選取 [上傳 JSON]。
1. 選取包含階層承載的檔案。
1. 選取 [上傳] 。

    ![入口網站_十][10]

### <a name="edit-a-single-hierarchy"></a>編輯單一階層

選取階層，然後選取 [編輯]。 進行必要的變更，然後選取 [儲存]。

![入口網站_十一][11]

### <a name="delete-a-hierarchy"></a>刪除階層

選取階層，然後選取 [刪除]。 如果沒有執行個體與該階層相關聯，系統就會刪除它。

![入口網站_十二][12]

## <a name="instances"></a>執行個體

### <a name="create-a-single-instance"></a>建立單一執行個體

1. 移至時間序列模型選取器面板，然後從功能表選取 [Instances] \(執行個體\)。 摺疊面板以將焦點放在時間序列模型執行個體。

    ![入口網站_十三][13]

1. 選取 [新增] 。

    ![入口網站_十四][14]

1. 輸入執行個體詳細資料，選取類型和階層關聯，然後選取 [建立]。

### <a name="bulk-upload-one-or-more-instances"></a>大量上傳一或多個執行個體

1. 選取 [上傳 JSON]。
1. 選取包含執行個體承載的檔案。

    ![入口網站_十五][15]

1. 選取 [上傳] 。

### <a name="edit-a-single-instance"></a>編輯單一執行個體

選取執行個體，然後選取 [編輯]。 進行必要的變更，然後選取 [儲存]。

![入口網站_十六][16]

### <a name="delete-an-instance"></a>刪除執行個體

選取執行個體，然後選取 [刪除]。 如果沒有事件與該執行個體相關聯，系統就會刪除它。

## <a name="next-steps"></a>後續步驟

- 如需有關時間序列模型的詳細資訊，請參閱[資料模型](./time-series-insights-update-tsm.md)。
- 若要深入了解預覽版，請參閱[在 Azure 時間序列深入解析預覽版總管中將資料視覺化](./time-series-insights-update-explorer.md)。
- 若要了解支援的 JSON 塑形，請參閱[支援的 JSON 塑形](./time-series-insights-send-events.md#json)。

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png