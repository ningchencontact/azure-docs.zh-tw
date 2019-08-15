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
ms.openlocfilehash: 09a3cc5a623be2ee5a9d50204f0902ca9f400a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857332"
---
1. [建立 Azure Machine Learning 服務工作區](../articles/machine-learning/service/how-to-manage-workspace.md)。

1. 複製 [GitHub 存放庫](https://aka.ms/aml-notebooks)。

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. 使用以下其中一種方法將工作區組態檔新增至複製的目錄：

    * 在 [Azure 入口網站](https://ms.portal.azure.com)中，從您工作區的 [概觀]  區段選取 [下載 config.json]  。 

    ![下載 config.json](./media/aml-dsvm-server/download-config.png)

    * 使用 [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) Notebook 中的程式碼在您複製的目錄中建立新的工作區。

1. 從複製的目錄中啟動 Notebook 伺服器。

    ```shell
    jupyter notebook
    ```