---
title: 電腦視覺 API Python 教學課程 | Microsoft Docs
description: 藉由在「Microsoft 認知服務」中使用 Jupyter Notebook，了解如何將「電腦視覺 API」與 Python 搭配使用。 使用常用的程式庫以視覺方式呈現您的結果。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368506"
---
# <a name="computer-vision-api-python-tutorial"></a>電腦視覺 API Python 教學課程

本教學課程說明如何使用以 Python 撰寫的「電腦視覺 API」，以及如何使用一些常用程式庫將您的結果以視覺化方式呈現。 使用 Jupyter 來執行教學課程。 若要了解如何開始使用互動式 Jupyter Notebook，請參考：[Jupyter 文件](http://jupyter.readthedocs.io/en/latest/index.html) \(英文\)。 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>在 Jupyter 中開啟教學課程 Notebook 

1. 瀏覽至 [GitHub 中的教學課程筆記本](https://github.com/Microsoft/Cognitive-Vision-Python)。 
2. 按一下綠色按鈕來複製或下載教學課程。 
3. 開啟命令提示字元，然後移至 _Cognitive-Vision-Python-master\Jupyter Notebook_資料夾。 
4. 從命令提示字元執行 **jupyter notebook** 命令。 這會啟動 Jupyter。
5. 在 Jupyter 視窗中，按一下 [Computer Vision API Example.ipynb] \(電腦視覺 API Example.ipynb\) 以開啟教學課程 Notebook 

### <a name="running-the-tutorial"></a>執行教學課程

若要使用這個 Notebook，您將需要有「電腦視覺 API」的訂用帳戶金鑰。 請瀏覽[訂用帳戶頁面](https://azure.microsoft.com/try/cognitive-services/)來註冊。 在 [登入] 頁面上，使用您的 Microsoft 帳戶進行登入，如此就能夠訂閱並取得免費金鑰。 完成註冊程序之後，將金鑰貼到下面重新產生之 Notebook 的變數區段中。 不論是主要金鑰還是次要金鑰都可運作。 請務必以引號括住金鑰來讓它變成字串。

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
