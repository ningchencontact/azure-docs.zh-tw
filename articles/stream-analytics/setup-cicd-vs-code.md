---
title: 持續整合及開發與 Azure Stream Analytics CI/CD npm 套件
description: 本文說明如何使用 Azure Stream Analytics CI/CD npm 封裝來設定持續整合和部署程序。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158513"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>持續整合及開發與 Stream Analytics CI/CD npm 套件
本文說明如何使用 Azure Stream Analytics CI/CD npm 套件來設定持續整合和部署程序。

## <a name="build-the-vs-code-project"></a>建置 VS 程式碼專案

您可以啟用持續整合和部署使用 Azure Stream Analytics 作業**asa-streamanalytics-cicd** npm 套件。 Npm 套件會提供工具來產生的 Azure Resource Manager 範本[Stream Analytics Visual Studio 程式碼專案](quick-create-vs-code.md)。 它可以用在 Windows、 macOS 和 Linux 上而不需要安裝 Visual Studio Code。

一旦[下載套件](https://www.npmjs.com/package/azure-streamanalytics-cicd)，使用下列命令輸出的 Azure Resource Manager 範本。 **ScriptPath**引數是絕對路徑**asaql**專案中的檔案。 請確定 asaproj.json 和 JobConfig.json 檔案位於相同的資料夾與指令碼檔案。 如果**outputPath**未指定，範本將會置於**部署**下的專案資料夾**bin**資料夾。

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
（在 macOS) 上的範例
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

當 Stream Analytics Visual Studio 程式碼專案建置成功時，它會產生下列兩個 Azure Resource Manager 範本檔案底下**bin / [Debug/Retail] /deploy**資料夾： 

*  Resource Manager 範本檔案

       [ProjectName].JobTemplate.json 

*  Resource Manager 參數檔案

       [ProjectName].JobTemplate.parameters.json   

Parameters.json 檔案中的預設參數會從您的 Visual Studio 程式碼專案中的設定。 如果您想要部署到其他環境，請據以取代參數。

> [!NOTE]
> 對於所有認證，預設值都會設為 null。 部署至雲端之前，「必須」  設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
深入了解如何[使用 Resource Manager 範本檔案和 Azure PowerShell 部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)。 深入了解如何[在 Resource Manager 範本中使用物件作為參數](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。

若要使用 Azure Data Lake Store Gen1 的受控識別作為輸出接收端，您必須在部署至 Azure 之前，使用 PowerShell 提供服務主體的存取權。 深入了解如何[使用 Resource Manager 範本部署具有受控識別的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)。
## <a name="next-steps"></a>後續步驟

* [快速入門：在 Visual Studio Code （預覽） 建立 Azure Stream Analytics 雲端作業](quick-create-vs-code.md)
* [測試 Stream Analytics 查詢，在本機使用 Visual Studio Code （預覽）](vscode-local-run.md)
* [探索 Azure Stream Analytics 與 Visual Studio Code （預覽）](vscode-explore-jobs.md)
