---
title: 在 Azure 入口網站中尋找 Azure Active Directory 使用者活動報告 | Microsoft Docs
description: 了解 Azure Active Directory 使用者活動報告在 Azure 入口網站中的位置。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624907"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>在 Azure 入口網站中尋找活動報告

在本文中，您將了解如何在 Azure 入口網站中尋找 Azure Active Directory (Azure AD) 使用者活動報告。

## <a name="audit-logs-report"></a>稽核記錄報告

稽核記錄報告會將應用程式活動的多個相關報告結合到單一檢視中，以取得有相關內容可參考的報告。 若要存取稽核記錄報告：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 從右上角選取您的目錄，然後從左側導覽窗格中選取 [Azure Active Directory] 刀鋒視窗。
3. 您可以從 [Azure Active Directory] 刀鋒視窗的 [活動] 區段中選取 [稽核記錄]。 

    ![稽核記錄檔](./media/howto-find-activity-reports/482.png "稽核記錄檔")

稽核記錄報告會彙總下列報告：

* 稽核報告
* 密碼重設活動
* 密碼重設註冊活動
* 自助式群組活動
* Office365 群組名稱變更
* 帳戶佈建活動
* 密碼變換狀態
* 帳戶佈建錯誤

### <a name="filtering-on-audit-logs"></a>篩選稽核記錄

您可以使用稽核報告中的進階篩選功能來存取特定類別的稽核資料，只要在 [活動類別] 篩選器中指定類別即可。 例如，若要檢視與自助式密碼重設相關的所有活動，可選擇 [自助式密碼管理] 類別。 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

活動類別包括︰

- 核心目錄
- 自助式密碼管理
- 自助式群組管理
- 帳戶佈建


## <a name="sign-ins-report"></a>登入報告 

**登入**檢視包含所有使用者登入，以及**應用程式使用量**報告。 您也可以在 [企業應用程式] 概觀的 [管理] 區段中檢視應用程式使用量資訊。

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

若要存取登入報告：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
2. 從右上角選取您的目錄，然後從左側導覽窗格中選取 [Azure Active Directory] 刀鋒視窗。
3. 您可以從 [Azure Active Directory] 刀鋒視窗的 [活動] 區段中選取 [Signins]。 

    ![登入檢視](./media/howto-find-activity-reports/483.png "登入檢視")


### <a name="filtering-on-application-name"></a>篩選應用程式名稱

您可以使用登入報告來檢視應用程式使用量的相關詳細資料，只要篩選使用者名稱或應用程式名稱即可。

![[篩選登入事件] 頁面](./media/howto-find-activity-reports/07.png "[篩選登入事件] 頁面")

## <a name="security-reports"></a>安全性報告

### <a name="anomalous-activity-reports"></a>異常活動報告

異常活動報告會提供 Azure AD 可偵測到，並以此作為報告的安全性相關風險事件資訊。

下表列出 Azure 入口網站中的 Azure AD 異常活動安全性報告，以及對應的風險事件類型。 如需詳細資訊，請參閱 [Azure Active Directory 風險事件](concept-risk-events.md)。  


| Azure AD 異常活動報告 |  Identity Protection 風險事件類型|
| :--- | :--- |
| 認證外洩的使用者 | 認證外洩 |
| 異常的登入活動 | 不可能到達非典型位置的移動 |
| 從可能受感染的裝置登入 | 從受感染的裝置登入|
| 從不明來源登入 | 從匿名 IP 位址登入 |
| 從具有可疑活動的 IP 位址登入 | 從具有可疑活動的 IP 位址登入 |
| - | 從不熟悉的位置登入 |

下列 Azure AD 異常活動安全性報告並未包含在 Azure 入口網站的風險事件中：

* 在多次失敗後登入
* 從多個地理區域登入


### <a name="detected-risk-events"></a>偵測到的風險事件

在 [Azure 入口網站](https://portal.azure.com)中，您可以在 [Azure Active Directory] 刀鋒視窗的 [安全性] 區段中，存取所偵測到的風險事件相關報告。 偵測到的風險事件會在下列報告中進行追蹤︰   

- [有風險的使用者](concept-user-at-risk.md)
- [有風險的登入](concept-risky-sign-ins.md)

    ![安全性報告](./media/howto-find-activity-reports/04.png "安全性報告")

## <a name="next-steps"></a>後續步驟

* [稽核記錄概觀](concept-audit-logs.md)
* [登入概觀](concept-sign-ins.md)
* [風險事件概觀](concept-risk-events.md)
