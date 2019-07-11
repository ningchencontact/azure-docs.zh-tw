---
title: 案例評估 - 個人化工具
titleSuffix: Azure Cognitive Services
description: 在任何情況下，只要應用程式可以選取正確的項目、動作或產品來顯示，便可以套用個人化工具來創造更好的體驗、達成更佳的商務成果或提升生產力。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: a5bf0d7d0478ad836ed1921b302ab01b004ec73c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722226"
---
# <a name="where-can-you-use-personalizer"></a>個人化工具可以應用在何處？

在任何情況下，如果應用程式需要選取正確的項目、動作或產品來顯示，請用個人化工具來創造更好的體驗、達成更佳的商務成果或提升生產力。 

個人化工具會使用機器學習來選取要對使用者顯示的動作。 根據傳送給服務的資料數量、品質及分佈，選取項目會極為不同。

### <a name="checklist-for-applying-personalizer"></a>適用於套用個人化工具的檢查清單


您可以在下列情況中套用個人化工具：

* 您期望應用程式能達成商務或可用性目標。
* 應用程式中有某個地方若能根據內容來決定要對使用者顯示的項目，將可改善該目標。
* 從使用者的整體行為和總報酬分數，將能學習到最佳選擇，且應該這麼做。
* 在為了個人化而使用機器學習時，有遵循[負責任的使用指導方針](ethics-responsible-use.md)和您選擇的選項。
* 根據內容做決策的行為，可以透過從一組有限選項來決定最佳選項 (動作) 排名的方式來表示。
* 藉由測量使用者行為的某些層面，並透過「報酬分數」  來加以表示，可以判斷經過排名的選項對您應用程式的適用程度。 報酬分數是 -1 到 1 之間的數字。
* 報酬分數中不會放入太多干擾因素或外部因素。 實驗的持續時間不會太久，既能計算出報酬分數，又不會喪失其關聯性。
* 您可以將排名的內容以至少有 5 個[特性](concepts-features.md)的清單來表示，這些特性是您認為有助於做出正確選擇，且不包含個人識別資訊的特性 (PII)。
* 您擁有每個內容選項 (即動作  ) 的相關資訊，其形式為含有至少 5 個您認為有助於個人化工具做出正確選擇的[特性](concepts-features.md)。
* 應用程式可以長時間保留資料，而足以累積至少 100,000 個互動的歷程記錄。

## <a name="machine-learning-considerations-for-applying-personalizer"></a>適用於套用個人化工具的機器學習考量

個人化工具是以增強式學習作為基礎，這是一種適用於藉由提供意見反應來對其做出教導行為的機器學習方法。 

在下列情況時，個人化工具會有最佳學習效果：

* 即使問題會隨時間而變化 (例如，對於新聞或流行的偏好)，仍有足夠事件可以掌握最佳個人化。 個人化工具會適應真實世界的持續變化，但如果沒有足夠的事件和資料可供學習以便探索並確定新的模式，就無法獲得最佳結果。 請選擇發生頻率夠高的使用案例。 在尋找時，請考慮每天至少發生 500 次的使用案例。
* 內容和動作有足夠的[特性](concepts-features.md)能夠促進學習。
* 每次呼叫時，所要排名的動作不超過 50 個。
* 資料保留設定可讓個人化工具收集足夠的資料來執行離線評估和原則最佳化。 一般來說，至少要有 50,000 個資料點。

## <a name="monitor-effectiveness-of-personalizer"></a>監視個人化工具的效果

您可以定期地藉由執行[離線評估](concepts-offline-evaluation.md)來監視個人化工具的效果。

## <a name="use-personalizer-with-recommendation-engines"></a>搭配使用個人化工具與推薦引擎

許多公司使用推薦引擎、行銷和宣傳工具、對象區隔和群集、協同篩選以及其他方式，從龐大的目錄中向客戶推薦產品。

[Microsoft Recommender GitHub 存放庫](https://github.com/Microsoft/Recommenders)提供了用來建置推薦系統的範例和最佳做法 (以 Jupyter Notebook 的形式提供)。 其提供的實用範例可用於準備資料、建置模型、評估、調整以及運作推薦引擎，並適用於許多常見方法 (包括 xDeepFM、SAR、ALS、RBM、DKN)。

在有推薦引擎時，個人化工具也可與其搭配運作。

* 推薦引擎會取出大量項目 (例如 500,000 個)，並從數百或數千個選項中推薦其中的幾個 (例如最好的 20 個)。
* 個人化工具會取出一小部分動作與其大量相關資訊，並針對給定的豐富內容即時對這些動作排名，但大多數的推薦引擎則只會使用一些與使用者、產品和其互動有關的屬性。
* 個人化工具的設計目的，是為了能夠隨時自動探索使用者的偏好，以在內容會快速變化的情況 (例如，新聞、現場活動、即時社群內容、有每日更新的內容或季節性的內容) 中產生更好的結果。

常見用法是取得推薦引擎的輸出 (例如，某位客戶的前 20 項產品)，並將其作為個人化工具的輸入動作。

## <a name="next-steps"></a>後續步驟

[倫理和負責使用](ethics-responsible-use.md)。