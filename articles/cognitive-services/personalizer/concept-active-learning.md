---
title: 作用中和非作用中的事件-個人化工具
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 321f12fef44cae43caf53d78b2908e68f9edd0a8
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043895"
---
# <a name="active-and-inactive-events"></a>作用中和非作用中的事件

當您的應用程式呼叫排名 API 時，您會收到應用程式應該在 [rewardActionId] 欄位中顯示的動作。  從那時起，個人化工具會預期具有相同 eventId 的報酬呼叫。 報酬分數將用來定型將用於未來排名呼叫的模型。 如果沒有收到 eventId 的報酬電話，則會套用預設報酬。 預設報酬會在 Azure 入口網站中建立。

在某些情況下，應用程式可能需要先呼叫 Rank，才知道結果將使用或顯示給使用者。 例如，當升級內容的頁面轉譯被行銷活動覆寫時，可能會發生這種情況。 如果順位呼叫的結果從未用過，而且使用者也看不到它，就不正確地使用任何報酬來定型，零或其他。
這通常發生在下列情況：

* 您可能預先轉譯了使用者可能會或可能不會看到的某些 UI。 
* 您的應用程式可能進行了預測性的個人化，其中排名呼叫是以較不即時的內容來進行，而且其輸出可能會或可能不會由應用程式使用。 

在這些情況下，使用個人化工具的正確方式是呼叫 Rank 要求事件為_非_使用中。 個人化工具不會預期此事件的報酬，而且也不會套用預設報酬。 稍後在您的商務邏輯中，如果應用程式使用排名呼叫中的資訊，您只需要_啟動_事件。 從活動開始，個人化工具會預期該事件的報酬，或在沒有對獎勵 API 進行明確呼叫時套用預設報酬。

## <a name="get-inactive-events"></a>取得非作用中事件

若要停用事件的定型，請使用 `learningEnabled = False` 來呼叫 Rank。

如果您傳送 eventId 的報酬，或為該 eventId 呼叫 `activate` API，則會隱含地學習非作用中的事件。

## <a name="learning-settings"></a>學習設定

學習設定會決定模型定型的特定*超參數*。 相同資料的兩種模型（在不同的學習設定上定型）最後會不同。

### <a name="import-and-export-learning-policies"></a>匯入和匯出學習原則

您可以在 Azure 入口網站中匯入和匯出學習原則檔案。 這可讓您儲存現有原則，並測試及取代這些原則，以及在原始程式碼控制中將這些原則封存為成品，以便未來加以參考和稽核。

### <a name="learning-policy-settings"></a>學習原則設定

**學習原則**的設定應不可變更。 只有在您了解變更會如何影響個人化工具時，才能變更設定。 在不知道會有何影響的情況下變更設定會導致副作用，例如個人化工具模型失效。

### <a name="comparing-effectiveness-of-learning-policies"></a>比較學習原則的有效性

您可以執行[離線評估](concepts-offline-evaluation.md)，針對個人化工具記錄中的過去資料，比較不同學習原則的成效。

[上傳您自己的學習原則](how-to-offline-evaluation.md)來與目前的學習原則做比較。

### <a name="discovery-of-optimized-learning-policies"></a>探索最佳學習原則

執行[離線評估](how-to-offline-evaluation.md)時，個人化工具就可以建立更好的學習原則。 在線上個人化工具中使用更好的學習原則 (也就是在離線評估中有更好的獎勵) 會產生更好的結果。

建立最佳化的學習原則後，您可以將該原則直接套用到個人化工具，讓其立即取代目前的原則，或是您可以將其儲存以進行進一步評估，並在未來決定是否要捨棄、儲存或套用該原則。
