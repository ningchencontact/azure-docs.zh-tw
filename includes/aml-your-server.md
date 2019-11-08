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
ms.date: 09/26/2019
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476080"
---
1. 使用位於 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) \(英文\) 的指示來安裝適用於 Python 的 Azure Machine Learning SDK

1. 建立 [Azure Machine Learning 工作區](../articles/machine-learning/service/how-to-manage-workspace.md)。

1. 撰寫[組態檔](../articles/machine-learning/service/how-to-configure-environment.md#workspace)檔案 (**aml_config/config.json**)。

1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 從複製的目錄中啟動 Notebook 伺服器。

    ```shell
    jupyter notebook
    ```
