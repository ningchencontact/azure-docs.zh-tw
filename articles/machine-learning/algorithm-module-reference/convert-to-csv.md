---
title: 轉換成 CSV：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [轉換成 CSV] 模組，將資料集轉換成 CSV 格式，您可以使用 R 或 Python 腳本模組進行下載、匯出或共用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 999f78ab08e1a2c9dd6b28d853e49fbb559fab83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493849"
---
# <a name="convert-to-csv-module"></a>轉換成 CSV 模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

您可以使用此模組，將資料集轉換成 CSV 格式，以供使用 R 或 Python 腳本模組下載、匯出或共用。

### <a name="more-about-the-csv-format"></a>關於 CSV 格式的詳細資訊 

CSV 格式（代表「逗號分隔值」）是許多外部機器學習工具所使用的檔案格式。 CSV 是使用開放原始碼語言（例如 R 或 Python）時的通用交換格式。

即使您在 Azure Machine Learning 中執行大部分的工作，有時可能會發現將資料集轉換成 CSV 以用於外部工具是很方便的。 例如：

+ 下載 CSV 檔案，以使用 Excel 開啟檔案，或將它匯入關係資料庫。  
+ 將 CSV 檔案儲存至雲端存放裝置，並從 Power BI 連接到該檔案，以建立視覺效果。  
+ 使用 CSV 格式來準備要在 R 和 Python 中使用的資料。 只要以滑鼠右鍵按一下模組的輸出，即可產生直接從 Python 或 Jupyter 筆記本存取資料所需的程式碼。 

當您將資料集轉換成 CSV 時，該檔案會儲存在您的 Azure ML 工作區中。 您可以使用 Azure 儲存體公用程式來直接開啟和使用檔案，或者您可以用滑鼠右鍵按一下模組輸出並將 CSV 檔案下載到您的電腦，或在 R 或 Python 程式碼中使用它。  

## <a name="how-to-configure-convert-to-csv"></a>如何設定轉換成 CSV


1.  將 [[轉換成 CSV](./convert-to-csv.md) ] 模組新增至您的管線。 您可以在設計工具的 [**資料格式轉換**] 群組中找到此模組。 

2. 將它連接到輸出資料集的任何模組。   
  
3.  執行管道。

### <a name="results"></a>結果
  

按兩下 [[轉換成 CSV](./convert-to-csv.md)] 的輸出，然後選取其中一個選項。  

 + **結果資料集-> 下載**：立即開啟 CSV 格式的資料複本，您可以將其儲存至本機資料夾。 如果您沒有指定資料夾，則會套用預設檔案名，而且 CSV 檔案會儲存在本機**下載**程式庫中。


 + **結果資料集-> 另存為資料集**：將 CSV 檔案儲存回 Azure ML 工作區作為個別的資料集。

 + **產生資料存取程式碼**： Azure ML 會產生兩組程式碼，以供您使用 Python 或使用 R 來存取資料。若要存取資料，請將程式碼片段複製到您的應用程式。 （*產生資料存取程式碼即將推出）。*

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 