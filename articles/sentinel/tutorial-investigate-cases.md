---
title: 使用 Azure Sentinel Preview 調查事件 |Microsoft Docs
description: 使用此教學課程來瞭解如何使用 Azure Sentinel 調查事件。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780390"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>教學課程：使用 Azure Sentinel Preview 調查事件

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教學課程可協助您偵測 Azure Sentinel 的威脅。

在您將[資料來源連接](quickstart-onboard.md)到 Azure Sentinel 之後, 您會想要在發生可疑的情況時收到通知。 為了讓您這麼做, Azure Sentinel 可讓您建立高階警示規則, 以產生可指派的事件, 並用於深入調查環境中的異常和威脅。 

> [!div class="checklist"]
> * 建立事件
> * 調查事件
> * 回應威脅

## <a name="investigate-incidents"></a>調查事件

事件可以包含多個警示。 它是特定調查的所有相關辨識項的匯總。 系統會根據您在 [**分析**] 頁面中定義的警示建立事件。 與警示相關的屬性 (例如嚴重性和狀態) 會在事件層級設定。 當您讓 Azure Sentinel 知道您要尋找的威脅種類, 以及如何尋找它們之後, 您可以藉由調查事件來監視偵測到的威脅。 

1. 選取 [**事件**]。 [**事件**] 頁面可讓您知道有多少事件、已開啟的數目、已設定為**進行中**的數目, 以及已關閉的事件數目。 針對每個事件, 您可以查看發生的時間, 以及事件的狀態。 查看嚴重性以決定要先處理的內容。 在 [**事件**] 頁面中, 按一下 [**警示**] 索引標籤, 查看與事件相關的所有警示。 您稍早在事件中對應的實體可以在 [**實體**] 索引標籤中查看。您可以視需要篩選事件, 例如 [狀態] 或 [嚴重性]。 當您查看 [**事件**] 索引標籤時, 您會看到 [開啟事件], 其中包含您在**分析**中定義的偵測規則所觸發的警示。 在頂端, 您會看到您的使用中事件、新事件和進行中事件。 您也可以依嚴重性查看所有事件的總覽。

   ![警示儀表板](./media/tutorial-investigate-cases/cases.png)

2. 若要開始調查, 請按一下特定事件。 在右側, 您可以看到事件的詳細資訊, 包括其嚴重性、涉及的實體數目摘要 (根據您的對應)。 每個事件都有唯一的識別碼。 事件的嚴重性是根據事件中所包含的最嚴重警示來判斷。  

1. 若要在事件中查看警示和實體的更多詳細資料, 請按一下 [事件] 頁面中的 [**查看完整詳細資料**], 並查看摘要事件資訊的相關索引標籤。  完整的事件檢視會合並警示中的所有辨識項、相關聯的警示和實體。

1. 在 [**警示**] 索引標籤中, 檢查警示本身-觸發時間, 以及超過您設定的閾值。 您可以查看警示的所有相關資訊–觸發警示的查詢、每個查詢所傳回的結果數目, 以及對警示執行腳本的能力。 若要更進一步向下切入事件, 請按一下 [點擊次數]。 這會開啟產生結果的查詢, 以及在 Log Analytics 中觸發警示的結果。

3. 在 [**實體**] 索引標籤中, 您可以看到您對應為警示規則定義之一部分的所有實體。 

4. 如果您要主動調查事件, 建議您將事件狀態設定為 [**進行中**], 直到您關閉它為止。 您也可以關閉事件, 其中 [**已關閉已解決**] 是指出事件已處理的事件狀態, 而 [**已關閉**] 則是不需要處理之事件的狀態。 說明您必須說明關閉事件的理由。

5. 事件可以指派給特定的使用者。 針對每個事件, 您可以藉由設定 [事件**擁有**者] 欄位來指派擁有者。 所有事件都以未指派的形式啟動。 您可以移至事件並依您的名稱篩選, 以查看您擁有的所有事件。 

5. 按一下 [**調查**] 以使用補救步驟來查看調查對應和缺口的範圍。 



## <a name="respond-to-threats"></a>回應威脅

Azure Sentinel 提供兩個主要選項, 讓您使用腳本來回應威脅。 您可以設定腳本在觸發警示時自動執行, 也可以手動執行腳本來回應警示。

- 您可以將腳本設定為在設定腳本時觸發警示時自動執行。 

- 您可以從警示內部手動執行腳本, 方法是按一下 [ **View**腳本], 然後選取要執行的腳本。




## <a name="next-steps"></a>後續步驟
在本教學課程中, 您已瞭解如何使用 Azure Sentinel 開始調查事件。 繼續進行教學課程, 以瞭解[如何使用自動化的操作手冊來回應威脅](tutorial-respond-threats-playbook.md)。
> [!div class="nextstepaction"]
> [回應威脅](tutorial-respond-threats-playbook.md), 將您對威脅的回應自動化。

