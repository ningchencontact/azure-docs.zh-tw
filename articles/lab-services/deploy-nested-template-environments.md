---
title: 部署在 Azure DevTest Labs 中的巢狀的 Resource Manager 範本環境 |Microsoft Docs
description: 了解如何部署巢狀的 Azure Resource Manager 範本，以提供使用 Azure DevTest Labs 的環境。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835281"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>用於測試環境的巢狀的 Azure Resource Manager 範本部署
巢狀的部署可讓您執行的主要 Resource Manager 範本內的 從其他 Azure Resource Manager 範本。 它可讓您部署分解成一組目標和特定目的的範本。 提供測試、 重複使用和可讀性方面的優點。 發行項[部署 Azure 資源時使用連結的範本](../azure-resource-manager/resource-group-linked-templates.md)利用數個程式碼範例提供此解決方案的概觀。 這篇文章提供 Azure DevTest Labs 的特定範例。 

## <a name="key-parameters"></a>索引鍵參數
雖然您可以從頭開始建立您自己的 Resource Manager 範本，我們建議您使用[Azure 資源群組專案](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)在 Visual Studio 中，這可讓您輕鬆地開發及偵錯範本。 當您新增到 azuredeploy.json 的巢狀的部署資源時，Visual Studio 會新增數個項目，讓範本更有彈性。 這些項目包括子資料夾中，使用次要的範本和參數檔案，在主要範本檔案中，變數名稱和新檔案的儲存位置的兩個參數。 **_ArtifactsLocation**並 **_artifactsLocationSasToken**是 DevTest Labs 使用的索引鍵參數。 

如果您不熟悉 DevTest 實驗室環境的運作方式，請參閱[使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。 您的範本會儲存在連結至 DevTest Labs 中實驗室的儲存機制。 當您建立新的環境使用這些範本時，檔案會移到實驗室中的 Azure 儲存體容器中。 若要能夠找出並複製巢狀的檔案，DevTest Labs _artifactsLocation 和 _artifactsLocationSasToken 參數識別，並複製到儲存體容器的子資料夾。 然後，它會自動插入的位置和共用存取簽章 (SaS) 權杖的參數。 

## <a name="nested-deployment-example"></a>巢狀的部署範例
以下是巢狀部署的簡單範例：

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

包含此範本儲存機制中的資料夾包含一個子資料夾`nestedtemplates`的檔案**NestOne.json**並**NestOne.parameters.json**。 在 [ **azuredeploy.json**，URI 範本所建立的構件位置、 巢狀的範本] 資料夾中，使用巢狀範本檔案名稱。 同樣地，參數的 URI 是使用巢狀範本的成品位置、 巢狀的範本資料夾，以及參數檔案建置的。 

以下是相同的專案結構，在 Visual Studio 中的映像： 

![Visual Studio 中的專案結構](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

在主資料夾，但不是比單一層級深度，您可以新增其他資料夾。 

## <a name="next-steps"></a>後續步驟
請參閱下列文章中有關環境的詳細資料： 

- [透過 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定，並在 Azure DevTest Labs 中使用公用環境](devtest-lab-configure-use-public-environments.md)
- [在 Azure DevTest Labs 的實驗室的虛擬網路連線的環境](connect-environment-lab-virtual-network.md)