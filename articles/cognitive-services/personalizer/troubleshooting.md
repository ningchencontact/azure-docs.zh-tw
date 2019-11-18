---
title: 疑難排解-個人化工具
titleSuffix: Azure Cognitive Services
description: 本文包含有關個人化工具常見問題的解答。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953172"
---
# <a name="personalizer-troubleshooting"></a>個人化工具疑難排解

本文包含有關個人化工具常見問題的解答。

## <a name="learning-loop"></a>學習迴圈

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>學習迴圈似乎沒有學習。 如何? 修正此問題嗎？

學習迴圈需要數千個報酬電話，順位呼叫才會有效率地設定優先順序。 

如果您不確定學習迴圈目前的行為，請執行[離線評估](concepts-offline-evaluation.md)，並套用已更正的學習原則。 

## <a name="next-steps"></a>後續步驟

[設定模型更新頻率](how-to-settings.md#model-update-frequency)