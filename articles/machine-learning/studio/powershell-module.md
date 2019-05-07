---
title: 適用於 Machine Learning Studio 的 PowerShell 模組
titleSuffix: Azure Machine Learning Studio
description: 使用 PowerShell 來建立及管理 Azure Machine Learning Studio 工作區、實驗、Web 服務等。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: bee42f8a9582908963c0eef95a2fd04742cd425e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205671"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>適用於 Azure Machine Learning Studio 的 PowerShell 模組

使用 PowerShell 模組，您可以透過程式設計方式管理您的 Studio 資源和資產，例如工作區、資料集和 Web 服務。

您可以使用三個 Powershell 模組與 Studio 資源進行互動：

* 2018 年發行的 [Azure PowerShell Az](#az-rm) (包含 AzureRM 的所有功能)，雖然使用不同的 Cmdlet 名稱
* [AzureRM](#az-rm) 2016，取代 PowerShell Az 發行
* 2016 年發行的 [Azure Machine Learning Studio 傳統模組](#classic)

雖然這些 PowerShell 模組有一些相似之處，則會將每個專為特定案例。 本文說明 PowerShell 模組之間的差異，並協助您決定要選擇哪一個模組。  

檢查以下[支援表格](#support-table)，以查看每個模組所支援的資源。 

## <a name="az-rm"></a> Azure PowerShell Az 和 AzureRM

Az 現在是預計用來與 Azure 互動的 PowerShell 模組，而且包含 AzureRM 所有先前的功能。 AzureRM 將持續收到錯誤 (bug) 修正，但不會收到任何新的 Cmdlet 或功能。  Az 和 AzureRM 兩者都會管理使用 **Azure Resource Manager** 部署模型部署的解決方案。 這些資源包括 Studio 工作區和 Studio 「 新 」 的 web 服務。 

PowerShell 傳統可以安裝與 Az 或是 AzureRM 涵蓋這兩種 「 新 」 和 「 傳統 」 資源類型。 不過，不建議同時安裝 Az 和 AzureRM。 若要在 Az 與 AzureRM 之間做決定，Microsoft 建議將 Az 用於所有未來部署。  深入了解與 AzureRM 和中的移轉路徑的 Az[簡介 Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。

若要開始使用 Az，請遵循 [Azure Az 安裝指示](https://docs.microsoft.com/powershell/azure/install-az-ps)。

## <a name="classic"></a>PowerShell 傳統模組

[PowerShell 傳統模組](https://aka.ms/amlps)可讓您管理使用**傳統部署模型**部署的資源。 這些資源包括 Studio 使用者資產、 「 傳統 」 web 服務和 「 傳統 」 web 服務端點。

不過，Microsoft 建議您使用 Resource Manager 部署模型，針對所有未來的資源，來簡化的部署和管理資源。 如果您要深入了解部署模型，請參閱 [Azure Resource Manager 與傳統部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)一文。

若要開始使用 PowerShell 傳統模組，請從 GitHub 下載[發行套件](https://github.com/hning86/azuremlps/releases)並遵循[安裝指示](https://github.com/hning86/azuremlps/blob/master/README.md)。 指示說明如何解除封鎖已下載/解壓縮的 DLL，然後再匯入 PowerShell 環境。

PowerShell 傳統可以安裝與 Az 或是 AzureRM 涵蓋這兩種 「 新 」 和 「 傳統 」 資源類型。

## <a name="support-table"></a> PowerShell 支援表格


| | **Az** |  **PowerShell 傳統** |
| --- | --- | --- |
| 建立/刪除工作區 | [Resource Manager 範本](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| 管理工作區的承諾用量方案 | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| 管理工作區使用者 |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 管理 Web 服務 | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>(「 new 」 web 服務)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(「 傳統 」 web 服務) |
| 管理 web 服務端點/索引鍵 |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| 管理使用者的資料集/定型模型| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 管理使用者實驗 |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 管理自訂模組 | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>後續步驟
這些 PowerShell 模組，請參閱完整的文件：
* [PowerShell 傳統](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
