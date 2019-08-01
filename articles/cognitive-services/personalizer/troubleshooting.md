---
title: 疑難排解-個人化工具
titleSuffix: Azure Cognitive Services
description: 您可以在這篇文章中找到有關個人化工具的疑難排解問題。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663672"
---
# <a name="personalizer-troubleshooting"></a>個人化工具疑難排解

本文包含有關個人化工具常見問題的解答。

## <a name="learning-loop"></a>學習迴圈

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>學習迴圈似乎沒有學習。 如何修正此警示?

學習迴圈需要數千個報酬電話, 順位呼叫才會有效率地設定優先順序。 

如果您不確定學習迴圈目前的行為, 請執行[離線評估](concepts-offline-evaluation.md), 並套用已更正的學習原則。 

## <a name="next-steps"></a>後續步驟

[設定模型更新頻率](how-to-settings.md#model-update-frequency)