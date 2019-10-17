---
title: 資料探索和虛擬化工具
titleSuffix: Azure Data Science Virtual Machine
description: 資料科學虛擬機器的資料探索和視覺效果工具。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330708"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器上的資料探索和視覺效果工具

在資料科學中，關鍵在於瞭解資料。 視覺效果和資料探索工具有助於加速資料了解。 資料科學虛擬機器（DSVM）上提供的下列工具可讓您更輕鬆地進行這個重要步驟。

## <a name="apache-drill"></a>Apache 深入探詢
|    |           |
| ------------- | ------------- |
| 這是什麼？   | 海量資料的開放原始碼 SQL 查詢引擎    |
| 支援的 DSVM 版本      | Windows、Linux  |
| 它如何在 DSVM 上進行設定和安裝？      |  僅在 `/dsvm/tools/drill*` 內嵌模式中安裝   |
| 典型的使用案例      |  適用于就地資料探索，而不需要解壓縮、轉換、載入（ETL）。 查詢不同的資料來源和格式，包括 CSV、JSON、關聯式資料表和 Hadoop。     |
| 如何使用並加以執行      | 桌面快捷方式  <br/> [在 10 分鐘內開始使用深入探詢](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM 上的相關工具      |   Rattle、Weka、SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| 這是什麼？   | 互動式資料視覺效果和 BI 工具    |
| 支援的 DSVM 版本      | Windows  |
| 典型的使用案例      |  資料視覺效果和建立儀表板   |
| 如何使用並加以執行      | 桌面快捷方式（`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`），或直接從 [**開始**] 功能表執行。      |
| DSVM 上的相關工具      |   Visual Studio 2019、Visual Studio Code、Juno      |

> [!NOTE]
> 您需要 Microsoft Office 365 帳戶才能存取 Power BI。


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   使用 R 進行資料採礦的圖形化使用者介面（GUI）   |
| 支援的 DSVM 版本     | Windows、Linux     |
| 典型的使用案例      | 適用於 R 的一般 UI 資料採礦工具    |
| 如何使用並加以執行      | UI 工具。 在 Windows 上，開啟命令提示字元，執行 R，然後在 R 內執行 `rattle()`。 在 Linux 上，使用 X2Go 連接、啟動終端機、執行 R，然後在 R 內執行 `rattle()`。 |
| 範例的連結      | [Rattle](https://togaware.com/onepager/) \(英文\) |
| DSVM 上的相關工具      |LightGBM、Weka、Xgboost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 這是什麼？   |  資料採礦工作的機器學習演算法集合。 這些演算法可以直接套用至資料集，或從您自己的 JAVA 程式碼呼叫。 Weka 包含資料前置處理、分類、回歸、群集、關聯規則和視覺效果的工具。 |
| 支援的 DSVM 版本     | Windows、Linux     |
| 典型的使用案例      | 一般機器學習工具     |
| 如何使用並加以執行      | 在 Windows 上，於 [開始] 功能表中搜尋 Weka。 在 Linux 上，使用 X2Go 登入，然後移至 應用程式 > 開發 > Weka。 |
| 範例的連結      | [Weka 範例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) \(英文\) |
| DSVM 上的相關工具      |LightGBM、Rattle、Xgboost   |




