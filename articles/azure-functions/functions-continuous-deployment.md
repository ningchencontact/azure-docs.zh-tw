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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594547"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的持續部署

您可以使用持續部署您的程式碼中使用 Azure Functions[原始檔控制整合](functions-deployment-technologies.md#source-control)。 原始檔控制整合可讓程式碼更新觸發部署至 Azure 的工作流程。 如果您還不熟悉 Azure Functions，開始檢閱[Azure Functions 概觀](functions-overview.md)。

連續部署是一個不錯的選項，專案的您用來整合多個以及頻繁參與。 當您使用持續部署時，您會針對您的程式碼，可讓小組輕鬆地共同作業維護單一真實來源。 您可以從下列來源的程式碼位置的 Azure Functions 中設定持續部署：

* [Azure 儲存機制](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

在 Azure functions 單位是部署的函式應用程式。 函式應用程式中的所有函式會部署在相同的時間。 啟用持續部署之後，存取在 Azure 入口網站中的函式程式碼都會設定為*唯讀*因為真實來源設定為其他位置。

## <a name="requirements-for-continuous-deployment"></a>連續部署的需求

連續部署才能成功，您的目錄結構必須與 Azure 函式必須要有基本的資料夾結構相容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>設定持續部署

若要設定持續部署現有的函式應用程式，完成這些步驟。 步驟示範與 GitHub 存放庫整合，但類似的步驟適用於 Azure 的存放庫或其他原始程式碼儲存機制。

1. 在您函式應用程式中[Azure 入口網站](https://portal.azure.com)，選取**平台功能** > **部署中心**。

    ![開啟部署中心](./media/functions-continuous-deployment/platform-features.png)

2. 在 **部署中心**，選取**GitHub**，然後選取**授權**。 如果您已經已獲授權 GitHub，請選取**繼續**。 

    ![Azure App Service 部署中心](./media/functions-continuous-deployment/github.png)

3. 在 GitHub 中，選取**授權 AzureAppService**  按鈕。 

    ![授權 Azure App Service](./media/functions-continuous-deployment/authorize.png)
    
    在 **部署中心**在 Azure 入口網站中，選取**繼續**。

4. 選取其中一個下列的組建提供者：

    * **App Service 建置服務**:當您不需要建置，或如果您需要泛型組建的最佳。
    * **Azure 的管線 （預覽）** :當您需要更充分掌控建置最佳。 此提供者目前為預覽狀態。

    ![選取組建提供者](./media/functions-continuous-deployment/build.png)

5. 設定原始檔控制選項，您指定的特定資訊。 Github，您必須輸入或選取的值**組織**，**存放庫**，並**分支**。 值會根據您的程式碼的位置。 然後，選取 [繼續]  。

    ![設定 GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. 檢閱所有詳細資料，然後按**完成**完成您的部署組態。

    ![總結](./media/functions-continuous-deployment/summary.png)

此程序完成時，從指定的來源的所有程式碼會部署到您的應用程式中。 此時，部署來源中的變更會觸發函式應用程式在 Azure 中變更的部署。

## <a name="deployment-scenarios"></a>部署案例

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>將現有函式移至持續部署

如果您已撰寫函式[Azure 入口網站](https://portal.azure.com)和您想要下載您的應用程式的內容，然後再切換至持續部署，請移至**概觀**函式應用程式 索引標籤。 選取 [**下載應用程式內容**] 按鈕。

![下載應用程式內容](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 設定持續整合之後，您可以不會再編輯 Functions 入口網站中的原始程式檔。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的最佳做法](functions-best-practices.md)
