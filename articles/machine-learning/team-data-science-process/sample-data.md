---
title: 在 Azure Blob 容器、SQL Server 和 Hive 資料表中進行資料取樣 | Microsoft Docs
description: 如何探索儲存在各種 Azure 環境中的資料。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: b7b233532a526879e886950c3a0d8cfe169251a7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441497"
---
# <a name="heading"></a>在 Azure Blob 容器、SQL Server 和 Hive 資料表中進行資料取樣

下列文章說明如何對三個不同 Azure 位置中任一個位置所儲存的資料進行取樣：

* [**Azure Blob 容器資料**](sample-data-blob.md)會以程式設計方式進行下載，然後使用 Python 程式碼進行取樣。
* [**SQL Server 資料**](sample-data-sql-server.md)會使用 SQL 和 Python 程式設計語言進行取樣。 
* [**Hive 資料表**](sample-data-hive.md)會使用 Hive 查詢進行取樣。

這個取樣工作是 [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的一個步驟。

**為何要對資料進行取樣？**

如果您規劃分析的資料集很龐大，通常最好是對資料進行向下取樣，將資料縮減為更小但具代表性且更容易管理的大小。 這有助於資料了解、探索和功能工程。 它在 Cortana 分析程序中扮演的角色是能夠快速建立資料處理函式與機器學習服務模型的原型。

