---
title: Azure Functions 的持續部署 | Microsoft Docs
description: 您可以使用 Azure App Service 的持續部署功能來發佈您的函式。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: d4d2f24a0a7b1f01627ed2cea4a5732ca0e001c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068481"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的持續部署

Azure Functions 可讓您部署您的程式碼透過持續[原始檔控制整合](functions-deployment-technologies.md#source-control)。 這可讓程式碼適用更新觸發程序的工作流程部署至 Azure。 如果您還不熟悉 Azure Functions，快速入門[Azure Functions 概觀](functions-overview.md)。

持續部署在您要在其中整合多個是不錯的選擇，專案以及頻繁參與。 它也可讓您維護單一函式程式碼的真實來源。 您可以從下列來源的程式碼位置的 Azure Functions 中設定持續部署：

* [Azure 儲存機制](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure functions 單位是部署的函式應用程式。 這表示函式應用程式中的所有函式會部署在相同的時間。 啟用持續部署之後，在 Azure 入口網站中的函式程式碼存取已設定為*唯讀*，因為真實來源設定為其他位置。

## <a name="requirements-for-continuous-deployment"></a>連續部署的需求

連續部署才能成功，您的目錄結構必須是相容的 Azure 函式必須要有下列基本資料夾結構：

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>設定持續部署

您可以使用此程序來為現有的函數應用程式設定持續部署。 這些步驟會示範與 GitHub 存放庫整合，但類似步驟適用於 Azure 的存放庫或其他原始程式碼儲存機制。

1. 在您函式應用程式中[Azure 入口網站](https://portal.azure.com)，選取**平台功能** > **部署中心**。

    ![開啟 部署中心](./media/functions-continuous-deployment/platform-features.png)

2. 在 **部署中心**，選取**GitHub** for**原始檔控制** > **授權**。

    ![部署中心](./media/functions-continuous-deployment/github.png)

3. 選取 **授權 AzureAppService** > **繼續**。

    ![授權](./media/functions-continuous-deployment/authorize.png)

4. 選取其中一個下列的組建提供者：

    * **App Service 建置服務**-最佳在不需要在建置時，或如果您需要一般的組建。
    * **Azure 的管線 （預覽）** -當您需要更充分掌控建置最佳。 此提供者目前為預覽狀態。

    ![選取的組建提供者](./media/functions-continuous-deployment/build.png)

5. 設定原始檔控制選項，您指定的特定資訊。 針對 GitHub，您必須提供**組織**，**存放庫**，並**分支**您的程式碼所在的位置。 然後，選取 [繼續]  。

    ![將 GitHub 設定](./media/functions-continuous-deployment/github-specifics.png)

6. 最後，檢閱所有詳細資料，並選取**完成**完成您的部署組態。

    ![總結](./media/functions-continuous-deployment/summary.png)

此程序完成時，從指定的來源的所有程式碼會部署到您的應用程式中。 此時，部署來源中的變更會觸發函式應用程式在 Azure 中變更的部署。

## <a name="deployment-scenarios"></a>部署案例

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>將現有函式移至持續部署

如果您已撰寫函式[Azure 入口網站](https://portal.azure.com)而且想要下載您的應用程式的內容切換至持續部署之前，您應該瀏覽**概觀**] 索引標籤，您的函式應用程式，然後按一下 [**下載應用程式內容** 按鈕。

![下載應用程式內容](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 設定持續整合之後，您可以不會再編輯 Functions 入口網站中的原始程式檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的最佳做法](functions-best-practices.md)
