---
title: 教學課程 - 跨工作階段和裝置與 Azure Cosmos DB 後端共用 Azure Spatial Anchors | Microsoft Docs
description: 在本教學課程中，您將了解如何透過後端服務和 Azure Cosmos DB 在 Unity 中跨 Android/iOS 裝置共用 Azure Spatial Anchors 識別碼。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 19c2298c9bda03acba28496a97c89c2a53e3c44e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964907"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>教學課程：跨工作階段和裝置與 Azure Cosmos DB 後端共用 Azure Spatial Anchors

在本教學課程中，您將了解如何使用 [Azure Spatial Anchors](../overview.md) 在一個工作階段中建立錨點，然後在另一個工作階段中於相同或不同裝置上找到這些錨點。 例如，第二個工作階段的日期可能並不一樣。 這些相同的錨點也可由多個裝置在相同的位置同時找到。

![說明物件持續性的 GIF](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) 是一款跨平台開發人員服務，可讓您用來建立混合實境體驗，其中的物件在一段時間之後仍跨裝置保持在原位置。 當您完成時，您將會有可部署至兩個或更多裝置的應用程式。 一個執行個體所建立的空間錨點，將使用 Azure Cosmos DB 與其他執行個體共用其識別碼。

您將學習如何：

> [!div class="checklist"]
> * 在 Azure 中部署可用來共用錨點並將它們儲存至 Azure Cosmos DB 的 ASP.NET Core Web 應用程式。
> * 在 Azure 快速入門中的 Unity 範例內設定 AzureSpatialAnchorsLocalSharedDemo 場景，以使用「共用錨點」Web 應用程式。
> * 將應用程式部署到一或多個裝置並執行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

應注意的是，雖然您在本教學課程中將使用 Unity 和 Azure Cosmos DB，但這只為了提供範例，說明如何跨裝置共用 Spatial Anchors 識別碼。 您可以使用其他語言和後端技術來達到相同的目標。 此外，本教學課程所使用的 ASP.NET Core Web 應用程式需要 .NET Core 2.2 SDK。 它在「適用於 Windows 的 Web 應用程式」上可正常執行，但目前無法在「適用於 Linux 的 Web 應用程式」上執行。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

複製 `Connection String` 因為您將需要它。

## <a name="open-the-sample-project-in-unity"></a>在 Unity 中開啟範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>部署共用錨點服務

開啟 Visual Studio，然後開啟 `Sharing\SharingServiceSample` 資料夾中的專案。

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>設定服務以使用您的 Azure Cosmos DB 資料庫

在 [方案總管]  中開啟 `SharingService\Startup.cs`。

找出檔案頂端的 `#define INMEMORY_DEMO`，並將它註解化。儲存檔案。

在 [方案總管]  中開啟 `SharingService\appsettings.json`。

找出 `StorageConnectionString` 屬性，並將其值設為您在[建立資料庫帳戶步驟](#create-a-database-account)中記下的 `Connection String` 值。 儲存檔案。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure Cosmos DB 在各裝置間共用錨點識別碼。 若要深入了解如何在新的 Unity HoloLens 應用程式中使用 Azure Spatial Anchors，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [啟動新的 Android 應用程式](./tutorial-new-unity-hololens-app.md)