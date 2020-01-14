---
title: 作用中和非作用中的事件-個人化工具
titleSuffix: Azure Cognitive Services
description: 本文討論如何在個人化工具服務內使用作用中和非作用中的事件、學習設定和學習原則。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834468"
---
# <a name="active-and-inactive-events"></a>使用中和非使用中的事件

當您的應用程式呼叫排名 API 時，您會收到應用程式應該在 [ **rewardActionId** ] 欄位中顯示的動作。  從那時起，個人化工具預期具有相同 eventId 的報酬呼叫。 報酬分數將用來定型模型，以供未來的排名呼叫使用。 如果沒有收到 eventId 的報酬電話，則會套用預設報酬。 預設獎勵會在 Azure 入口網站中設定。

在某些情況下，應用程式可能需要先呼叫 Rank，才知道結果將使用或顯示給使用者。 例如，當行銷活動覆寫升級內容的頁面轉譯時，可能會發生這種情況。 如果從未使用過 Rank 呼叫的結果，且使用者從未看到它，則不傳送對應的報酬呼叫。

一般來說，這些案例會發生在下列情況：

* 您是可呈現的 UI，使用者可能會看到或看不到。
* 您的應用程式正在執行預測性個人化，其中的排名呼叫是以極少的即時內容進行，而且應用程式可能會或可能不會使用輸出。

在這些情況下，請使用個人化工具呼叫 Rank，要求事件為_非_使用中。 個人化工具不會預期此事件的報酬，而且不會套用預設報酬。
稍後在您的商務邏輯中，如果應用程式使用排名呼叫中的資訊，只要_啟動_事件即可。 一旦活動處於作用中狀態，個人化工具預期會有事件報酬。 如果沒有對獎勵 API 進行明確的呼叫，個人化工具會套用預設的報酬。

## <a name="inactive-events"></a>非作用中事件

若要停用事件的定型，請使用 `learningEnabled = False`呼叫 Rank。 針對非作用中事件，如果您傳送 eventId 的報酬或為該 eventId 呼叫 `activate` API，則會隱含地啟用學習。

## <a name="learning-settings"></a>學習設定

學習設定會決定模型定型的*超參數*。 在不同學習設定上定型之相同資料的兩種模型最終會有所不同。

### <a name="import-and-export-learning-policies"></a>匯入和匯出學習原則

您可以從 Azure 入口網站匯入和匯出學習原則檔案。 您可以使用這個方法來儲存現有的原則、加以測試、加以取代，然後在原始程式碼控制中將它們封存為成品，以供日後參考和審核。

瞭解[如何](how-to-learning-policy.md)匯入和匯出學習原則。

### <a name="understand-learning-policy-settings"></a>瞭解學習原則設定

學習原則中的設定不會進行變更。 只有在您瞭解它們如何影響個人化工具時，才變更設定。 若沒有此知識，您可能會造成問題，包括使個人化工具模型失效。

個人化工具使用[vowpalwabbit](https://github.com/VowpalWabbit)來定型和評分事件。 如需如何使用 vowpalwabbit 編輯學習設定的相關資訊，請參閱[vowpalwabbit 檔](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)。 當您有正確的命令列引數之後，請將命令儲存至具有下列格式的檔案（以所需的命令取代 arguments 屬性值），然後上傳檔案，以匯入個人化工具資源之 [**模型與學習設定**] Azure 入口網站窗格中的學習設定。

下列 `.json` 是學習原則的範例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>比較學習原則

您可以藉由執行[離線評估](concepts-offline-evaluation.md)，比較不同學習原則對個人化工具記錄中過去資料執行的方式。

[上傳您自己的學習原則](how-to-learning-policy.md)，將其與目前的學習原則進行比較。

### <a name="optimize-learning-policies"></a>優化學習原則

個人化工具可以在[離線評估](how-to-offline-evaluation.md)中建立優化學習原則。 在離線評估中有更佳報酬的優化學習原則，在個人化工具中線上使用時，將會產生更佳的結果。

優化學習原則之後，您可以直接將它套用至個人化工具，讓它立即取代目前的原則。 或者，您可以儲存優化的原則以供進一步評估，並在稍後決定要捨棄、儲存或套用它。
