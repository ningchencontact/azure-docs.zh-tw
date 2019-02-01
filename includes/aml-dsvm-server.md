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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302179"
---
1. 完成 [Azure Machine Learning Python 快速入門](../articles/machine-learning/service/quickstart-create-workspace-with-python.md)以建立工作區。  您可以視需要放心略過**使用 Notebook** 的部分。
1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. 使用以下其中一種方法新增工作區組態檔：
    * 將您使用必要快速入門建立的 **aml_config\config.json** 檔案複製到已複製的目錄。
    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) Notebook 中的程式碼在您複製的目錄中建立新的工作區。
1. 從複製的目錄中啟動 Notebook 伺服器。
    
    ```shell
    jupyter notebook
    ```