---
title: 如何使用 Azure Active Directory Power BI 內容套件 | Microsoft Docs
description: 了解如何使用 Azure Active Directory Power BI 內容套件
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c0326a6b611d5f3d5633db2d2b64b8cdc15e10a7
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816679"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>如何使用 Azure Active Directory Power BI 內容套件

|  |
|--|
|目前，Azure AD Power BI 內容套件會使用 Azure AD Graph API 從您的 Azure AD 租用戶擷取資料。 因此，您可能會發現內容套件中的可用資料與使用 [Microsoft Graph API](concept-reporting-api.md) 擷取以用於報告的資料之間有些許差異。 |
|  |

IT 管理員必須了解您的使用者如何採納並使用 Azure Active Directory 功能， 以便規劃 IT 基礎結構及通訊，除了提高使用率，也能充分運用 AAD 功能。 Azure Active Directory 的 Power BI 內容套件可讓您進一步分析資料，深入了解目錄的最新狀態。 將 Azure Active Directory API 整合至 Power BI ，您可以輕鬆下載預先建立的內容套件，並使用 Power BI 提供的豐富視覺效果，深入了解 Azure Active Directory 中的所有活動。 您可以建立自己的儀表板，並輕鬆地與組織中所有人共用。 

## <a name="prerequisites"></a>必要條件

您需要 Azure AD Premium (P1/P2) 授權才能使用內容套件。 

## <a name="install-the-content-pack"></a>安裝內容套件

如需安裝 Azure AD Power BI 內容套件，請查看[快速入門](quickstart-install-power-bi-content-pack.md)。

## <a name="what-can-i-do-with-this-content-pack"></a>此內容套件有何功用？

在說明此內容套件的功用前，您可以快速預覽內容套件中的各種報表。 報表資料可回溯至 **30 天前**。

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>此版 Azure Active Directory 記錄內容套件中包含的報表

**應用程式使用量和趨勢報表**：分析您組織中使用的應用程式，以及哪些應用程式最常使用和使用時機。 您可以使用此報表來收集分析結果，了解您最近在組織中推出的應用程式使用狀況，或找出哪些應用程式較受歡迎。 透過執行這些動作，您可以在發現未使用的應用程式時，改善使用方式。

**不同位置和不同使用者的登入狀況**：分析使用 Azure 識別碼的登入狀況，以及分析使用者的識別碼。 透過此項目，您可以更深入地了解個人登入狀況並回答下列問題：

- 此使用者從何處登入？
- 哪一個使用者最常登入，以及從何處登入？ 
- 登入是否成功？  
 
您可以按一下特定日期或位置，以深入探詢詳細資料。

**每個應用程式的唯一使用者**：檢視使用指定應用程式的所有唯一使用者。 此項目僅包括「*成功*」登入應用程式的使用者。

**裝置登入**：檢視您組織中使用者使用的作業系統和瀏覽器類型，其中包含使用者的詳細資訊：

- 使用者名稱
- IP 位址
- 位置 
- 登入狀態 

透過此報表，您可以了解組織中的各種裝置設定檔，並根據使用內容決定裝置原則

**SSPR 漏斗圖**：了解在組織中如何完成密碼重設。 了解透過 SSPR 工具嘗試進行密碼重設的次數，以及成功重設的次數。 使用 SSPR 漏斗圖可更深入了解密碼重設錯誤的狀況，以及了解發生特定錯誤的原因。 此報表會提供更深入的說明，讓您了解如何在組織中使用 SSPR 工具，如此一來，您就可以做出正確決策。

## <a name="customizing-azure-ad-activity-content-pack"></a>自訂 Azure AD 的活動內容套件

**變更視覺效果**：您可以按一下 [編輯報表] 並選取您想要的視覺效果，來變更報表的視覺效果。
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/10.png) 

**包含其他欄位**：您可以透過選取要加入/移除的欄位，來將欄位新增至報表，或從報表中移除。 在下列範例中，我會將 [登入狀態] 欄位新增至資料表檢視。 
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/11.png) 

**在儀表板中釘選視覺效果**：您可以自訂儀表板，並將自己的視覺效果新增至報表，然後將其釘選至儀表板。 在下列範例中，我已新增名為「登入狀態」的篩選器，並將其包含在報表中。 我也會將橫條圖的視覺效果變更為折線圖，且新的視覺效果可釘選制儀表板中。

![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/12.png) 

![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/13.png) 
 

 


**共用儀表板**：當您建立您想要的內容之後，您可以與組織中的使用者共用儀表板。 請記住，當您共用報表時，其他人可以看到您在報表中選取的欄位。
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>排定每日重新整理 Power BI 報表

若要排定每日重新整理 Power BI 報表，請移至 [資料集 > 設定 > 排定重新整理]，並執行下列設定。
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>更新至較新版本的內容套件

如果您想要更新內容套件，以取得較新版本：

- 下載新的內容套件，並根據本文所列的指示進行設定。

- 設定為成後，請移至 [資料來源 > 設定 > 資料來源認證]，然後重新輸入您的認證，如下所示

    ![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/16.png) 

新版的內容套件開始運作時，您就可以透過刪除與舊版相關聯的基礎報表和資料庫來移除舊版。

## <a name="still-having-issues"></a>仍然有問題嗎？ 

請參閱我們的[疑難排解指南](troubleshoot-content-pack.md)。 如需使用 Power BI 的一般說明，請參閱這些[說明文章](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。
 

## <a name="next-steps"></a>後續步驟

* [安裝 Power BI 內容套件](quickstart-install-power-bi-content-pack.md)。
* [針對內容套件錯誤進行疑難排解](troubleshoot-content-pack.md)。
* [什麼是 Azure AD 報告？](overview-reports.md)
