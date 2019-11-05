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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489588"
---
**計算目標可以從一個定型作業重複使用到下一個**。 例如，將遠端 VM 連結至您的工作區之後，您可以將它重複用於多個作業。

|訓練 &nbsp;目標| GPU 支援 |[自動化 ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML 管線](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning 設計工具](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[本機電腦](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 比如 | 是 | &nbsp; | &nbsp; |
|[Azure Machine Learning 計算實例](../articles/machine-learning/service/concept-compute-instance.md)| 是 | | 是 |  |
|[Azure Machine Learning 計算叢集](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| 是 | 是 & <br/>超參數&nbsp;微調 | 是 | 是 |
|[遠端虛擬機器](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |是 | 是 & <br/>超參數微調 | 是 | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | 是 | 是 | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | 是 | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | 是 | &nbsp; |
