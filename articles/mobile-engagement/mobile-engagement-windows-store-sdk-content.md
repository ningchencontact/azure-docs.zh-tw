---
title: Windows 通用 app SDK 內容
description: 了解適用於 Azure Mobile Engagement 的 Windows 通用 app SDK 的內容
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Windows 通用 app SDK 內容
> [!IMPORTANT]
> Azure Mobile Engagement 將於 2018 年 3 月 31 日停止服務。 此頁面將於不久之後刪除。
> 

本文件列出及說明 SDK 在應用程式中部署的內容。

## <a name="the-resources-folder"></a>`/Resources` 資料夾
此資料夾包含 Mobile Engagement 需要的所有資源。 您也可以自訂它們，以符合您的應用程式。

* `EngagementConfiguration.xml` ：Mobile Engagement 的組態檔，您可以在此自訂 Mobile Engagement 的設定 (Mobile Engagement 連接字串、報告當機...)。

### <a name="html-folder"></a>/html 資料夾
* `EngagementNotification.html`：應用程式內橫幅的 `Notification` Web 檢視 HTML 設計。
* `EngagementAnnouncement.html`：應用程式內橫幅的 `Announcement` Web 檢視 HTML 設計。

### <a name="images-folder"></a>/images 資料夾
* `EngagementIconNotification.png`：顯示在通知左側的品牌圖示，由您的品牌圖示取代此圖示。
* `EngagementIconOk.png`：動作或驗證按鈕之 Reach 內容頁面的 `Ok` 圖示。
* `EngagementIconNOK.png`：Reach 內容頁面之驗證按鈕停用時的 `NOK` 圖示。
* `EngagementIconClose.png`：隱藏按鈕之 Reach 通知和內容的 `Close` 圖示。

### <a name="overlay-folder"></a>/overlay 資料夾
* `EngagementPageOverlay.cs` ：負責將 Engagement Reach 應用程式內 UI 加入子系的重疊頁面。

