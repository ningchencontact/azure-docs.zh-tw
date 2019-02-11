---
標題：適用於 Machine Learning Studio 的 PowerShell 模組 titleSuffix:Azure Machine Learning Studio description:使用 PowerShell 來建立及管理 Azure Machine Learning Studio 工作區、實驗、Web 服務等。 services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-ms.author=haining, previous-author=hning86 ms.date:01/25/2019
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>適用於 Azure Machine Learning Studio 的 PowerShell 模組

使用 PowerShell 模組，您可以透過程式設計方式管理您的 Studio 資源和資產，例如工作區、資料集和 Web 服務。

您可以使用三個 Powershell 模組與 Studio 資源進行互動：

* 2018 年發行的 [Azure PowerShell Az](#az-rm) (包含 AzureRM 的所有功能)，雖然使用不同的 Cmdlet 名稱
* 2016 年發行的 [AzureRM](#az-rm)
* 2016 年發行的 [Azure Machine Learning Studio 傳統模組](#classic)

雖然這些模組有一些相似之處，但每個模組皆針對特殊情況所設計。 本文說明 PowerShell 模組之間的差異，並協助您決定要選擇哪一個模組。

## <a name="choosing-modules"></a> 選擇模組

根據您所管理的資源類型，選擇可用的 PowerShell 模組。

檢查以下[支援表格](#support-table)，以查看每個模組所支援的資源。 因為 PowerShell 傳統模組可以平行方式與 Az 或 AzureRM 一起安裝，所以您可安裝兩個模組並涵蓋所有資源類型 (傳統搭配 Az 或傳統搭配 AzureRM)

不過，不建議同時安裝 Az 和 AzureRM。 若要在 Az 與 AzureRM 之間做決定，Microsoft 建議將 Az 用於所有未來部署。 只有您的環境中有需要 AzureRm 的特殊情況，才可使用 AzureRm。

若要深入了解 Az 與 AzureRM 之間的差異，以及我們所提供的移轉路徑，請參閱 [Azure PowerShell Az 簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。

## <a name="az-rm"></a> Azure PowerShell Az 和 AzureRM

Az 和 AzureRM 兩者都會管理使用 **Azure Resource Manager** 部署模型部署的解決方案。 這些資源包括 Studio 工作區和 Studio 新型 Web 服務。 若要管理使用傳統部署模型部署的資源，您應該使用 PowerShell 傳統模組。 如果您要深入了解部署模型，請參閱 [Azure Resource Manager 與傳統部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)一文。

Az 現在是預計用來與 Azure 互動的 PowerShell 模組，而且包含 AzureRM 所有先前的功能。 AzureRM 將持續收到錯誤 (bug) 修正，但不會收到任何新的 Cmdlet 或功能。 雖有從 AzureRM 的升級路徑，但如果您在使用 Studio 時遇到 Az 相關問題，請回報問題並改回使用 AzureRM。

若要開始使用 Az，請遵循 [Azure Az 安裝指示](https://docs.microsoft.com/powershell/azure/install-az-ps)。

## <a name="classic"></a>PowerShell 傳統模組

[PowerShell 傳統模組](https://aka.ms/amlps)可讓您管理使用**傳統部署模型**部署的資源。 這些資源包括 Studio 使用者資產、傳統 Web 服務和傳統 Web 服務端點。

不過，Microsoft 建議您將 Resource Manager 部署模型使用於所有新的資源，以簡化資源的部署和管理。 如果您要深入了解部署模型，請參閱 [Azure Resource Manager 與傳統部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)一文。

若要開始使用 PowerShell 傳統模組，請從 GitHub 下載[發行套件](https://github.com/hning86/azuremlps/releases)並遵循[安裝指示](https://github.com/hning86/azuremlps/blob/master/README.md)。 指示說明如何解除封鎖已下載/解壓縮的 DLL，然後再匯入 PowerShell 環境。

## <a name="support-table"></a> PowerShell 支援表格

 **Studio 工作區** | **Az** |  **AzureRM** | **PowerShell 傳統** |
| --- | --- | --- | --- | --- |
| 建立/刪除工作區 | [Resource Manager 範本](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Resource Manager 範本](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| 管理工作區使用者 |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 管理承諾用量方案 | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Web 服務** | **Az** | **AzureRM** | **PowerShell 傳統** |
| 管理 Web 服務 | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (新型 Web 服務) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (新型 Web 服務) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (傳統 Web 服務) |
| 管理端點/金鑰 |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (新型 Web 服務) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (新型 Web 服務) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (傳統 Web 服務) |
|||
| **使用者資產** | **Az** | **AzureRM** | **PowerShell 傳統** |
| 管理資料集/定型模型 |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 管理實驗 |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 管理自訂模組 |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>後續步驟
遵循下列連結以取得完整的 PowerShell 模組文件：
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell 傳統](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
