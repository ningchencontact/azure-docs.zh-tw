---
title: 設定個人化工具
titleSuffix: Azure Cognitive Services
description: 服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833916"
---
# <a name="configure-personalizer"></a>設定個人化工具

服務設定包括服務如何處理報酬、服務進行探索的頻率、模型重新定型的頻率，以及要儲存多少資料。

## <a name="create-personalizer-resource"></a>建立個人化工具資源

請針對每個意見反應迴圈建立一個個人化工具資源。

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 先前的連結會帶您前往個人化工具服務的 [建立] 頁面。
1. 輸入服務名稱、選擇訂用帳戶、位置、定價層及資源群組。
1. 選擇確認及 [建立]。

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>在 Azure 入口網站中設定服務

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。
1. 尋找個人化工具資源。
1. 在 [**資源管理**] 區段中 **，選取 [** 設定]。

    離開 Azure 入口網站之前，從**金鑰**頁面中複製其中一個資源金鑰。 您需要此金鑰方可使用[個人化工具 SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)。

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>根據使用案例設定意見反應迴圈的報酬

設定您的意見反應迴圈使用報酬的服務。 對下列值所做的變更將會重設目前的個人化工具模型，並將它重新定型為過去2天的資料：

![設定意見反應迴圈的獎勵值](media/settings/configure-model-reward-settings.png)

|值|目的|
|--|--|
|報酬等候時間|設定要讓個人化工具為排名呼叫收集報酬值的時間長度，這段時間從該排名呼叫發生的當下起算。 此值的設定方式是詢問：「應該個人化工具等待報酬電話多久？」 在此時段過後才抵達的報酬會記錄下來，但不會用於學習。|
|預設報酬|如果個人化工具未在「報酬等候時間」的時段內收到任何報酬呼叫，便會指派預設報酬。 預設報酬會預設為零，且大部分情況下皆是如此。|
|報酬彙總|如果從相同的排名 API 呼叫收到多個報酬，便會使用下列其中一個彙總方法：**總和**或**最早**。 「最早」會挑選最早收到的分數，並捨棄其餘分數。 如果您想要從可能重複的呼叫中挑出唯一的報仇，這會非常有用。 |

變更這些值之後，請務必選取 [**儲存**]。

### <a name="configure-exploration"></a>設定探索

個人化能夠探索新的模式，並藉由探索替代方案來適應隨著時間變化的使用者行為。 [**探索**] 值會決定要使用探索來回答的排名呼叫百分比。

對此值所做的變更將會重設目前的個人化工具模型，並以過去2天的資料重新定型。

![探索值會決定要使用探索來回答的排名呼叫百分比](media/settings/configure-exploration-setting.png)

變更此值之後，請務必選取 [**儲存**]。

### <a name="model-update-frequency"></a>模型更新頻率

個人化工具排名呼叫不會自動使用最新的模型 (已從每個作用中事件的獎勵 API 呼叫中進行定型)。 **模型更新頻率**會設定排名呼叫所用模型的更新頻率。

若您想要密切追蹤使用者行為的變更，可使用較高的模型更新頻率。 例如播放即時新聞、熱門內容或即時產品競標的網站。 在這些案例中，您可以使用 15 分鐘的頻率。 針對大多數使用案例，較低的更新頻率會比較有效。 一分鐘的更新頻率即適用於使用個人化工具對應用程式的程式碼進行偵錯、示範，或以互動方式測試機器學習服務的面向。

![模型更新頻率會設定要將新的個人化工具模型重新定型的頻率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

變更此值之後，請務必選取 [**儲存**]。

### <a name="data-retention"></a>資料保留

**資料保留期間**會設定個人化工具要將資料記錄保留多少天。 必須有過去的資料記錄才能執行[離線評估](concepts-offline-evaluation.md)，此評估可用來測量個人化工具是否有效，並將學習原則最佳化。

變更此值之後，請務必選取 [**儲存**]。

## <a name="export-the-personalizer-model"></a>匯出個人化工具模型

從 [**模型和學習設定**] 的 [資源管理] 區段中，查看 [模型建立] 和 [上次更新日期]，然後匯出目前的模型。 您可以使用 Azure 入口網站或個人化工具 API 來匯出要加以封存的模型檔案。

![匯出目前的個人化工具模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除學習迴圈的資料

1. 在 Azure 入口網站中，針對您的個人化工具資源，在 [**模型和學習設定**] 頁面上，選取 [**清除資料**]。
1. 若要清除所有資料，並將學習迴圈重設為原始狀態，請選取所有3個核取方塊。

    ![在 Azure 入口網站中，清除個人化工具資源中的資料。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|目的|
    |--|--|
    |記錄的個人化和報酬資料。|此記錄資料會用於離線評估。 如果您要重設資源，請清除資料。|
    |重設個人化工具模型。|此模型會在每次重新定型時變更。 此定型頻率**會在 [設定] 頁面上**的 **[上傳模型頻率**] 中指定。 |
    |將 [學習原則] 設定為 [預設]。|如果您已將學習原則變更為離線評估的一部分，則會重設為原始學習原則。|

1. 選取 [**清除選取的資料**] 以開始清除進程。 狀態會在 Azure 通知的右上方導覽中回報。

## <a name="next-steps"></a>後續步驟

[瞭解如何管理學習原則](how-to-learning-policy.md)
