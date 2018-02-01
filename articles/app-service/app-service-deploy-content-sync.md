---
title: "將雲端資料夾的內容同步處理到 Azure App Service"
description: "了解如何從雲端資料夾透過內容同步處理，將您的應用程式部署至 Azure App Service。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>將雲端資料夾的內容同步處理到 Azure App Service
本教學課程將示範如何從受歡迎的雲端儲存體服務 (例如 Dropbox 與 OneDrive) 將內容同步處理至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。 

## <a name="overview"></a>內容同步處理部署概觀
隨選內容同步處理部署是由與 App Service 整合之 [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki) 所提供。 在 [Azure 入口網站](https://portal.azure.com)中，您可以在雲端儲存空間中指定特殊資料夾、在該資料夾中處理您的應用程式程式碼和內容，並按一下按鈕以同步至 App Service。 內容同步處理會在組建和部署使用 Kudu 程序。 

## <a name="contentsync"></a>如何啟用內容同步處理部署
若要從 [Azure 入口網站](https://portal.azure.com)啟用內容同步處理，請遵循下列步驟：

1. 在應用程式位於 Azure 入口網站的頁面中，按一下 [設定] > [部署來源]。 按一下 [選擇來源]，然後選取 [OneDrive] 或 [Dropbox] 作為部署來源。 
   
    ![內容同步處理](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > 由於 API 中的基礎差異，目前不支援**商務用 OneDrive**。 
   > 
   > 
2. 完成授權工作流程，以便讓 App Service 存取作為所有 App Service 內容存放區之 OneDrive 或 Dropbox 的特定預先定義指定路徑。 授權之後，App Service 平台會提供您在指定內容路徑下建立內容資料夾的選項，或是選擇此指定的內容路徑下的現有內容資料夾。 以下為針對 App Service 同步處理所使用之雲端儲存體帳戶下的指定內容路徑：  
   
   * **OneDrive**：`Apps\Azure Web Apps` 
   * **Dropbox**：`Dropbox\Apps\Azure`
3. 初始內容同步處理之後，內容同步處理可以視需要從 Azure 入口網站啟動。 [部署] 頁面上提供了部署歷程記錄。
   
    ![部署歷程記錄](./media/app-service-deploy-content-sync/onedrive_sync.png)

如需有關 Dropbox 部署的詳細資訊，請參閱 [從 Dropbox 部署] (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/) \(英文\)。

