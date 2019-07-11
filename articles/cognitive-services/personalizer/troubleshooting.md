---
title: 疑難排解-Personalizer
titleSuffix: Azure Cognitive Services
description: 這篇文章中可以找到疑難排解 Personalizer 疑問。
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722260"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 疑難排解

這篇文章包含 Personalizer 相關的常見問題集疑難排解問題的解答。

## <a name="learning-loop"></a>學習迴圈

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>若要了解似乎不是學習迴圈。 如何修正此？

學習迴圈需要少數的數千個 Reward 呼叫陣序的呼叫有效地排列優先順序。 

如果您不確定您的 learning 迴圈目前行為的方式，請執行[離線評估](concepts-offline-evaluation.md)，並將修正過的學習原則套用。 

## <a name="next-steps"></a>後續步驟

[設定模型的更新頻率](how-to-settings.md#model-update-frequency)