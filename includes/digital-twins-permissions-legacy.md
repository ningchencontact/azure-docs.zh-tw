---
title: 包含檔案
description: 包含檔案
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: ef6b395aeff18a63f52f58e2477679b48a19b002
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624756"
---
1. 在 [Azure 入口網站](https://portal.azure.com)中，從左側窗格開啟 **Azure Active Directory**，然後開啟 [屬性] 窗格。 將 [目錄識別碼] 複製到暫存檔。 您會使用此值來設定下一節的應用程式範例。

    ![Azure Active Directory 目錄識別碼](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. 在 [Azure 入口網站](https://portal.azure.com)中, 從左窗格開啟 [ **Azure Active Directory** ], 然後開啟 [**應用程式註冊 (舊版)** ] 窗格。 選取 [**新增應用程式註冊**] 按鈕。

1. 在 [名稱] 方塊中為此應用程式註冊提供易記的名稱。 將 [應用程式類型] 選擇為 [原生]，然後將 [重新導向 URI] 選擇為 `https://microsoft.com`。 選取 [建立]。

    ![建立窗格](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  開啟已註冊的應用程式，並將 [應用程式識別碼] 欄位的值複製到暫存檔。 這個值可識別 Azure Active Directory 應用程式。 您會使用應用程式識別碼來設定後續章節的應用程式範例。

    ![Azure Active Directory 應用程式識別碼](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. 開啟應用程式註冊窗格。 選取 [設定] > [必要權限]，然後：

   a. 選取左上方的 [新增] 來開啟 [新增 API 存取權] 窗格。

   b. 選取 [選取 API]，並搜尋 **Azure Digital Twins**。 如果搜尋沒有找到 API，請改為搜尋 **Azure 智慧空間**。

   c. 選取 [Azure Digital Twins (Azure 智慧空間服務)] 選項，然後選擇 [選取]。

   d. 選擇 [選取權限]。 選取 [讀取/寫入權限] 委派權限核取方塊，然後選擇 [選取]。

   e. 在 [新增 API 存取權] 窗格中，選取 [完成]。

   f. 在 [**必要許可權**] 窗格中, 選取 [**授與許可權**] 按鈕, 並接受顯示的通知。 如果未授與此 API 的許可權, 請洽詢您的系統管理員。

      ![必要權限窗格](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 
