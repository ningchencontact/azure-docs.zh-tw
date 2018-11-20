---
title: 快速入門：使用 Azure REST API 和 Python 偵測影像中的臉部
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用 Azure Face REST API 搭配 Python 來偵測影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: d2a98226895bbe5996785ca4726f20df9b98ffdd
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683605"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>快速入門：使用 Face REST API 和 Python 偵測影像中的臉部

在本快速入門中，您將使用 Azure Face REST API 搭配 Python 來偵測影像中的人臉。 指令碼會在人臉周圍繪製框架，並在影像上加上性別和年齡資訊。

![一名男性和一名女性，其臉部周圍繪有矩形，且影像上會顯示年齡和性別](../images/labelled-faces-python.png)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 


## <a name="prerequisites"></a>必要條件

- 臉部 API 訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)取得免費的試用訂用帳戶金鑰。 或是，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱臉部 API 服務並取得金鑰。

## <a name="run-the-jupyter-notebook"></a>執行 Jupyter Notebook

您可以在 [MyBinder](https://mybinder.org) 上以 Jupyter Notebook 形式執行本快速入門。 若要啟動 Binder，請選取下方的按鈕。 然後，遵循 Notebook 中的指示來進行。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>後續步驟

接下來，請瀏覽臉部 API 參考文件，以深入了解支援的案例。

> [!div class="nextstepaction"]
> [臉部 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
