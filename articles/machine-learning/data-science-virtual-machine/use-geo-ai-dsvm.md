---
title: 使用地區人工智慧資料科學虛擬機器 - Azure | Microsoft Docs
description: 了解如何使用地區 AI 資料科學虛擬機器來分析資料，並依據地理空間資料建立模型。
keywords: 深度學習、AI、資料科學工具、資料科學虛擬機器、地理空間分析
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 11026d642a5136ffcf0caa5815934115b6becf89
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238454"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>使用地區人工智慧資料科學虛擬機器

使用地區人工智慧 (AI) 資料科學虛擬機器 (VM) 來擷取要分析的資料、執行資料整理 (data wrangling)，以及為取用地理空間資訊的 AI 應用程式建立模型。 一旦您完成佈建地理 AI 資料科學 VM 並以您的 ArcGIS 帳戶登入 ArcGIS Pro，便可以開始與 ArcGIS 桌面和線上 ArcGIS 互動。 您也可以從 Python 介面以及從 R 語言橋接器 (後者需在地理資料科學 VM 上預先設定) 存取 ArcGIS。 若要建置豐富的 AI 應用程式，可以結合機器學習 (ML) 和深入學習架構以及其他資料科學 VM 上可用的資料科學軟體。  


## <a name="configuration-details"></a>組態詳細資料

Python 程式庫 [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) 是用來連接 ArcGIS 的介面，安裝在位於 ```c:\anaconda``` 的資料科學 VM 的全域根 conda 環境中。 

- 如果您在命令提示字元中執行 Python，執行 ```activate``` 啟動進入 conda 根 Python 環境。 
- 如果您使用整合式開發環境 (IDE) 或 Jupyter Notebook，可以選取環境或核心以確保您進入正確的 conda 環境。 

連接 ArcGIS 的 R 橋接器會安裝為名為 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 的 R 程式庫，放在位於 ```C:\Program Files\Microsoft\ML Server\R_SERVER``` 的主要 Microsoft R 伺服器獨立執行個體中。 Visual Studio、RStudio、Jupyter 皆已預先設定成使用此 R 環境，且能夠存取 ```arcgisbinding``` R 程式庫。 


## <a name="geo-ai-data-science-vm-samples"></a>地區 AI 資料科學 VM 範例

除了基底資料科學 VM 中以 ML 和深入學習架構為基礎的範例，本節亦提供幾個地理空間範例作為地區 AI 資料科學 VM 的一部分。 這些範例可幫助您立即開始使用地理空間資料和 ArcGIS 軟體開發 AI 應用程式。 


1. [開始使用 Python 進行地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)：此介紹範例示範如何使用 ArcGIS 的 Python 介面 (由 [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) 程式庫提供) 來處理地理空間資料。 此範例也示範如何結合傳統機器學習與地理空間資料，並以視覺化方式在 ArcGIS 地圖中呈現結果。 

2. [開始使用 R 進行地理空間分析](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)：此介紹範例示範如何使用 ArcGIS 的 R 介面 (由 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 程式庫提供) 來處理地理空間資料。 

3. [像素層級土地使用分類](https://github.com/Azure/pixel_level_land_classification)：此教學課程說明如何建立可輸入空拍影像、並傳回地表標籤的深度類神經網路模型。 地表標籤例如「森林覆蓋」、「水」。 模型會針對影像中的每個像素傳回這樣的標籤。 此模型是使用 Microsoft 的開放原始碼 [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) 深入學習架構所建置。 


## <a name="next-steps"></a>後續步驟

此處可找到其他使用資料科學 VM 的範例：

* [範例](dsvm-samples-and-walkthroughs.md)

