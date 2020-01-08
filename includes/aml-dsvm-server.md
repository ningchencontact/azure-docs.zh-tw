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
ms.openlocfilehash: ac5656a8efe1dee932bfe753f8bdc06b787e20af
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529777"
---
1. [建立 Azure Machine Learning 工作區](../articles/machine-learning/how-to-manage-workspace.md)。

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