---
title: 如何使用 Azure Active Directory Power BI 內容套件 | Microsoft Docs
description: 了解如何使用 Azure Active Directory Power BI 內容套件
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8adcbe4245a8b8cb6e422b31b3ed23ed42f55575
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180789"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>如何使用 Azure Active Directory Power BI 內容套件

|  |
|--|
|目前，Azure AD Power BI 內容套件會使用 Azure AD Graph API 從您的 Azure AD 租用戶擷取資料。 因此，您可能會發現內容套件中的可用資料與使用 [Microsoft Graph API](concept-reporting-api.md) 擷取以用於報告的資料之間有些許差異。 |
|  |

Azure Active Directory (Azure AD) 的 Power BI 內容套件包含預先建置的報告，可協助您了解使用者採用及使用 Azure AD 功能的情形。 這可讓您使用 Power BI 的豐富視覺效果體驗，來深入了解目錄中的所有活動。 您也可以建立自己的儀表板，並與組織中的任何人共用。 

## <a name="prerequisites"></a>必要條件

您需要 Azure AD Premium (P1/P2) 授權才能使用內容套件。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。

## <a name="install-the-content-pack"></a>安裝內容套件

如需安裝 Azure AD Power BI 內容套件，請查看[快速入門](quickstart-install-power-bi-content-pack.md)。

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>此版 Azure AD 記錄內容套件中包含的報表

Azure AD Power BI 內容套件中包含下列報告。 這些報告包含**過去 30 天內**的資料。

**應用程式使用情形和趨勢報告**：此報告可讓您深入了解組織中所使用的應用程式。 您會取得最熱門應用程式的清單，或了解您最近在組織中所部署應用程式的使用情形。 這可讓您在一段時間過後追蹤及改善使用情形。

**不同位置和不同使用者的登入狀況**：此報告會提供使用 Azure 身分識別所執行的所有登入相關資料。 透過此報告，您可以深入了解個人登入狀況並回答下列問題：

- 此使用者從何處登入？
- 哪一個使用者最常登入，以及從何處登入？ 
- 登入是否成功？  
 
您也可以選取特定日期或位置，以篩選結果。

**每個應用程式的唯一使用者**：此報告可讓您檢視使用指定應用程式的所有唯一使用者。 報告中只會包含已「成功」登入應用程式的使用者。

**裝置登入**：此報告可協助您了解組織中所使用的各種裝置設定檔，並根據使用情形決定裝置原則。 報告中所提供的資料，會圍繞在 OS 類型和用來登入應用程式的瀏覽器，以及使用者的詳細資訊，包含：

- 使用者名稱
- IP 位址
- 位置 
- 登入狀態 

**SSPR 漏斗圖**：此報告可協助您了解 SSPR 工具在組織內的使用情形。 您可以檢視透過 SSPR 工具嘗試進行密碼重設的次數，以及成功重設的次數。 您也可以更深入地了解密碼重設失敗事件，並了解發生特定錯誤的原因。 

## <a name="customize-azure-ad-activity-content-pack"></a>自訂 Azure AD 的活動內容套件

**變更視覺效果**：您可以按一下 [編輯報表] 並選取您想要的視覺效果，來變更報表的視覺效果。
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/10.png) 

**包含其他欄位**：您可以透過選取要加入/移除的欄位，來將欄位新增至報表，或從報表中移除。 例如，您可以將 [登入狀態] 欄位新增至資料表檢視，如下所示。 
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/11.png) 

**在儀表板中釘選視覺效果**：您可以將自己的視覺效果新增至報告，然後將其釘選至儀表板，以自訂儀表板。 

![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/13.png) 
 
**共用儀表板**：您也可以與組織中的使用者共用儀表板。 在共用報告後，使用者便可以看到您在報告中選取的欄位。
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>排定每日重新整理 Power BI 報表

若要排定每日重新整理 Power BI 報表，請移至 [資料集] > [設定] > [排定重新整理]，並執行下列設定。
 
![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>更新至較新版本的內容套件

如果您想要將內容套件更新為較新版本：

- 下載新的內容套件，並使用本文中的指示進行設定。

- 設定為成後，請移至 [資料來源] > [設定] > [資料來源認證]，然後重新輸入您的認證。

    ![Azure Active Directory Power BI 內容套件](./media/howto-power-bi-content-pack/16.png) 

確認新版的內容套件如預期般運作後，您就可以透過刪除與舊版相關聯的基礎報表和資料庫來移除舊版。

## <a name="troubleshoot-content-pack-errors"></a>針對內容套件錯誤進行疑難排解

使用內容套件時，您可能會遇到下列錯誤： 

- [重新整理失敗](#refresh-failed) 
- [無法更新資料來源認證](#failed-to-update-data-source-credentials) 
- [匯入資料的時間太長](#data-import-is-too-slow) 

如需使用 Power BI 的一般說明，請參閱這些[說明文章](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

### <a name="refresh-failed"></a>重新整理失敗 
 
**此錯誤的呈現方式**：從 Power BI 寄送電子郵件，或是重新整理記錄中的失敗狀態。 


| 原因 | 修正方式 |
| ---   | ---        |
| 若連線到內容套件的使用者認證已重設，但卻未在內容套件的連線設定中加以更新，即會導致重新整理失敗錯誤。 | 在 Power BI 中，找出對應到 Azure AD 活動記錄儀表板 (**Azure Active Directory 活動記錄**) 的資料集、選擇 [排程重新整理]，然後輸入您的 Azure AD 認證。 |
| 重新整理可能會因為基礎內容套件中的資料問題而失敗。 | [提出支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
 
 
### <a name="failed-to-update-data-source-credentials"></a>無法更新資料來源認證 
 
**此錯誤的呈現方式**：在 Power BI 中，當您連線到 Azure AD 活動記錄內容套件時。 

| 原因 | 修正方式 |
| ---   | ---        |
| 連線的使用者既不是全域系統管理員，也不是安全性讀取者或安全性系統管理員。 | 使用非全域系統管理員或安全性讀取者或安全性系統管理員的帳戶來存取內容套件。 |
| 您的租用戶不是 Premium 租用戶，或者沒有任何具備 Premium 授權檔案的使用者。 | [提出支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
 
### <a name="data-import-is-too-slow"></a>資料匯入速度太慢 
 
**此錯誤的呈現方式**：在 Power BI 中，於連線到內容套件後，資料匯入程序就會開始準備您的儀表板以用於 Azure AD 活動記錄。 您會看見此訊息：**正在匯入資料...**，然後再也沒有任何進展。  

| 原因 | 修正方式 |
| ---   | ---        |
| 根據您的租用戶大小而定，此步驟所需的時間可能從數分鐘到 30 分鐘。 | 如果訊息未在一小時內變更以顯示您的儀表板，請[提出支援票證](../fundamentals/active-directory-troubleshooting-support-howto.md)。|
  
## <a name="next-steps"></a>後續步驟

* [安裝 Power BI 內容套件](quickstart-install-power-bi-content-pack.md)。
* [什麼是 Azure AD 報告？](overview-reports.md)
