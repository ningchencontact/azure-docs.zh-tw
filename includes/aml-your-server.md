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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123201"
---
1. 依照[建立 Azure Machine Learning 服務工作區](../articles/machine-learning/service/setup-create-workspace.md#portal)中的指示執行下列工作：
    * 建立 Miniconda 環境
    * 安裝適用於 Python 的 Azure Machine Learning SDK
    * 建立工作區
    * 撰寫工作區設定檔 (**aml_config/config.json**)。
    
1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用以下各種方法新增工作區設定檔：
    * 將您在步驟 1 建立的 **aml_config/config.json** 檔案複製到已複製的目錄。

    * 在 [Azure 入口網站](https://ms.portal.azure.com)中，從您工作區的 [概觀] 區段選取 [下載 config.json]。 

    ![下載 config.json](./media/aml-dsvm-server/download-config.png)

    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) 中的程式碼建立新的工作區。

1. 從複製的目錄中啟動 Notebook 伺服器。
    
    ```shell
    jupyter notebook
    ```