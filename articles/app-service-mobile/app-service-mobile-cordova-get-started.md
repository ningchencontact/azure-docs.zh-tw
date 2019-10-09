---
title: 在 Azure App Service Mobile Apps 上建立 Cordova 應用程式 | Microsoft Docs
description: 請依照此教學課程中的說明，開始使用 Azure 行動應用程式後端來進行 Apache Cordova 開發
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
tags: ''
keywords: cordova, javascript, mobile, 用戶端
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e19938256e2c0ddafe1a3746decf61c2d45db386
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025406"
---
# <a name="create-an-apache-cordova-app"></a>建立 Apache Cordova 應用程式
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊 App Center [App center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

## <a name="overview"></a>總覽
本教學課程示範如何使用 Azure 行動應用程式後端，將雲端式後端服務新增到 Apache Cordova 行動應用程式。  您會建立新的行動應用程式後端，以及可在 Azure 中儲存應用程式資料的簡易「待辦事項清單」 Apache Cordova 應用程式。

所有其他 Apache Cordova 教學課程只要是與使用 Azure App Service 中的 Mobile Apps 功能相關，都必須先完成本教學課程。

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您需要下列必要條件：

* 具有 [Visual Studio Community 2017] 或更新版本的電腦。
* [Visual Studio Tools for Apache Cordova]。
* 有效的 [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)。

您也可以略過 Visual Studio 而直接使用 Apache Cordova 命令列。  在 Mac 電腦上完成本教學課程時，使用命令列相當有用。  本教學課程未涵蓋使用命令列來編譯 Apache Cordova 用戶端應用程式。

## <a name="create-an-azure-mobile-app-backend"></a>建立 Azure 行動應用程式後端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>建立資料庫連接，並設定用戶端和伺服器專案
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>下載並執行 Apache Cordova 應用程式
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
