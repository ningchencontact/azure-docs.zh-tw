---
title: 轉換為 CSV:模組參考
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure Machine Learning 服務中使用 轉換成 CSV 模組，將資料集轉換為 CSV 格式，可以下載、 匯出或共用以 R 或 Python 指令碼模組。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028721"
---
# <a name="convert-to-csv-module"></a>將轉換成 CSV 模組

本文說明 Azure Machine Learning 服務的視覺化介面 （預覽） 的模組。

使用此模組將資料集轉換為 CSV 格式，可以下載、 匯出或共用以 R 或 Python 指令碼模組。

### <a name="more-about-the-csv-format"></a>深入了解 CSV 格式 

CSV 格式，代表 「 逗號分隔值 」，是許多外部的機器學習工具所使用的檔案格式。 CSV 是一般的交換格式，使用 R 或 Python 等的開放原始碼語言時。

即使您執行大部分的 Azure Machine Learning 中工作時，有些時可能會很方便地將您的資料集轉換為 CSV 來使用外部工具 中的時候。 例如:

+ 下載 CSV 檔案，以開啟 excel，或匯入關聯式資料庫。  
+ 儲存至雲端儲存體，並從建立視覺效果的 Power BI 連接到該 CSV 檔案。  
+ 使用 CSV 格式來準備資料以用於 R 和 Python。 只要以滑鼠右鍵按一下產生直接從 Python 或 Jupyter notebook 中存取資料所需的程式碼模組的輸出。 

當您將資料集轉換為 CSV 時，檔案會儲存在您的 Azure ML 工作區中。 您可以使用 Azure 儲存體公用程式來開啟，並將檔案直接使用，或您可以以滑鼠右鍵按一下模組的輸出和 CSV 檔案下載到您的電腦，或在 R 或 Python 程式碼中使用它。  

## <a name="how-to-configure-convert-to-csv"></a>如何設定轉換為 CSV

1.  新增[轉換為 CSV](./convert-to-csv.md)模組至您的實驗。 您可以找到此模組中的**資料格式轉換**介面中的群組。 

2. 請將它連接到任何輸出資料集的模組。   
  
3.  執行實驗。

### <a name="results"></a>結果
  

按兩下的輸出[轉換為 CSV](./convert-to-csv.md)，然後選取其中一個選項。  

 + **結果資料集]-> [下載**:立即開啟一份資料以 CSV 格式，您可以儲存至本機資料夾。 如果您未指定資料夾，會套用預設檔案名稱和 CSV 檔案會儲存在本機**下載**程式庫。


 + **結果資料集]-> [儲存為資料集**:儲存回 Azure ML 工作區的 CSV 檔案，做為個別的資料集。

 + **產生資料存取程式碼**:Azure ML 會產生兩組程式碼，以存取資料，使用 Python 或。若要存取資料，將複製到您的應用程式的程式碼片段。 (*產生的資料存取程式碼會立即出現。* )

## <a name="next-steps"></a>後續步驟

請參閱[可用的模組集](module-reference.md)Azure Machine Learning 服務。 