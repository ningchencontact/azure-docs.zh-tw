---
title: 在 Visual Studio 中使用 Azure Logic Apps 和 Azure Functions 建置無伺服器應用程式
description: 建置、 部署及管理使用 Azure Logic Apps 和 Azure Functions Visual Studio 中的第一個無伺服器應用程式
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444860"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>使用 Azure Logic Apps 和 Azure Functions Visual Studio 中建置第一個無伺服器應用程式

您可以快速開發及部署雲端應用程式，例如在 Azure 中，使用無伺服器工具和功能[Azure Logic Apps](../logic-apps/logic-apps-overview.md)並[Azure Functions](../azure-functions/functions-overview.md)。 本文說明如何開始在 Visual Studio 中建置無伺服器應用程式，而該應用程式會使用可呼叫 Azure 函式的邏輯應用程式。 若要深入了解 Azure 中的無伺服器解決方案，請參閱[採用 Functions 和 Logic Apps 的 Azure 無伺服伺服器](../logic-apps/logic-apps-serverless-overview.md)。

## <a name="prerequisites"></a>必要條件

若要建置 Visual Studio 中的無伺服器應用程式，您需要：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 下列工具。 如果您不需要它們，請下載並安裝它們。

  * [Visual Studio 2019 2017年或 2015 （Community 或其他版本）](https://aka.ms/download-visual-studio)。 
  本快速入門使用免費的 Visual Studio Community 2017。

    > [!IMPORTANT]
    > 當您安裝 Visual Studio 2019 或 2017 時，請務必選取 **Azure 開發**工作負載。

  * [Microsoft Azure SDK for.NET (版本 2.9.1 或更新版本)](https://azure.microsoft.com/downloads/)。 
  深入了解 [Azure SDK for .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet)。

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)(英文)。

  * 您想要的 Azure Logic Apps Tools for Visual Studio 版本：

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    您可以直接從 Visual Studio Marketplace 下載並安裝 Azure Logic Apps Tools，或了解[如何從 Visual Studio 內部安裝此擴充功能](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。 
    請務必在完成安裝之後重新啟動 Visual Studio。

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)進行本機偵錯函式。

* Web 存取權時使用內嵌的邏輯應用程式設計工具。

  設計工具需要網際網路連線才能在 Azure 中建立資源，以及從邏輯應用程式中的連接器讀取屬性和資料。 
  例如，如果您使用 Dynamics CRM Online 連接器，則設計工具會檢查您的 CRM 執行個體，以取得可用的預設和自訂屬性。

## <a name="create-a-resource-group-project"></a>建立資源群組專案

若要開始進行，請為您的無伺服器應用程式建立 [Azure 資源群組專案](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。 在 Azure 中，您在中建立資源*資源群組*，這是您用於組織、 管理和部署資源當作單一資產的整個應用程式的邏輯集合。 對於 Azure 中的無伺服器應用程式，您的資源群組包含 Azure Logic Apps 和 Azure Functions 資源。 深入了解 [Azure 資源群組和資源](../azure-resource-manager/resource-group-overview.md)。

1. 啟動 Visual Studio，並使用您的 Azure 帳戶登入。

1. 在 [檔案]  功能表上，選取 [新增]   > [專案]  。

   ![在 Visual Studio 中建立新專案](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. 在 [已安裝]  之下，選取 **Visual C#** 或 **Visual Basic**。 然後，選取**雲端** > **Azure 資源群組**。

   > [!NOTE]
   > 如果**雲端**類別目錄或**Azure 資源群組**專案不存在，請確定您安裝 Azure SDK for Visual Studio。

   如果您使用 Visual Studio 2019，請遵循下列步驟：

   1. 在 [**建立新的專案**方塊中，選取**Azure 資源群組**任一視覺效果的專案範本C#或 Visual Basic，然後選取**下一步]** 。

   1. 提供的名稱和您想要使用 Azure 資源群組的其他專案資訊。 當您完成時，選取 [建立]  。

1. 為您的專案名稱和位置，然後按**確定**。

   Visual Studio 會提示您從 [範本] 清單中選取範本。 
   此範例會使用 Azure 快速入門範本，使您可以建立包含邏輯應用程式和 Azure 函式呼叫的無伺服器應用程式。

   > [!TIP]
   > 在您不想預先方案部署到 Azure 資源群組的情況下，您可以使用空白**邏輯應用程式**範本，只會建立空白邏輯應用程式。

1. 從**顯示從這個位置的範本**清單中，選取**Azure 快速入門 (github.com/Azure/azure-quickstart-templates)** 。

1. 在 [搜尋] 方塊中，輸入 「 邏輯應用程式 」 作為篩選條件。 從結果中，選取**101-logic-app-and-function-app**範本。

   ![選取 Azure 快速入門範本](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio 會為您的資源群組專案建立並開啟解決方案。 
   您選取的 Azure 快速入門範本會建立名為 azuredeploy.json 內您的資源群組專案的部署範本。 此部署範本包含簡單的邏輯應用程式，由 HTTP 要求所觸發，會呼叫 Azure 函式，並傳回結果做為 HTTP 回應的定義。

   ![新增無伺服器解決方案](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. 接下來，將您的解決方案部署至 Azure。 您可以開啟部署範本，並檢閱您的無伺服器應用程式的資源之前，您必須這麼做。

## <a name="deploy-your-solution"></a>部署您的解決方案

您可以在邏輯應用程式設計工具，在 Visual Studio 中開啟邏輯應用程式之前，您必須已在 Azure 中部署 Azure 資源群組。 設計工具可接著在您的邏輯應用程式中建立資源和服務的連線。 這項工作，請遵循下列步驟來部署至 Azure 入口網站從 Visual Studio 方案：

1. 在 [方案總管] 中，從您的資源專案的捷徑功能表，選取**Deploy** > **新增**。

   ![建立資源群組的新部署](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. 如果它們尚未選取，請選取您的 Azure 訂用帳戶和您要部署的資源群組。 然後，選取**部署**。

   ![部署設定](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. 如果**編輯參數**方塊出現時，提供要用於邏輯應用程式與您的 Azure 函式應用程式，在部署時，資源名稱，然後將儲存您的設定。 確定您的函式應用程式使用全域唯一的名稱。

   ![提供邏輯應用程式和函式應用程式的名稱](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   當 Visual Studio 開始部署至您指定的資源群組時，您的解決方案部署狀態就會出現在 Visual Studio 的 [輸出]  視窗。 
   部署完成之後，您的邏輯應用程式會在 Azure 入口網站中上線。

## <a name="edit-your-logic-app-in-visual-studio"></a>編輯您在 Visual Studio 中的邏輯應用程式

若要編輯邏輯應用程式部署之後，請藉由使用 Visual Studio 中的邏輯應用程式設計工具開啟邏輯應用程式。

1. 在 [方案總管] 中，從快顯功能表的 azuredeploy.json 檔案中，選取**開啟與邏輯應用程式設計工具**。

   ![在 邏輯應用程式設計工具中開啟 azuredeploy.json](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您有最新的更新，適用於 Visual Studio。

1. 之後**邏輯應用程式屬性**下方方塊出現**訂用帳戶**，如果尚未選取，請選取您的 Azure 訂用帳戶。 底下**資源群組**，選取 資源群組和部署您的解決方案的所在位置，然後選取**確定**。

   ![邏輯應用程式屬性](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   在 Logic Apps 設計工具開啟之後，您就可以繼續新增步驟或變更工作流程，以及儲存您的更新。

   ![在 Logic Apps 設計工具中開啟的邏輯應用程式](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>建立 Azure Functions 專案

若要使用 JavaScript、 Python、 建立您的函式專案和函式F#、 PowerShell、 批次或 Bash，請依照下列中的步驟[使用 Azure Functions Core Tools](../azure-functions/functions-run-local.md)。 若要使用開發 Azure 函式C#在您的解決方案，使用C#類別程式庫中的步驟[發佈做為函式應用程式的.NET 類別庫](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)。

## <a name="deploy-functions-from-visual-studio"></a>從 Visual Studio 部署函式

部署範本會部署您的方案，從 azuredeploy.json 檔案中的變數所指定 Git 存放庫中有任何 Azure 函式。 如果您建立並在您的方案中撰寫您的 Functions 專案時，您可以該專案簽入至 Git 原始檔控制 （例如 GitHub 或 Azure DevOps），然後更新`repo`變數，讓此範本會部署 Azure 函式。

## <a name="manage-logic-apps-and-view-run-history"></a>管理邏輯應用程式及檢視執行歷程記錄

針對已部署在 Azure 中的 logic apps，您可以仍然編輯、 管理、 檢視執行歷程記錄，並停用這些應用程式從 Visual Studio。

1. 從 Visual Studio 中的 [檢視]  功能表，開啟 [Cloud Explorer]  。

1. 底下**所有訂用帳戶**，選取您想要管理，然後選取 邏輯應用程式相關聯的 Azure 訂用帳戶**套用**。

1. 在 [Logic Apps]  下，選取您的邏輯應用程式。 從該應用程式的捷徑功能表，選取 [使用邏輯應用程式編輯器開啟]  。

   > [!TIP]
   > 如果您在 Visual Studio 2019 中沒有此命令，請檢查您有最新的更新，適用於 Visual Studio。

您現在可以將已經發佈的邏輯應用程式下載到您的資源群組專案中。 因此，雖然您可能會在 Azure 入口網站中啟動邏輯應用程式，您可以仍然匯入和管理 Visual Studio 中的該應用程式。 如需詳細資訊，請參閱[使用 Visual Studio 管理邏輯應用程式](../logic-apps/manage-logic-apps-with-visual-studio.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio 管理邏輯應用程式](manage-logic-apps-with-visual-studio.md)
