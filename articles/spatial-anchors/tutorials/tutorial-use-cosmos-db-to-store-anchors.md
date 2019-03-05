---
title: 教學課程 - 透過 Azure Spatial Anchors 和 Azure Cosmos DB 後端跨工作階段和裝置進行共用 | Microsoft Docs
description: 在本教學課程中，您將了解如何透過後端服務和 Azure Cosmos DB 在 Unity 中的裝置之間共用 Azure Spatial Anchors 識別碼。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753438"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>教學課程：透過 Azure Spatial Anchors 和 Azure Cosmos DB 後端跨工作階段和裝置進行共用

本教學課程將說明如何使用 [Azure Spatial Anchors](../overview.md) 來：

1. 在一個工作階段中建立錨點，然後在相同或不同裝置上的另一個工作階段中尋找這些錨點。 例如，在不同的日期。
2. 建立可由多個裝置同時在相同位置找到的錨點。

![持續性](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有可部署至兩個或更多裝置的應用程式。 一個執行個體所建立的 Azure Spatial Anchors，將使用 Cosmos DB 與其他執行個體共用其識別碼。

您將學習如何：

> [!div class="checklist"]
> * 的 Azure 中部署可用來共用錨點並將其儲存至 Cosmos DB 的 ASP.NET Core Web 應用程式。
> * 在快速入門中的 Unity 範例內設定 AzureSpatialAnchorsLocalSharedDemo 場景，以使用「共用錨點」Web 應用程式。
> * 部署到一或多個裝置並執行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

應注意的是，雖然您在本教學課程中將使用 Unity 和 Azure Cosmos DB，但這只為了提供範例來說明如何與其他裝置共用 Azure Spatial Anchor 識別碼。 您可以使用其他語言和後端技術來達到相同的目標。 此外，本教學課程所使用的 ASP.NET Core Web 應用程式具有對 .NET Core 2.2 SDK 的相依性。 它在一般 Azure Web Apps (適用於 Windows) 上可正常運作，但目前不適用於 Azure Web Apps for Linux。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>建立資料庫帳戶

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

請記下 `Connection String`，稍後將會用到。

## <a name="deploy-your-sharing-anchors-service"></a>部署共用錨點服務

開啟 Visual Studio，並開啟 `Sharing\SharingServiceSample` 資料夾上的專案。

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>設定服務，使其使用您的 Cosmos DB

在 [方案總管] 中，開啟 `SharingService\Startup.cs`。

找出檔案頂端的 `#define INMEMORY_DEMO` 行，並將其註解化。儲存檔案。

在 [方案總管] 中，開啟 `SharingService\appsettings.json`。

找出 `StorageConnectionString` 屬性，並將其值設為您在[建立資料庫帳戶步驟](#create-a-database-account)中記下的 `Connection String`。 儲存檔案。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Azure Cosmos DB 在各裝置間共用錨點識別碼。 若要深入了解 Azure Spatial Anchors 程式庫，請繼續參考關於如何建立和尋找錨點的指南。

> [!div class="nextstepaction"]
> [使用 Azure Spatial Anchors 建立和尋找錨點](../create-locate-anchors-overview.md)
