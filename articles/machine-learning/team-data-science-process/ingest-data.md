---
title: 將資料載入至 Azure 儲存體環境以便進行分析 | Microsoft Docs
description: 從 Azure Blob 儲存體來回移動資料
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: deguhath
ms.openlocfilehash: 369b06ee22b30977affb3642f0d321e5d92e6c41
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="load-data-into-storage-environments-for-analytics"></a>將資料載入至儲存體環境以便進行分析
Team Data Science Process 要求將資料內嵌或載入至各種不同的儲存體環境，以便在程序的每個階段中以最適當的方式處理或分析。 通常用於處理的資料目的地包括 Azure Blob 儲存體、SQL Azure 資料庫、Azure VM 上的 SQL Server、HDInsight (Hadoop) 和 Azure Machine Learning。 

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

此 **功能表** 所連結的主題會說明如何將資料內嵌至可儲存和處理資料的目標環境。

技術和商務需求以及資料的初始位置、格式和大小，會決定需要擷取資料到其中的目標環境以達成您的分析目標。 以下案例並不常見：要求在數個環境之間移動資料來達成建構預測模型所需的各種工作。 比方說，這一系列工作可以包含資料瀏覽、前置處理、清除、向下取樣和模型定型。

