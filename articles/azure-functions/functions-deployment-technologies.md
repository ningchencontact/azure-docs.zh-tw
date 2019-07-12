---
title: 在 Azure Functions 中的部署技術 |Microsoft Docs
description: 了解不同的方式，您可以將程式碼部署至 Azure Functions。
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594401"
---
# <a name="deployment-technologies-in-azure-functions"></a>在 Azure Functions 中的部署技術

您可以使用幾個不同的技術，將您的 Azure Functions 專案程式碼部署至 Azure。 本文章提供這些技術的完整清單，描述的技術可供函式的類別、 說明當您使用每個方法，會發生什麼事並提供最好的方法在各種案例中使用的建議. 支援部署至 Azure Functions 的各種工具，會調整成適當的技術，根據其內容。

## <a name="deployment-technology-availability"></a>部署技術的可用性

> [!IMPORTANT]
> Azure Functions 支援跨平台的本機開發和裝載在 Windows 和 Linux 上。 目前，已提供三個主控方案：[耗用量](functions-scale.md#consumption-plan)， [Premium](functions-scale.md#premium-plan)，以及[專用 (Azure App Service)](functions-scale.md#app-service-plan)。 每個方案都有不同的行為。 並非所有的部署技術可供 Azure Functions 的每個類別。

| 部署技術 | Windows 耗用量 | Windows Premium （預覽） | 專用的 Windows  | Linux 耗用量 （預覽） | 專用的 Linux |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| 外部套件 URL<sup>1</sup> |✔|✔|✔|✔|✔|
| 部署 zip |✔|✔|✔| |✔|
| Docker 容器 | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| 原始檔控制 |✔|✔|✔| |✔|
| 本機 Git<sup>1</sup> |✔|✔|✔| |✔|
| 雲端同步處理<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| 入口網站編輯 |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup>需要的部署技術[手動觸發程序同步](#trigger-syncing)。  
<sup>2</sup>入口網站啟用編輯只針對 HTTP 和計時器觸發程序函式，在 Linux 上使用專用的方案。

## <a name="key-concepts"></a>重要概念

一些重要概念非常重要了解部署 Azure Functions 中的運作方式。

### <a name="trigger-syncing"></a>同步處理觸發程序

當您變更任何程式觸發程序時，函式基礎結構必須留意的變更。 同步處理會自動執行許多部署技術。 不過，在某些情況下，您必須手動同步您的觸發程序。 當您藉由參考外部套件 URL、 本機 Git、 雲端同步處理或 FTP 部署您的更新時，您必須以手動方式同步處理您的觸發程序。 您可以同步處理觸發程序在三種方式之一：

* 在 Azure 入口網站中重新啟動您的函式應用程式
* 傳送 HTTP POST 要求來`https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>`使用[主鍵](functions-bindings-http-webhook.md#authorization-keys)。
* 傳送 HTTP POST 要求到`https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`。 您的訂用帳戶識別碼、 資源群組名稱，與您的函式應用程式的名稱來取代預留位置。

## <a name="deployment-technology-details"></a>部署技術詳細資料 

下列的部署方法可在 Azure Functions 中。

### <a name="external-package-url"></a>外部套件 URL

您可以使用外部套件 URL 來參考遠端封裝 (.zip) 檔，其中包含您的函式應用程式。 從提供的 URL，下載檔案，並在中執行的應用程式[執行從封裝](run-functions-from-deployment-package.md)模式。

>__如何使用它：__ 新增`WEBSITE_RUN_FROM_PACKAGE`至您的應用程式設定。 此設定的值應該是 URL （您想要執行特定的封裝檔案的位置）。 您可以新增設定任一[在入口網站](functions-how-to-use-azure-function-app-settings.md#settings)或是[藉由使用 Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)。 
>
>如果您使用 Azure Blob 儲存體，使用的私用容器[共用的存取簽章 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)以便封裝函式存取。 每當應用程式重新啟動，它會擷取一份內容。 應用程式的存留期內，您的參考必須是有效的。

>__使用時機：__ 外部套件 URL 是 Azure Functions 取用方案 （預覽） 在 Linux 上執行的唯一支援的部署方法。 當您更新函數應用程式參考的套件檔案時，您必須[手動同步處理觸發程序](#trigger-syncing)告訴 Azure 您的應用程式已變更。

### <a name="zip-deploy"></a>部署 zip

使用 zip 部署至包含 Azure 函數應用程式.zip 檔案推送。 您可以選擇性地設定您的應用程式中啟動[執行從封裝](run-functions-from-deployment-package.md)模式。

>__如何使用它：__ 使用您最愛的用戶端工具部署：[VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)， [Visual Studio](functions-develop-vs.md#publish-to-azure)，或有[Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure)。 若要以手動方式將.zip 檔案部署至函式應用程式中，請依照中的指示[部署從.zip 檔案或 URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)。
>
>當您部署使用 zip 部署時，您可以設定您的應用程式中執行[執行從封裝](run-functions-from-deployment-package.md)模式。 若要設定封裝的執行模式，請設定`WEBSITE_RUN_FROM_PACKAGE`應用程式設定值設定成`1`。 我們建議 zip 部署。 它會產生更快速的載入時間，針對您的應用程式，而且 VS Code、 Visual Studio 和 Azure CLI 的預設值。

>__使用時機：__ Zip 部署在 Windows 上執行的 Azure functions 和 Azure Functions 中的專用的計劃在 Linux 上執行的是建議的部署技術。

### <a name="docker-container"></a>Docker 容器

您可以部署 Linux 容器映像，其中包含您的函式應用程式。

>__如何使用它：__ 在專用的方案中建立 Linux 函數應用程式，並指定從執行的容器映像。 您可以使用兩種方式執行此動作：
>
>* 在 Azure 入口網站在 Azure App Service 方案上建立 Linux 函數應用程式。 針對**發佈**，選取**Docker 映像**，然後設定容器。 輸入映像所在的位置。
>* 使用 Azure CLI，在 App Service 方案上建立 Linux 函數應用程式。 若要深入了解，請參閱[使用自訂映像，在 Linux 上建立的函式](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image)。
>
>若要在中使用自訂的容器，部署至現有的應用程式[Azure Functions Core Tools](functions-run-local.md)，使用[ `func deploy` ](functions-run-local.md#publish)命令。

>__使用時機：__ 使用 Docker 容器選項時您需要更充分掌控 Linux 環境函式應用程式執行。 這種部署機制是僅適用於在 Linux 上的 App Service 方案中執行的函式。

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy 封裝，並部署您的 Windows 應用程式至任何 IIS 伺服器，包括在 Azure 中的 Windows 上執行的函式應用程式。

>__如何使用它：__ 使用[適用於 Azure Functions 的 Visual Studio tools](functions-create-your-first-function-visual-studio.md)。 清除**從封裝檔案 （建議選項） 執行**核取方塊。
>
>您也可以下載[Web 部署 3.6](https://www.iis.net/downloads/microsoft/web-deploy) ，並呼叫`MSDeploy.exe`直接。

>__使用時機：__ Web 部署支援，而且不有任何問題，但的慣用的機制是[zip 部署與執行中封裝啟用](#zip-deploy)。 若要進一步了解，請參閱[Visual Studio 開發指南](functions-develop-vs.md#publish-to-azure)。

### <a name="source-control"></a>原始檔控制

您可以使用原始檔控制函式應用程式連接到 Git 存放庫。 該存放庫中的程式碼的更新會觸發部署。 如需詳細資訊，請參閱 < [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)。

>__如何使用它：__ 您可以使用部署中心在 Azure Functions 入口網站來設定從原始檔控制發行。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。

>__使用時機：__ 使用原始檔控制是在自己的函式應用程式共同作業的團隊的最佳做法。 原始檔控制是很好的部署選項，可讓更複雜的部署管線。

### <a name="local-git"></a>本機 Git

您可以使用本機 Git 推送程式碼從本機電腦到 Azure Functions 使用 Git。

>__如何使用它：__ 請依照下列中的指示[本機 Git 部署至 Azure App Service](../app-service/deploy-local-git.md)。

>__使用時機：__ 一般情況下，我們建議您最好使用不同的部署方式。 當您從本機 Git 發佈時，您必須[手動同步處理觸發程序](#trigger-syncing)。

### <a name="cloud-sync"></a>雲端同步

使用雲端同步處理您的內容從 Dropbox 與 OneDrive Azure Functions。

>__如何使用它：__ 請依照下列中的指示[從雲端資料夾同步內容](../app-service/deploy-content-sync.md)。

>__使用時機：__ 一般情況下，我們建議其他部署方法。 當您使用雲端同步發佈時，您必須[手動同步處理觸發程序](#trigger-syncing)。

### <a name="ftp"></a>FTP

您可以使用 FTP 直接將檔案傳送給 Azure Functions。

>__如何使用它：__ 請依照下列中的指示[將內容部署使用 FTP/s](../app-service/deploy-ftp.md)。

>__使用時機：__ 一般情況下，我們建議其他部署方法。 當您使用 FTP 發佈時，您必須[手動同步處理觸發程序](#trigger-syncing)。

### <a name="portal-editing"></a>入口網站編輯

在入口網站編輯器中，您可以直接編輯 （基本上在部署每次您儲存變更） 的函數應用程式中的檔案。

>__如何使用它：__ 若要能夠編輯您的函式，在 Azure 入口網站中，您必須擁有[入口網站中建立您的函式](functions-create-first-azure-function.md)。 若要保留單一真實來源，請使用任何其他部署方法唯讀模式，可讓您的函式，導致無法繼續的入口網站編輯。 若要回到的狀態，您可以在其中編輯您在 Azure 入口網站中的檔案，您可以手動開啟編輯模式回到`Read/Write`，並移除任何與部署相關的應用程式設定 (例如`WEBSITE_RUN_FROM_PACKAGE`)。 

>__使用時機：__ 此網站是開始使用 Azure Functions 的好方法。 針對更密集的開發工作中，我們建議您使用用戶端工具：
>
>* [開始使用 VS Code](functions-create-first-function-vs-code.md)
>* [開始使用 Azure Functions Core Tools](functions-run-local.md)
>* [開始使用 Visual Studio](functions-create-your-first-function-visual-studio.md)

下表顯示的作業系統和語言，該支援入口網站編輯：

| | Windows 耗用量 | Windows Premium （預覽） | 專用的 Windows | Linux 耗用量 （預覽） | 專用的 Linux |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C# 指令碼 |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (預覽) | | | | | |
| PowerShell （預覽） |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> 入口網站啟用編輯只針對 HTTP 和計時器觸發程序函式，在 Linux 上使用專用的方案。

## <a name="deployment-slots"></a>部署位置

當您將函式應用程式部署至 Azure 時，您可以部署到不同的部署位置，而不需要直接到生產環境部署。 如需有關部署位置的詳細資訊，請參閱 < [Azure App Service 位置](../app-service/deploy-staging-slots.md)。

### <a name="deployment-slots-levels-of-support"></a>部署位置支援層的級

有兩種適用於部署位置的支援層級：

* **公開上市 (GA)** :完全支援和核准用於生產環境。
* **預覽**：尚不支援，但預期未來觸達 GA 狀態。

| OS/主控方案 | 層級的支援 |
| --------------- | ------ |
| Windows 耗用量 | 預覽 |
| Windows Premium （預覽） | 預覽 |
| 專用的 Windows | 正式運作 |
| Linux 耗用量 | 不支援 |
| 專用的 Linux | 正式運作 |

## <a name="next-steps"></a>後續步驟

閱讀這些文章以深入了解部署函式應用程式： 

+ [Azure Functions 的持續部署](functions-continuous-deployment.md)
+ [使用 Azure DevOps 的持續傳遞](functions-how-to-azure-devops.md)
+ [適用於 Azure Functions 的 zip 部署](deployment-zip-push.md)
+ [執行您的 Azure Functions 從套件檔案](run-functions-from-deployment-package.md)
+ [在 Azure Functions 中函數應用程式的自動化資源部署](functions-infrastructure-as-code.md)
