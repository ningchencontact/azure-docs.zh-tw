---
title: 表情 API Python 教學課程 | Microsoft Docs
description: 使用 Jupyter Notebook 來了解如何搭配 Python 使用認知服務表情 API。 使用常用的程式庫以視覺方式呈現您的結果。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368519"
---
# <a name="emotion-api-using-python-tutorial"></a>使用 Python 的表情 API 教學課程

> [!IMPORTANT]
> 影片 API 預覽將於 2017 年 10 月 30 日結束。 請試用新的[影片索引器 API 預覽](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，以輕鬆地從影片中擷取見解，並增強內容探索體驗，像是偵測話語、臉部、人物及表情而得來的搜尋結果。 [深入了解](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

為了讓您能輕鬆開始使用表情 API，以下連結的 Jupyter Notebook 會示範如何在 Python 中使用該 API，以及如何使用一些常用的程式庫以視覺方式呈現您的結果。 

[GitHub 中筆記本的連結](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb) \(英文\)

### <a name="using-the-jupyter-notebook"></a>使用 Jupyter Notebook

若要以互動方式使用該筆記本，您必須複製它，然後在 Jupyter 中執行。 若要了解如何開始使用互動式 Jupyter Notebook，請遵循以下網址中的指示：http://jupyter.readthedocs.org/en/latest/install.html \(英文\)。 

若要使用這個筆記本，您需要有表情 API 的訂用帳戶金鑰。 請造訪[訂用帳戶頁面](https://azure.microsoft.com/try/cognitive-services/)來註冊。 在 [登入] 頁面上，請使用您的 Microsoft 帳戶登入，便能訂閱並取得免費的金鑰。 完成註冊程序之後，請將金鑰貼至以下所示的變數區段中。 不論是主要金鑰還是次要金鑰都可運作。

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
