---
title: 機器學習 & 資料科學工具
titleSuffix: Azure Data Science Virtual Machine
description: 了解預先安裝在「資料科學虛擬機器」上的機器學習工具和架構。
keywords: 資料科學工具、資料科學虛擬機器、資料科學工具、linux 資料科學
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: d823eb615f983190ee6974ee3012f069309ca753
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195634"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Azure 資料科學上的機器學習和資料科學工具虛擬機器
「Azure 資料科學虛擬機器」具有一組以各種熱門語言提供 (例如 Python、R 及 Julia)、適用於機器學習 (ML) 的豐富工具和程式庫。 

以下是「資料科學虛擬機器」上的一些 ML 工具和程式庫。 

## <a name="azure-machine-learning-sdk-for-python"></a>適用于 Python 的 Azure Machine Learning SDK
請參閱適用于 Python 的[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)的完整參考。
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   Azure Machine Learning 是一項雲端服務，可供您用來開發及部署 ML 模型。 您可以使用 Python SDK，在進行模型建置、定型、調整規模及管理時加以追蹤。 將模型部署成容器，並在雲端、內部部署環境或 Azure IoT Edge 上執行。   |
| 支援的版本     | Windows (Conda 環境：AzureML)、Linux (Conda 環境：py36)    |
| 典型的使用案例      | 一般 ML 平台      |
| 如何設定或安裝它？      |  搭配 GPU 支援安裝   |
| 如何使用或執行它      | 作為 Python SDK 和 Azure CLI。 啟動至 Conda 環境 `AzureML` (Windows 版)「或」啟動至 `py36` (Linux 版)。      |
| 範例的連結      | 範例 Jupyter Notebook 包含在 `AzureML` 目錄中的 Notebook 底下。  |
| 相關工具      | Visual Studio Code、Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| 這是什麼？   |    XGBoost 是一個適用於 Python、R、Java、Scala、C++ 等語言的快速、可攜且分散式漸層提升 (GBDT、GBRT 或 GBM) 程式庫。 它可在單一機器、Hadoop 及 Spark 上執行。    |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般 ML 程式庫      |
| 如何設定或安裝它？      |  搭配 GPU 支援安裝   |
| 如何使用或執行它      | 作為 Python 程式庫 (2.7 和 3.5)、R 套件及路徑上命令列工具 (`C:\dsvm\tools\xgboost\bin\xgboost.exe` (適用於 Windows)、`/dsvm/tools/xgboost/xgboost` (適用於 Linux))    |
| 範例的連結      | 範例包含在 VM 的 `/dsvm/tools/xgboost/demo`(適用於 Linux) 中，以及 `C:\dsvm\tools\xgboost\demo` (適用於 Windows) 中。   |
| 相關工具      | LightGBM、MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   Vowpal Wabbit (也稱為 "VW") 是一個開放原始碼的快速核心外學習系統程式庫。    |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般 ML 程式庫      |
| 如何設定或安裝它？      |  Windows--msi 安裝程式、Linux--apt-get |
| 如何使用或執行它      | 作為路徑上命令列工具 (`C:\Program Files\VowpalWabbit\vw.exe` (在 Windows 上)、`/usr/bin/vw` (在 Linux 上))    |
| 範例的連結      | [VowPal Wabbit 範例](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) \(英文\) |
| 相關工具      |LightGBM、MXNet、XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 這是什麼？   |  Weka 是資料採礦工作的 ML 演算法集合。 您可以將這些演算法直接套用至資料集，也可以從您自己的 Java 程式碼呼叫它們。 Weka 包含資料前置處理、分類、迴歸、群集、關聯規則和視覺效果的工具。 |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 一般 ML 工具     |
| 如何使用或執行它      | 在 Windows 上，於 [開始] 功能表中搜尋 Weka。 在 Linux 上，使用 X2Go 進行登入，然後移至 [應用程式] > [開發] > [Weka]。 |
| 範例的連結      | [Weka 範例](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) \(英文\) |
| 相關工具      |LightGBM、Rattle、XGBoost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 這是什麼？   |   Rattle 是一個適用於使用 R 進行資料採礦的圖形化使用者介面。   |
| 支援的版本     | Windows、Linux     |
| 典型的使用案例      | 適用於 R 的一般 UI 資料採礦工具    |
| 如何使用或執行它      | UI 工具。 在 Windows 上，啟動命令提示字元、執行 R，然後在 R 內執行 `rattle()`。 在 Linux 上，使用 X2Go 進行連線、啟動終端機、執行 R，然後在 R 內執行 `rattle()`。 |
| 範例的連結      | [Rattle](https://togaware.com/onepager/) \(英文\) |
| 相關工具      |LightGBM、Weka、XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| 這是什麼？   | LightGBM 是一種以決策樹演算法為基礎的快速、分散式、高效能漸層提升 (GBDT、GBRT、GBM 或 MART) 架構。 它可用於排名、分類及許多其他 ML 工作。    |
| 支援的版本      | Windows、Linux    |
| 典型的使用案例      | 一般用途的漸層提升架構      |
| 如何設定或安裝它？      | 在 Windows 上，LightGBM 是以 Python 套件的形式安裝。 在 Linux 上，命令列可執行檔位於 `/opt/LightGBM/lightgbm`，R 套件和 Python 套件皆已安裝。     |
| 範例的連結      | [LightGBM 指南](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide) \(英文\)   |
| 相關工具      | MXNet、XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| 這是什麼？   | H2O 是一個開放原始碼 AI 平台，支援記憶體內部、分散式、快速及可調式 ML。  |
| 支援的版本      | Linux   |
| 典型的使用案例      | 一般用途的分散式、可調式 ML   |
| 如何設定或安裝它？      | H2O 是安裝在 `/dsvm/tools/h2o` 中。      |
| 如何使用或執行它      | 使用 X2Go 來連線至 VM。 啟動新的終端機，然後執行 `java -jar /dsvm/tools/h2o/current/h2o.jar`。 然後啟動網頁瀏覽器，並連線至 `http://localhost:54321`。      |
| 範例的連結      | VM 上 Jupyter 中的 `h2o` 目錄底下有提供範例。      |
| 相關工具      | Apache Spark、MXNet、XGBoost、Sparkling Water、Deep Water    |

「資料科學虛擬機器」上有數個其他 ML 程式庫，例如安裝在「資料科學虛擬機器」上之 Anaconda Python 發行版本隨附的熱門 `scikit-learn` 套件。 若要查看以 Python、R 及 Julia 提供的套件清單，請執行個別的套件管理員。
