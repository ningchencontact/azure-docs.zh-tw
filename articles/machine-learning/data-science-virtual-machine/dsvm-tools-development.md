---
title: 開發工具
titleSuffix: Azure Data Science Virtual Machine
description: 瞭解資料科學虛擬機器上可用的工具和整合式開發環境。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 62eb5f72d4b4395602b2665c0d1b3da4f6bb459b
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950189"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure 資料科學虛擬機器上的開發工具

資料科學虛擬機器（DSVM）會在高生產力的整合式開發環境（IDE）中組合數個熱門工具。 以下是 DSVM 提供的一些工具。

## <a name="visual-studio-2017"></a>Visual Studio 2017

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一般用途 IDE      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 軟體發展    |
| 它如何在 DSVM 上進行設定和安裝？      | 資料科學工作負載 (Python 和 R 工具)、Azure 工作負載 (Hadoop、Data Lake)、Node.js、SQL Server 工具、[適用於 Visual Studio Code 的 Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| 如何使用並加以執行      | 桌面快捷方式（`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`）    |
| DSVM 上的相關工具      |     Visual Studio Code、RStudio、Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一般用途 IDE      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 程式碼編輯器和 Git 整合   |
| 如何使用並加以執行      | Windows 中的桌面快捷方式（`C:\Program Files (x86)\Microsoft VS Code\Code.exe`）、在 Linux 中的桌面快捷方式或終端機（`code`）    |
| DSVM 上的相關工具      |     Visual Studio 2017、RStudio、Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 適用于 R 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用並加以執行      | Windows 上的桌面快捷方式（`C:\Program Files\RStudio\bin\rstudio.exe`），Linux 上的桌面快捷方式（`/usr/bin/rstudio`）      |
| DSVM 上的相關工具      |   Visual Studio 2017、Visual Studio Code、Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 適用于 R 語言的用戶端 IDE   |
| 這是什麼？   | 適用於 R 的網頁型 IDE    |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用並加以執行      | 使用_systemctl 啟用 rstudio-伺服器_，然後啟動具有_systemctl start rstudio-server_的服務。 然後登入位於 HTTP： \//vm-ip：8787的 RStudio 伺服器。       |
| DSVM 上的相關工具      |   Visual Studio 2017、Visual Studio Code、RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Julia 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  Julia 開發     |
| 如何使用並加以執行      | Windows 上的桌面快捷方式（`C:\JuliaPro-0.5.1.1\Juno.bat`），Linux 上的桌面快捷方式（`/opt/JuliaPro-VERSION/Juno`）      |
| DSVM 上的相關工具      |   Visual Studio 2017、Visual Studio Code、RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Python 語言的用戶端 IDE    |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      |  Python 開發     |
| 如何使用並加以執行      | Linux 上的桌面快捷方式（`/usr/bin/pycharm`）      |
| DSVM 上的相關工具      |   Visual Studio 2017、Visual Studio Code、RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 互動式資料視覺效果和 BI 工具    |
| 支援的 DSVM 版本      | Windows  |
| 典型的使用案例      |  資料視覺效果和建立儀表板   |
| 如何使用並加以執行      | 桌面快捷方式（`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`）      |
| DSVM 上的相關工具      |   Visual Studio 2017、Visual Studio Code、Juno      |

