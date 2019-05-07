---
title: 配置設定
titleSuffix: Azure Cognitive Services
description: 服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 0cd08e1191c68c57975d3e68648134925155e7f2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026686"
---
# <a name="personalizer-settings"></a>個人化工具設定

服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。

請針對每個意見反應迴圈建立一個個人化工具資源。 

## <a name="configure-service-settings-in-the-azure-portal"></a>在 Azure 入口網站中設定服務設定

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 尋找個人化工具資源。 
1. 在 [資源管理] 區段中，選取 [設定]。

    ![登入 Azure 入口網站。 尋找個人化工具資源。 在 [資源管理] 區段中，選取 [設定]。](media/settings/how-to-configure.png)

### <a name="reward-settings-for-the-feedback-loop"></a>意見反應迴圈的報酬設定

就服務對於意見反應迴圈如何使用報酬的設定進行配置。 變更下列設定將會重設目前的個人化工具模型，並會以過去 2 天的資料將模型重新定型：

![設定意見反應迴圈的報酬設定](media/settings/configure-model-reward-settings.png)

|設定|目的|
|--|--|
|報酬等候時間|設定要讓個人化工具為排名呼叫收集報酬值的時間長度，這段時間從該排名呼叫發生的當下起算。 系統會詢問下列問題來設定此值：「個人化工具應該等候報酬呼叫多久？」 在此時段過後才抵達的報酬會記錄下來，但不會用於學習。|
|預設報酬|如果個人化工具未在「報酬等候時間」的時段內收到任何報酬呼叫，便會指派預設報酬。 預設報酬會預設為零，且大部分情況下皆是如此。|
|報酬彙總|如果從相同的排名 API 呼叫收到多個報酬，便會使用下列其中一個彙總方法：**總和**或**最早**。 「最早」會挑選最早收到的分數，並捨棄其餘分數。 如果您想要從可能重複的呼叫中挑出唯一的報仇，這會非常有用。 |

在變更這些設定之後，請務必選取 [儲存]。

### <a name="exploration-setting"></a>探索設定 

個人化能夠探索新的模式，並藉由探索替代方案來適應隨著時間變化的使用者行為。 **探索**設定會決定要在進行探索時回應多少百分比的排名呼叫。 

變更此設定將會重設目前的個人化工具模型，並會以過去 2 天的資料將模型重新定型。

![探索設定會決定要在進行探索時回應多少百分比的排名呼叫](media/settings/configure-exploration-setting.png)

在變更此設定之後，請務必選取 [儲存]。

### <a name="model-update-frequency"></a>模型更新頻率

**模型更新頻率**會設定要將新的個人化工具模型重新定型的頻率。 

![模型更新頻率會設定要將新的個人化工具模型重新定型的頻率。](media/settings/configure-model-update-frequency-settings.png)

在變更此設定之後，請務必選取 [儲存]。

### <a name="data-retention"></a>資料保留

**資料保留期間**會設定個人化工具要將資料記錄保留多少天。 必須有過去的資料記錄才能執行[離線評估](concepts-offline-evaluation.md)，此評估可用來測量個人化工具是否有效，並將學習原則最佳化。

在變更此設定之後，請務必選取 [儲存]。

## <a name="export-the-personalizer-model"></a>匯出個人化工具模型

從 [資源管理] 的 [模型和原則] 區段，檢閱模型的建立日期和上次更新日期，然後匯出目前的模型。

![匯出目前的個人化工具模型](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>匯入和匯出學習原則

從 [資源管理] 的 [模型和原則] 區段，匯入新的學習原則或匯出目前的學習原則。

## <a name="next-steps"></a>後續步驟

[增強式學習](concepts-reinforcement-learning.md) 
