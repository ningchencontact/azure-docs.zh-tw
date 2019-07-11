---
title: 教學課程 - 跨工作階段和裝置來共用 Azure Spatial Anchors | Microsoft Docs
description: 在本教學課程中，您將了解如何在 Unity 中透過後端服務於 Adroid/iOS 裝置之間共用 Azure Spatial Anchors 識別碼。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 734e1d08413867a438270660fa97bb8c5737e087
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135389"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>教學課程：跨工作階段和裝置來共用 Azure Spatial Anchors

在本教學課程中，您將了解如何使用 [Azure Spatial Anchors](../overview.md) 在一個工作階段中建立錨點，然後在相同或不同裝置上找到這些錨點。 這些相同的錨點也可由多個裝置在相同的位置同時找到。

![持續性](./media/persistence.gif)

Azure Spatial Anchors 是一款跨平台開發人員服務，可讓您使用在一段時間之後仍跨裝置保持其位置的物件，建立混合實境體驗。 當您完成時，您將會有可部署至兩個或更多裝置的應用程式。 一個執行個體所建立的 Azure Spatial Anchors，將可與其他執行個體共用。

您將學習如何：

> [!div class="checklist"]
> * 在 Azure 中部署可用來共用錨點並將其儲存至記憶體一段時間的 ASP.NET Core Web 應用程式。
> * 在快速入門中的 Unity 範例內設定 AzureSpatialAnchorsLocalSharedDemo 場景，以使用「共用錨點」Web 應用程式。
> * 部署到一或多個裝置並執行。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

應注意的是，雖然您在本教學課程中將使用 Unity 和 ASP.NET Core Web 應用程式，但這只為了提供範例來說明如何與其他裝置共用 Azure Spatial Anchor 識別碼。 您可以使用其他語言和後端技術達到相同的目標。 此外，本教學課程所使用的 ASP.NET Core Web 應用程式具有對 .NET Core 2.2 SDK 的相依性。 它在一般 Azure Web Apps (適用於 Windows) 上可正常運作，但目前不適用於 Azure Web Apps for Linux。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-unity-sample-project"></a>下載 Unity 範例專案

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>部署共用錨點服務

開啟 Visual Studio，並開啟 `Sharing\SharingServiceSample` 資料夾上的專案。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Azure 中部署 ASP.NET Core Web 應用程式，然後設定並部署 Unity 應用程式。 您使用應用程式建立了 Spatial Anchors，並使用 ASP.NET Core Web 應用程式與其他裝置共用這些錨點。

若要深入了解如何改善 ASP.NET Core Web 應用程式，使它能夠使用 Azure Cosmos DB 儲存共用的 Spatial Anchors 識別碼，請繼續下一個教學課程。 Azure Cosmos DB 會為您的 ASP.NET Core Web 應用程式提供持續性。 這樣做會讓您的應用程式在今天建立一個錨點，幾天後回來時還能使用儲存在 Web 應用程式中的錨點識別碼再次找到該錨點。

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Cosmos DB 儲存錨點](./tutorial-use-cosmos-db-to-store-anchors.md)
