---
title: 主動式學習 - 個人化工具
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
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663717"
---
# <a name="active-learning-and-learning-policies"></a>主動式學習及學習原則 

當您的應用程式呼叫排名 API 時，您會收到內容的排名。 商務邏輯會使用此排名來判斷是否要向使用者顯示內容。 若您顯示排名的內容，這就是「作用中」  排名事件。 若您的應用程式沒有顯示排名的內容，這就是「非作用中」  排名事件。 

作用中排名事件資訊會傳回給個人化工具。 這項資訊會用在繼續以目前學習原則定型模型。

## <a name="active-events"></a>作用中事件

作用中事件應一律顯示給使用者，並傳回獎勵呼叫，以關閉學習迴圈。 

### <a name="inactive-events"></a>非作用中事件 

非作用中事件不應該變更基礎模型，因為使用者未獲得可從排名內容中做選擇的機會。

## <a name="dont-train-with-inactive-rank-events"></a>請勿以非作用中排名事件來進行定型 

對於某些應用程式，您可能需要在不知道應用程式是否會向使用者顯示結果的情況下，呼叫排名 API。 

發生這種情況的時機：

* 您可能預先轉譯了使用者可能會或可能不會看到的某些 UI。 
* 您的應用程式可能進行了預測性的個人化，其中排名呼叫是以較不即時的內容來進行，而且其輸出可能會或可能不會由應用程式使用。 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>在排名呼叫期間，對非作用中排名事件停用主動式學習

若要停用自動學習，請以 `learningEnabled = False` 呼叫排名。

如果您傳送排名的獎勵，則非作用中事件的學習會隱含地啟用。

## <a name="learning-policies"></a>學習原則

學習原則會決定模型定型的特定「超參數」  。 資料相同的兩個模型，若在不同的學習原則上進行定型，則兩個模型會有不同的行為。

### <a name="importing-and-exporting-learning-policies"></a>匯入和匯出學習原則

您可以在 Azure 入口網站中匯入和匯出學習原則檔案。 這可讓您儲存現有原則，並測試及取代這些原則，以及在原始程式碼控制中將這些原則封存為成品，以便未來加以參考和稽核。

### <a name="learning-policy-settings"></a>學習原則設定

**學習原則**的設定應不可變更。 只有在您了解變更會如何影響個人化工具時，才能變更設定。 在不知道會有何影響的情況下變更設定會導致副作用，例如個人化工具模型失效。

### <a name="comparing-effectiveness-of-learning-policies"></a>比較學習原則的有效性

您可以執行[離線評估](concepts-offline-evaluation.md)，針對個人化工具記錄中的過去資料，比較不同學習原則的成效。

[上傳您自己的學習原則](how-to-offline-evaluation.md)來與目前的學習原則做比較。

### <a name="discovery-of-optimized-learning-policies"></a>探索最佳學習原則

執行[離線評估](how-to-offline-evaluation.md)時，個人化工具就可以建立更好的學習原則。 在線上個人化工具中使用更好的學習原則 (也就是在離線評估中有更好的獎勵) 會產生更好的結果。

建立最佳化的學習原則後，您可以將該原則直接套用到個人化工具，讓其立即取代目前的原則，或是您可以將其儲存以進行進一步評估，並在未來決定是否要捨棄、儲存或套用該原則。