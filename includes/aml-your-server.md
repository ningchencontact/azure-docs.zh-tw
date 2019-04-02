---
title: 包含檔案
description: 包含檔案
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395666"
---
1. 使用[建立 Azure Machine Learning 服務工作區](../articles/machine-learning/service/setup-create-workspace.md#portal)的指示來建立 Miniconda 環境、建立工作區，以及撰寫工作區組態檔 (**aml_config/config.json**).

1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用以下其中一種方法新增工作區組態檔：
    * 將您使用必要快速入門建立的 **aml_config/config.json** 檔案複製到已複製的目錄。
    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的程式碼建立新的工作區。
1. 從複製的目錄中啟動 Notebook 伺服器。
    
    ```shell
    jupyter notebook
    ```