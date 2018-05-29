---
title: Azure Active Directory 應用程式 Proxy 與 Tableau | Microsoft Docs
description: 了解如何使用 Azure Active Directory (Azure AD) 應用程式 Proxy 提供對 Tableau 部署的遠端存取。  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7fa8654e413ac337994d35afdce7bd3478127f64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070867"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory 應用程式 Proxy 與 Tableau 

Azure Active Directory 應用程式 Proxy 和 Tableau 已互相合作，可確保能夠輕鬆使用「應用程式 Proxy」為 Tableau 部署提供遠端存取能力。 本文會說明如何設定此情節。  

## <a name="prerequisites"></a>先決條件 

本文中的情節假設您已完成以下事項：

- 設定 [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) (英文)。 

- 安裝[應用程式 Proxy 連接器](manage-apps/application-proxy-enable.md)。 

 

## <a name="enabling-application-proxy-for-tableau"></a>為 Tableau 啟用應用程式 Proxy 

如果想要在 Tableau 上使用應用程式 Proxy，必須傳送電子郵件至 [aadapfeedback@microsoft.com](mailto:aadapfeedback@microsoft.com)，將此情節啟用。
電子郵件內容：

-   主旨為「為 Tableau 啟用應用程式 Proxy」
-   在內文中提供您的租用戶識別碼    

您可以開始使用應用程式時，會收到確認通知。 通常需要約一週的時間。 不過您在等待期間可以先完成設定。


 

## <a name="publish-your-applications-in-azure"></a>在 Azure 中發佈應用程式 

若要發佈 Tableau，您必須在 Azure 入口網站中發佈應用程式。

關於：

- 如需步驟 1-8 的詳細，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](manage-apps/application-proxy-publish-azure-portal.md)。 
- 如需尋找應用程式 Proxy 欄位 Tableau 值的相關資訊，請參閱 Tableau 文件。  

**發佈應用程式**： 


1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。 

2. 選取 [Azure Active Directory] > [企業應用程式]。 

3. 在刀鋒視窗頂端選取 [新增]。 

4. 選取**內部部署應用程式**。 

5. 使用新應用程式的相關資訊填寫必要的欄位。 使用下列指導方針設定︰ 

    - **內部 URL**：此應用程式需含有即為真正 Tableau URL 的內部 URL。 例如： `https://adventure-works.tableau.com`。 

    - **預先驗證方法**︰Azure Active Directory (建議使用，但非必要)。 

6. 在刀鋒視窗頂端選取 [新增]。 已新增您的應用程式，快速入門功能表隨即開啟。 

7. 在 [快速啟動] 功能表中，選取 [指派測試使用者]，並將至少一個使用者新增至應用程式。 請確定此測試帳戶可存取內部部署應用程式。 

8. 選取**指派**以儲存測試使用者指派。 

9. (選擇性) 在應用程式管理頁面上，選取 [單一登入]。 在下拉式功能表中選擇 [整合式 Windows 驗證]，然後根據您的 Tableau 設定填妥必要欄位。 選取 [ **儲存**]。 

 

## <a name="testing"></a>測試 

您的應用程式現已完成測試準備。 存取您用來發佈 Tableau 的外部 URL，然後以已指派給這兩個應用程式的使用者身分登入。



## <a name="next-steps"></a>後續步驟

如需更多有關 Azure AD 應用程式 Proxy 的資訊，請參閱[如何為內部部署應用程式提供安全的遠端存取](manage-apps/application-proxy.md) (英文)。

