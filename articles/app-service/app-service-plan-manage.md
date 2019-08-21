---
title: 管理 App Service 方案 - Azure | Microsoft Docs
description: 了解如何執行不同的工作來管理 App Service 方案。
keywords: App Service, Azure App Service, 級別, App Service方案, 變更, 建立, 管理, 管控
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 77d5e4ace14fb0071b6e01a01edbad0128382303
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639726"
---
# <a name="manage-an-app-service-plan-in-azure"></a>在 Azure 建立管理 App Service 方案

[Azure App Service 方案](overview-hosting-plans.md)提供 App Service 應用程式執行所需的資源。 本指南說明如何管理 App Service 方案。

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案

> [!TIP]
> 如果您有 App Service 環境，請參閱[在 App Service 環境中建立 App Service 方案](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

您可建立空白的 App Service 方案，或是在應用程式建立期間建立方案。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [新增] > [Web + 行動]，然後選取 [Web 應用程式] 或其他 App Service 應用程式種類。

2. 選取現有的 App Service 方案或為新應用程式建立方案。

   ![在 Azure 入口網站中建立應用程式。][createWebApp]

   若要建立方案：

   a. 選取[[+] 建立新項目]。

      ![建立 App Service 方案。][createASP] 

   b. 針對 [App Service 方案]，輸入方案的名稱。

   c. 針對 [位置]，選取適當的位置。

   d. 針對 [定價層]，為服務選取適當的定價層。 選取 [檢視全部] 以檢視其他價格選項，例如 [免費] 和 [共用]。 選取定價層後，請按一下 [選取] 按鈕。

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>將應用程式移到不同的 App Service 方案

您也可以將應用程式移到另一個 App Service 方案，只要來源方案和目標方案位於「相同的資源群組和地理區域」即可。

> [!NOTE]
> Azure 會將每個新的 App Service 方案部署到部署單位 (在內部稱為網路空間)。 每個區域可以有許多網路空間，但您的應用程式只能在相同網路空間中建立的方案之間移動。 App Service 環境是隔離的網路空間，因此可以在相同 App Service Environment 中的方案之間移動應用程式，但無法在不同 App Service 環境中的方案之間移動。
>
> 您無法在建立方案時指定您想要的網路空間，但這可確保方案建立於與現有方案相同的網路空間中。 簡單地說，所有使用相同資源群組和區域組合建立的方案都會部署到相同的網路空間中。 比方說，如果您在資源群組 A 和區域 B 中建立方案，則您後續在資源群組 A 和區域 B 中建立的方案都會部署到相同網路空間中。 請注意，方案建立之後便不能移動網路空間，所以您無法藉由將方案移至另一個資源群組，將其移到與另一個方案「相同的網路空間」中。
> 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您要移動的應用程式。

1. 在功能表中，尋找 [App Service 方案] 區段。

1. 選取 [變更 App Service 方案] 以開啟 [App Service 方案] 選取器。

   ![App Service 方案選取器。][change] 

1. 在 [App Service 方案] 選取器中，選取現有的方案以移動此應用程式。   

[選取 App Service 方案] 頁面只會顯示與目前應用程式的 App Service 方案位於相同資源群組和地理區域的方案。

每個方案都有其專屬定價層。 例如，如果將網站從**免費**層移至**標準**層，則所有指派給它的應用程式都可以使用**標準**層的功能和資源。 不過，將應用程式從較高層的方案移至較低層的方案，表示您無法再存取特定功能。 如果您的應用程式使用目標方案未提供的功能，您會收到顯示哪些使用中功能無法使用的錯誤。 

例如，如果您其中一個應用程式使用 SSL 憑證，您可能會看到此錯誤訊息：

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

在此情況下，在您將應用程式移至目標方案之前，必須執行下列其中一個動作：
- 將目標方案的定價層規模調升為**基本**或更高。
- 移除應用程式的所有 SSL 連線。

## <a name="move-an-app-to-a-different-region"></a>將應用程式移到不同的區域

您的應用程式執行所在的區域，就是 App Service 方案所在的區域。 不過，您無法變更 App Service 方案區域。 如果您想在不同的區域執行應用程式，有一個替代方法是複製應用程式。 複製會在任何區域中的新或現有 App Service 方案中產生您應用程式的複本。

您可以在功能表的 [開發工具] 區段中找到 [複製應用程式]。

> [!IMPORTANT]
> 複製有一些限制。 您可在 [Azure App Service 應用程式複製](app-service-web-app-cloning.md)中閱讀相關限制。

## <a name="scale-an-app-service-plan"></a>調整 App Service 方案

若要相應增加 App Service 方案的定價層，請參閱[在 Azure 中相應增加應用程式](manage-scale-up.md)。

若要相應縮小方案的執行個體計數，請參閱[手動或自動調整執行個體計數](../monitoring-and-diagnostics/insights-how-to-scale.md)。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>刪除 App Service 方案

為避免非預期費用，當您刪除 App Service 方案中的最後一個應用程式時，App Service 預設也會刪除此方案。 如果您選擇改為保留方案，您應該將方案變更為**免費**層，以避免產生費用。

> [!IMPORTANT]
> 沒有相關聯應用程式的 App Service 方案仍會產生費用，因為它們會繼續保留已設定的 VM 執行個體。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Azure 中相應增加應用程式的規模](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
