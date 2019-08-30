---
title: 使用 Azure 串流分析 CI/CD npm 套件
description: 本文說明如何使用 Azure 串流分析 CI/CD npm 套件來設定持續整合和部署程式。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173336"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>使用串流分析 CI/CD npm 套件
本文說明如何使用 Azure 串流分析 CI/CD npm 套件來設定持續整合和部署程式。

## <a name="build-the-vs-code-project"></a>建立 VS Code 專案

您可以使用**mslearn-streamanalytics-cicd** npm 套件來啟用 Azure 串流分析作業的持續整合和部署。 Npm 套件提供工具來產生[串流分析 Visual Studio Code 專案](quick-create-vs-code.md)的 Azure Resource Manager 範本。 它可以在 Windows、macOS 和 Linux 上使用, 而不需要安裝 Visual Studio Code。

[下載套件](https://www.npmjs.com/package/azure-streamanalytics-cicd)之後, 請使用下列命令來輸出 Azure Resource Manager 範本。 **ScriptPath**引數是您專案中**script.asaql**檔案的絕對路徑。 請確定 asaproj.json 和 Jobconfig.json 檔案位於與腳本檔案相同的資料夾中。 如果未指定**outputPath** , 範本會放在專案**bin**資料夾下的 [**部署**] 資料夾中。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
範例 (在 macOS 上)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

當串流分析 Visual Studio Code 專案成功建立時, 它會在**bin/[Debug/Retail]/Deploy**資料夾下產生下列兩個 Azure Resource Manager 範本檔案: 

*  Resource Manager 範本檔案

       [ProjectName].JobTemplate.json 

*  Resource Manager 參數檔案

       [ProjectName].JobTemplate.parameters.json   

Parameters. json 檔案中的預設參數是來自 Visual Studio Code 專案中的設定。 如果您想要部署到其他環境，請據以取代參數。

> [!NOTE]
> 對於所有認證，預設值都會設為 null。 部署至雲端之前，「必須」設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
深入了解如何[使用 Resource Manager 範本檔案和 Azure PowerShell 部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 深入了解如何[在 Resource Manager 範本中使用物件作為參數](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。

若要使用 Azure Data Lake Store Gen1 的受控識別作為輸出接收端，您必須在部署至 Azure 之前，使用 PowerShell 提供服務主體的存取權。 深入了解如何[使用 Resource Manager 範本部署具有受控識別的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)。
## <a name="next-steps"></a>後續步驟

* [快速入門：在 Visual Studio Code (預覽) 中建立 Azure 串流分析雲端作業](quick-create-vs-code.md)
* [使用 Visual Studio Code (預覽) 在本機測試串流分析查詢](vscode-local-run.md)
* [使用 Visual Studio Code 探索 Azure 串流分析 (預覽)](vscode-explore-jobs.md)
