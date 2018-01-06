---
title: "從一個實驗建立多個模型 | Microsoft Docs"
description: "使用 PowerShell 以相同演算法但不同的訓練資料集，建立多個機器學習服務模型和 Web 服務端點。"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1076b8eb-5a0d-4ac5-8601-8654d9be229f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: garye;haining
ms.openlocfilehash: 3386e3bab7a92b080276c4f03f0b006bd5f68e98
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>使用 PowerShell，從一個實驗中建立許多機器學習服務模型和 Web 服務端點
以下是一個常見的機器學習服務問題︰您想要建立許多模型，這些模型具有相同的訓練工作流程，且使用相同的演算法，但以不同的訓練資料集做為輸入。 本文說明如何在 Azure Machine Learning Studio 中只使用單一實驗來大規模完成這項工作。

例如，假設您擁有一家全球性自行車出租加盟商。 您想要建立迴歸模型，根據歷史資料來預測出租需求。 您可以有 1,000 以租用方式佔用位置世界各地。 您已收集到的每個位置，其中包含重要功能，例如日期、 時間、 天氣和專屬於每個位置的流量的資料集。

您可以使用所有地點的所有資料集合併版本，將模型訓練一次。 但因為每個地點有其獨特的環境，較好的作法是使用每個地點的資料集，個別地訓練您的迴歸模型。 這樣一來，每個已定型的模型無法納入考量的不同存放區大小、 磁碟區、 geography、 母體擴展、 bike 易記流量的環境中，等等。

這可能是最好的方法，但您不想在 Azure Machine Learning 中建立 1,000 個訓練實驗，各代表唯一的地點。 除了非常龐大的工作，也是似乎沒有效率，因為每個實驗會有相同的元件，除了定型資料集。

幸運的是，您可以完成這項作業使用[重新訓練應用程式開發介面的 Azure Machine Learning](retrain-models-programmatically.md)和自動化工作[Azure 機器學習 PowerShell](powershell-module.md)。

> [!NOTE]
> 若要讓您更快速執行的範例，請減少 1,000 到 10 的位置數目。 但使用與 1,000 個地點相同的原理和程序。 唯一的差別是，如果您想要從 1,000 筆資料集來訓練，建議您考慮以平行方式執行下列 PowerShell 指令碼。 相關做法已超出本文的範圍，但您可以在網際網路上找到 PowerShell 多執行緒的範例。  
> 
> 

## <a name="set-up-the-training-experiment"></a>設定訓練實驗
使用範例[定型實驗](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1)位於[Cortana 智慧組件庫](http://gallery.cortanaintelligence.com)。 在 [Azure Machine Learning Studio](https://studio.azureml.net) 工作區開啟此實驗。

> [!NOTE]
> 為了依照此範例進行，建議您使用標準工作區，而不是免費工作區。 您針對每個客戶的總計的 10 個端點-建立一個端點，並且需要標準的工作區因為免費工作區是限制為 3 的端點。 如果您只需要免費工作區，只要將變更的指令碼，以便只有 3 個位置。
> 
> 

此實驗使用「匯入資料」  模組，從 Azure 儲存體帳戶匯入訓練資料集 *customer001.csv* 。 讓我們假設您已經收集從所有自行車以租用方式佔用位置的定型資料集，並以檔案名稱的範圍從將它們儲存在相同的 blob 儲存體位置*rentalloc001.csv*至*rentalloc10.csv*。

![映像](./media/create-models-and-endpoints-with-powershell/reader-module.png)

請注意，**Web 服務輸出**模組已新增至**訓練模型**模組。
當此實驗部署為 web 服務時，端點相關聯，則輸出會傳回已定型的模型中.ilearner 檔案的格式。

另外請注意，您已設定 web 服務參數的 URL，**匯入資料**模組使用。 這可讓您使用參數來指定個別的定型資料集來定型模型的每個位置。
還有其他方式，您無法完成。 您可以使用 web 服務參數使用 SQL 查詢以取得資料從 SQL Azure 資料庫，或使用**Web 服務輸出**模組中的資料集傳送到 web 服務。

![映像](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

現在，讓我們使用預設值 *rental001.csv* 作為訓練資料集，執行此訓練實驗。 如果檢視的輸出**評估**模組 (按一下輸出並選取 **視覺化**)，您可以看到您取得的不錯效能*AUC* = 0.91。 此時，您可以部署超出本訓練實驗 web 服務。

## <a name="deploy-the-training-and-scoring-web-services"></a>部署訓練和評分 Web 服務
若要部署訓練 Web 服務，請按一下實驗畫布下方的 [設定 Web 服務] 按鈕，然後選取 [部署 Web 服務]。 將此 Web 服務命名為「自行車出租訓練」。

現在您需要部署計分的 web 服務。
若要這樣做，請按一下**設定 Web 服務**下方的畫布和選取**預測 Web 服務**。 這會建立評分實驗。
您要讓它運作為 web 服務的幾個稍微調整。 移除標籤資料行 」 cnt 」 從輸入資料，然後將輸出限制為只執行個體識別碼和對應的預測的值。

為了省去這項工作的麻煩，您可以直接開啟資源庫中已備妥的 [預測性分析](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) 。

若要部署 Web 服務，請執行預測性實驗，然後按一下畫布下方的 [部署 Web 服務]  按鈕。 將評分 Web 服務命名為「自行車出租評分」。

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>使用 PowerShell 建立 10 個相同的 Web 服務端點
此 Web 服務隨附一個預設端點。 但是因為無法更新您不為感興趣的預設端點。 您要做為建立 10 個其他的端點，一個用於每個位置。 您可以使用 PowerShell。

首先，您設定的 PowerShell 環境：

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

接著，執行下列 PowerShell 命令：

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

現在建立 10 個端點，而且它們都包含相同的定型模型定型*customer001.csv*。 您可以在 Azure 入口網站中檢視它們。

![映像](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>使用 PowerShell 更新端點來使用不同的訓練資料集
下一步是使用經過每個客戶的個別資料所特別訓練的模型來更新端點。 但首先您必須產生這些模型從**自行車以租用方式佔用訓練**web 服務。 讓我們回到 **自行車出租訓練** Web 服務。 您要呼叫其 BES 端點 10 次具有 10 個不同的訓練資料集以產生 10 個不同的模型。 我們將使用 **InovkeAmlWebServiceBESEndpoint** PowerShell Cmdlet 來執行這項操作。

您也必須在 `$configContent` 提供您 Blob 儲存體帳戶的認證，也就是 `AccountName`、`AccountKey` 和 `RelativeLocation` 欄位。 `AccountName`可以是其中一種您帳戶的名稱，如中所示**Azure 入口網站**(*儲存體* 索引標籤)。 按一下儲存體帳戶後，按下底部的 [管理存取金鑰] 按鈕，即可找到儲存體帳戶的 `AccountKey`，並複製*主要存取金鑰*。 `RelativeLocation` 是相對於您儲存體的路徑，其可儲存新模型。 例如，下列指令碼中的 `hai/retrain/bike_rental/` 路徑指向名為 `hai` 的容器，而 `/retrain/bike_rental/` 是子資料夾。 目前，您無法透過入口網站 UI 建立子資料夾，但有[數個 Azure 儲存體總管](../../storage/common/storage-explorers.md)可讓您執行這項操作。 建議您在儲存體中將建立新的容器，來儲存新的訓練模型 (.ilearner 檔案)，如下所示︰從儲存體頁面中，按一下底部的 [新增] 按鈕，並命名為 `retrain`。 簡而言之，下列指令碼的必要變更屬於 `AccountName``AccountKey` 和 `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`)。

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> BES 端點是這項作業唯一支援的模式。 RRS 無法用於產生訓練的模型。
> 
> 

您可以看到以上版本，而不是建構 10 個不同 BES 工作組態 json 檔案，您以動態方式改為建立設定字串，並摘要以*jobConfigString*參數**InvokeAmlWebServceBESEndpoint** cmdlet，因為其實不需要保留在磁碟上的複本。

如果一切順利，一段時間之後，您應該會在 Azure 儲存體帳戶中看到 10 個 .ilearner 檔案，從 *model001.ilearner* 至 *model010.ilearner*。 現在，我們已準備好使用 **Patch-AmlWebServiceEndpoint** PowerShell Cmdlet，根據這些模型來更新 10 個評分 Web 服務端點。 請記住，一次可以只修補您以程式設計的方式稍早建立的非預設端點。

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

這應該會非常快速地執行。 執行完成時，我們將成功建立 10 個預測性 Web 服務端點，每個都包含經過出租地點獨特的資料集所訓練的訓練模型，完全出自於單一訓練實驗。 若要驗證這一點，您可以使用 **InvokeAmlWebServiceRRSEndpoint** Cmdlet 嘗試呼叫這些端點，並提供相同的輸入資料，應該就會看到不同的預測結果，因為模型是以不同的訓練集來訓練。

## <a name="full-powershell-script"></a>完整 PowerShell 指令碼
以下是完整原始程式碼的清單︰

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
