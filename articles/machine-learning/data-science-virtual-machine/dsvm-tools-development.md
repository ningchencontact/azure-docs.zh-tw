---
title: 資料科學虛擬機器開發工具 - Azure | Microsoft Docs
description: 瞭解在資料科學虛擬機器上預先安裝的工具和整合式開發環境。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074292"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>資料科學虛擬機器的開發工具

資料科學虛擬機器 (DSVM) 會在高生產力的整合式開發環境 (IDE) 中組合數個熱門工具。 以下是 DSVM 提供的一些工具。

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一般用途 IDE      |
| 支援的 DSVM 版本      | Windows      |
| 典型的使用案例      | 軟體發展    |
| 它如何在 DSVM 上進行設定和安裝？      | 資料科學工作負載 (Python 和 R 工具)、Azure 工作負載 (Hadoop、Data Lake)、Node.js、SQL Server 工具、[適用於 Visual Studio Code 的 Azure Machine Learning](https://github.com/Microsoft/vs-tools-for-ai)    |
| 如何使用並加以執行      | 桌面快捷方式`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| DSVM 上的相關工具      |     Visual Studio Code、RStudio、Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 一般用途 IDE      |
| 支援的 DSVM 版本      | Windows、Linux     |
| 典型的使用案例      | 程式碼編輯器和 Git 整合   |
| 如何使用並加以執行      | Windows 中的`C:\Program Files (x86)\Microsoft VS Code\Code.exe`桌面快捷方式 ()、Linux 中的`code`桌面快捷方式或終端機 ()    |
| DSVM 上的相關工具      |     Visual Studio 2019、RStudio、Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 適用于 R 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用並加以執行      | Windows 上的`C:\Program Files\RStudio\bin\rstudio.exe`桌面快捷方式 ()、Linux`/usr/bin/rstudio`上的桌面快捷方式 ()      |
| DSVM 上的相關工具      |   Visual Studio 2019、Visual Studio Code、Juno      |

## <a name="rstudio--server"></a>RStudio  Server 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 適用于 R 語言的用戶端 IDE   |
| 這是什麼？   | 適用於 R 的網頁型 IDE    |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      |  R 開發     |
| 如何使用並加以執行      | 使用_systemctl 啟用 rstudio-伺服器_, 然後啟動具有_systemctl start rstudio-server_的服務。 然後登入位於 HTTP:\//your-vm-ip: 8787 的 RStudio 伺服器。       |
| DSVM 上的相關工具      |   Visual Studio 2019、Visual Studio Code、RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Julia 語言的用戶端 IDE   |
| 支援的 DSVM 版本      | Windows、Linux      |
| 典型的使用案例      |  Julia 開發     |
| 如何使用並加以執行      | Windows 上的`C:\JuliaPro-0.5.1.1\Juno.bat`桌面快捷方式 ()、Linux`/opt/JuliaPro-VERSION/Juno`上的桌面快捷方式 ()      |
| DSVM 上的相關工具      |   Visual Studio 2019、Visual Studio Code、RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 這是什麼？   | Python 語言的用戶端 IDE    |
| 支援的 DSVM 版本      | Linux      |
| 典型的使用案例      |  Python 開發     |
| 如何使用並加以執行      | Linux 上的`/usr/bin/pycharm`桌面快捷方式 ()      |
| DSVM 上的相關工具      |   Visual Studio 2019、Visual Studio Code、RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| 這是什麼？   | 互動式資料視覺效果和 BI 工具    |
| 支援的 DSVM 版本      | Windows  |
| 典型的使用案例      |  資料視覺效果和建立儀表板   |
| 如何使用並加以執行      | 桌面快捷方式`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| DSVM 上的相關工具      |   Visual Studio 2019、Visual Studio Code、Juno      |

