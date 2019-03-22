---
title: 使用 Azure Sentinel 預覽版中的 notebook 追捕功能 |Microsoft Docs
description: 本文說明如何使用 notebook 的 Azure Sentinel 追捕功能。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107673"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>使用 notebook 來搜尋有異常

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 的 Sentinel 利用互動式的 Jupyter notebook 提供深入解析及動作，以調查或安全性異常情況，您的環境內的搜尋。 Azure 的 Sentinel 隨附預先載入由 Microsoft 的安全性分析師所開發的 notebook。 每個筆記本是具有特定使用案例的獨立工作流程的用途。 視覺效果會包含在每個筆記本，更快速的資料探索和威脅追捕。 自訂您的需求、 從頭開始建立新的 notebook 或從 Azure Sentinel 匯入 notebook 的內建 notebook ' GitHub 社群。 Jupyter notebook 會匯入為 Azure Notebooks 頁面中的專案，如此可讓使用者存取所有其 Azure Sentinel 的 notebook 在同一個地方。 Notebook 會使用按鈕的執行，或以符合其環境的使用者可以設定。

完全整合的體驗可讓雲端運算和儲存體上執行無基礎的維護額外負荷的 notebook。 能夠運用現有的 Jupyter Notebook 生態系統可讓您群眾原始碼模型，而不需要共用客戶資料。 


每個筆記本會裝載於 Azure Notebooks （目前處於預覽狀態）、 Azure 雲端中的互動式開發環境。 Notebook 都可供存取且一律可從任何瀏覽器中，在世界各地。  Azure 的 Sentinel' 適用於調查和追捕的內建筆記本會複製到屬於您，但您可以修改，並為您的環境量身打造的專案。 最熱門的內建筆記本的部分包括：

- **警示調查和追捕**:此互動式 notebook 可擷取相關的活動，並產生警示的資料相關聯的活動與豐富的警示，讓快速分類的不同類別的警示。

- **端點主機引導式追捕**:可讓您藉由向下切入至 端點主機相關聯的安全性相關活動四處尋找有安全性缺口。  

- **Office 登入引導式捕捉**:可讓您透過視覺化的可疑的記錄檔的地理位置，以及顯示異常登入模式衍生自 Office 365 資料，積極可疑的登入。

## <a name="run-a-notebook"></a>執行 notebook
在下列範例中，我們會執行內建的 notebook，以搜尋深度探究位置異常狀況，若要查看 是否未預期的位置中的任何人正在執行您的網路上的內容。

1. 在 Azure Sentinel 入口網站中，按一下**Notebook** ，然後選取任何內建的 notebook。
  
   ![選取 notebook](./media/notebooks/select-notebook.png)

3. 按一下 **匯入**複製到您的 Azure Notebooks 專案的 GitHub 存放庫。
   ![匯入 notebook](./media/notebooks/import1.png)
4. 每個筆記本會引導您執行搜尋或調查的步驟。 若要啟用單鍵執行自動匯入模型、 程式庫和其他相依性和連線到 Azure Sentinel 的組態。 預先載入所有的程式碼和必要執行 notebook 的程式庫。 您可以立即開始執行 notebook，針對您的 Log Analytics 工作區，而不設定。

   ![匯入存放庫](./media/notebooks/import2.png)

5. 瀏覽、 修改和執行提供的所有範例筆記本。 這些可用來當做建置組塊的許多不同的案例。

   ![選取 notebook](./media/notebooks/import3.png)



## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何執行中 Azure Sentinel 使用 notebook 追捕調查。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動積極的威脅](hunting.md)
- [使用書籤來儲存時追捕的有趣資訊](bookmarks.md)