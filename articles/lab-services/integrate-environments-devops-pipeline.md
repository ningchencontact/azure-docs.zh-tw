---
title: 將環境整合到 Azure DevTest Labs 中的 Azure 管線 |Microsoft Docs
description: 了解如何將 Azure DevTest Labs 環境整合到您的 Azure DevOps 持續整合 (CI) 和持續傳遞 (CD) 管線。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357400"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>將環境整合到您的 Azure DevOps 的 CI/CD 管線
您可以使用 Azure DevTest Labs 工作擴充功能安裝於 Azure DevOps 服務 （前身為 Visual Studio Team Services） 中輕鬆整合您的持續整合 (CI) / 持續傳遞 (CD) 建置和發行管線與 Azure研發/測試實驗室。 這些擴充功能進行快速部署的工作變得更容易[環境](devtest-lab-test-env.md)特定測試工作，並在測試完成後加以刪除。 

本文說明如何建立和部署環境，然後刪除環境，全都放在一個完整的管線。 您通常會在您自己自訂的建置、 測試與部署管線來個別每個工作執行。 這篇文章中所使用的擴充功能是除此之外[建立/刪除 DTL VM 工作](devtest-lab-integrate-ci-cd-vsts.md):

- 建立環境
- 刪除環境

## <a name="before-you-begin"></a>開始之前
您可以整合 Azure DevTest Labs 中的 CI/CD 管線之前，先安裝[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)從 Visual Studio Marketplace 的延伸模組。 

## <a name="create-and-configure-the-lab-for-environments"></a>建立及設定實驗室環境
本節說明如何建立和設定將 Azure 環境部署至實驗室。

1. [建立實驗室](devtest-lab-create-lab.md)如果您還沒有其中一個。 
2. 設定實驗室，並依照這篇文章中的指示建立的環境範本：[使用 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)。
3. 此範例中，使用現有的 Azure 快速入門範本[ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)。
4. 複製**201-web-app-redis-cache-sql-database**資料夾**ArmTemplate**步驟 2 中設定存放庫中的資料夾。

## <a name="create-a-release-definition"></a>建立發行定義
若要建立發行定義，請執行下列動作：

1.  在上**版本**索引標籤**建置及發行中樞**，選取**加號 （+）**  按鈕。
2.  在 [建立發行定義] 視窗中，選取 [空白] 範本，然後選取 [下一步]。
3.  選取 [稍後選擇]，然後選取 [建立]，以使用一個預設的環境、但不使用任何連結的構件來建立新的發行定義。
4.  若要開啟快顯功能表中，新的發行定義中，選取 [**省略符號 （...）** 環境的名稱下, 一步]，然後選取**設定變數**。
5.  在 [設定 - 環境] 視窗中，為您在發行定義工作中使用的變數輸入下列值：
1.  針對**administratorLogin**，輸入 SQL 系統管理員登入名稱。
2.  針對**administratorLoginPassword**，輸入密碼以供 SQL 系統管理員登入。 使用「掛鎖」圖示可隱藏及保護密碼。
3.  針對**databaseName**，輸入 SQL Database 名稱。
4.  這些變數都有專用的範例環境，不同的環境可能有不同的變數。

## <a name="create-an-environment"></a>建立環境
部署的下一個階段是建立環境，以用於開發或測試之用。

1. 在發行定義中，選取 [新增工作]。
2. 在 **任務**索引標籤上，加入 Azure DevTest Labs 建立環境的工作。 請依照下列方式設定工作：
    1. 針對 [Azure RM 訂用帳戶]，選取 [可用的 Azure 服務連線] 清單中的連線，或對您的 Azure 訂用帳戶建立權限更具限制性的連線。 如需詳細資訊，請參閱 [Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints)。
2. 針對**實驗室名稱**，選取您稍早建立的執行個體的名稱 *。
3. 針對**存放庫名稱**，選取 Resource Manager 範本 (201) 已推送至存放庫 *。
4. 針對**範本名稱**，選取 儲存到您來源的程式碼存放庫 * 環境的名稱。 
5. **實驗室名稱**，**存放庫名稱**，並**範本名稱**是易記的 Azure 資源識別碼。 手動輸入的易記名稱將會導致失敗，請使用下拉式清單選取的資訊。
6. 針對**環境名稱**，輸入名稱來唯一識別實驗室環境執行個體。  它必須是實驗室中的唯一項目。
7. **參數檔案**並**參數**，允許自訂參數傳遞至環境。 其中之一或兩者，都可以用來設定參數值。 此範例中，將使用的參數區段。 請使用您在環境中定義的變數名稱，例如： `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. 範本的輸出區段中，可以透過傳遞環境範本內的資訊。 請檢查**建立根據環境範本輸出的輸出變數**讓其他工作可以使用的資料。 `$(Reference name.Output Name)` 是要遵循的模式。 比方說，如果參考名稱為 DTL 而範本中的輸出名稱為位置的變數會是`$(DTL.location)`。

## <a name="delete-the-environment"></a>刪除環境
最後一個階段是刪除您在您的 Azure DevTest Labs 執行個體中部署的環境。 您通常會在之後執行的開發工作，或在已部署的資源上執行的測試，您需要刪除環境。

在發行定義中，選取**將工作加入**，然後在**部署**索引標籤上，新增**Azure DevTest Labs 刪除環境**工作。 進行下列設定：

1. 若要刪除 VM，請參閱[Azure DevTest Labs 工作](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. 針對 [Azure RM 訂用帳戶]，選取 [可用的 Azure 服務連線] 清單中的連線，或對您的 Azure 訂用帳戶建立權限更具限制性的連線。 如需詳細資訊，請參閱 [Azure Resource Manager 服務端點](/azure/devops/pipelines/library/service-endpoints)。
    2. 針對**實驗室名稱**，選取的實驗室所在的環境。
    3. 針對**環境名稱**，輸入要移除環境的名稱。
2. 輸入發行定義的名稱，並加以儲存。

## <a name="next-steps"></a>後續步驟
請參閱下列文章： 
- [使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
- 從 DevTest Labs 自動化的快速入門 Resource Manager 範本[DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)。
- [VSTS 疑難排解頁面](/azure/devops/pipelines/troubleshooting)

