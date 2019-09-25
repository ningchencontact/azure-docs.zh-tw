---
title: 包含檔案
description: 包含檔案
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 09/24/2019
ms.openlocfilehash: ccfbd16f4db770558f1bc0284860a5f8d9fb8b68
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266937"
---
> [!IMPORTANT]
> * 2019 年 5 月，新的 [Azure Active Directory]   > [應用程式註冊]  刀鋒視窗會取代舊版的 [Azure Active Directory]   > [應用程式註冊 (舊版)]  刀鋒視窗。
> * 在舊版刀鋒視窗中建立或顯示的應用程式註冊，會自動出現在新的刀鋒視窗中。
> * 如需遷移至新 Azure 應用程式註冊體驗的詳細資訊，請參閱 [Azure 應用程式註冊訓練指南](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide)和 [Azure Active Directory 快速入門](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)。

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，選取 [Azure Active Directory]   > [應用程式註冊]   > [新增註冊]  。

   [![Azure Active Directory 中的新應用程式註冊](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > 新的 [Azure Active Directory 應用程式註冊] 面板可讓您選取 [擁有的應用程式]  以篩選顯示的應用程式。

    應用程式在註冊後便會在此列出。

1. 請為應用程式提供名稱，然後選取 [僅此組織目錄中的帳戶]  來指定可存取 API 的 [支援的帳戶類型]  。 選擇要在使用者通過驗證後作為其重新導向目的地的有效 URI，然後選取 [註冊]  。

   [![在 Azure Active Directory 中建立應用程式](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 重要的 Azure Active Directory 應用程式資訊會顯示在所列出應用程式的 [概觀]  刀鋒視窗中。 在 [擁有的應用程式]  底下選取您的應用程式，然後選取 [概觀]  。

   [![複製應用程式識別碼](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   複製 [應用程式 (用戶端) 識別碼]  以便用於用戶端應用程式。

1. [驗證]  刀鋒視窗會指定重要的驗證組態設定。 

    1. [重新導向 URI]  必須符合驗證要求所提供的位址：

        * 對於裝載在本機開發環境中的應用程式，請選取 [公用用戶端 (行動和傳統型)]  。 請務必將 [預設用戶端類型]  設定為 [是]。
        * 對於裝載在 Azure App Service 上的單頁應用程式，請選取 [Web]  。

    1. 藉由勾選 [識別碼權杖]  來啟用隱含授與流程。

   [![建立新的用戶端密碼](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   按一下 [檔案]  。

1. 選取 [憑證 & 密碼]  ，接著 [新的用戶端密碼]  來建立應用程式密碼，以供用戶端用來證明其身分識別。

   [![建立新的用戶端密碼](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   接著將會顯示您的用戶端密碼。 將金鑰複製到您慣用的文字編輯器。

   > [!NOTE]
   > 您可以改用匯入憑證的功能。 為了增強安全性，建議使用憑證。 若要使用憑證，請選取 [上傳憑證]  。

1. 讓 Azure Active Directory 應用程式與 Azure 時間序列深入解析建立關聯。 選取 [API 權限]   > [新增權限]   > [組織使用的 API]  。 

    [![讓 API 與 Azure Active Directory 應用程式建立關聯](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   在搜尋列中輸入 `Azure Time Series Insights`，然後選取 `Azure Time Series Insights`。

1. 接下來，請指定應用程式所需的 API 權限類型。 根據預設，系統會醒目提示 [委派的權限]  。 選擇某個權限類型，然後選取 [新增權限]  。

    [![指定應用程式所需的 API 權限類型](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)