---
title: 驗證方法使用方式] & [insights 報告 （預覽）-Azure Active Directory
description: 報告 Azure AD 自助式密碼重設及多重要素驗證的驗證方法使用方式
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561033"
---
# <a name="authentication-methods-usage--insights-preview"></a>驗證方法使用方式] & [insights （預覽）

使用方式與深入解析可讓您了解您組織中運作的功能，例如 Azure Multi-factor Authentication 與自助式密碼重設的驗證方法。 此報告功能提供您的組織，以了解哪些方法註冊和它們的使用方式的方法。

## <a name="permissions-and-licenses"></a>使用權限與授權

使用方式和深入解析，可以存取下列角色：

- 全域管理員
- 安全性讀取者
- 安全性系統管理員
- 報告讀者

沒有額外的授權，才能存取之使用狀況和深入解析就行了。 Azure Multi-factor Authentication 與自助式密碼重設 (SSPR) 的授權資訊都位於[Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="how-it-works"></a>運作方式

若要存取驗證方法使用方式和深入解析：

1. 瀏覽至 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至**Azure Active Directory** > **密碼重設** > **使用量和 insights**。
1. 從**註冊**或是**使用量**概觀，您可以選擇開啟預先篩選來篩選的報表會根據您的需求。

![使用方式] & [insights 概觀](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

若要直接存取使用方式] & [深入解析，請前往[ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)。 此連結會帶您前往 註冊概觀。

註冊的使用者，使用者啟用，且使用者能夠圖格會顯示下列的登錄資料，為您的使用者：

- 註冊：使用者會被視為已註冊，如果使用者 （或系統管理員） 註冊了足夠的驗證方法，以符合您組織的 SSPR 或多重要素驗證原則。
- 已啟用：使用者會被視為已啟用，如果它們是在 SSPR 原則的範圍內。 如果群組已啟用 SSPR，使用者會視為已啟用，如果它們是在該群組。 如果所有使用者，已啟用 SSPR，則 （不包括來賓） 的租用戶中的所有使用者會被都視為已啟用。
- 支援：使用者會被視為支援，如果它們同時註冊並啟用。 此狀態表示他們可以 SSPR 執行在任何時候如果需要。

按一下任何一個磚或它們所示的深入解析，會帶您前往 註冊詳細資料的預先篩選清單。

**註冊**上的圖表**註冊** 索引標籤上顯示的數目成功和失敗的驗證方法註冊的驗證方法。 **重設**上的圖表**使用量** 索引標籤上顯示的成功數目和驗證失敗時的密碼重設流程的驗證方法。

按一下其中一個圖表，將會帶您前往註冊預先篩選清單，或重設事件。

您可以使用上方、 右上角的控制項，來變更顯示註冊和重設的圖表，以 24 小時、 7 天或 30 天的稽核資料的日期範圍。

註冊資料 

### <a name="registration-details"></a>註冊詳細資料

按一下**註冊的使用者**，**啟用的使用者**，或**使用者能夠**磚或深入解析會帶您前往 註冊詳細資料。

註冊詳細資料 報告顯示每個使用者的下列資訊：

- 名稱
- 使用者名稱
- 註冊狀態 （All、 註冊、 未註冊）
- 啟用狀態，啟用 (未啟用）
- 支援的狀態 (全部的功能，不支援)
- 方法 （應用程式通知、 應用程式程式碼、 電話、 簡訊、 電子郵件、 安全性問題）

使用的控制項清單的頂端，您可以搜尋使用者，並篩選顯示的資料行的使用者清單。

### <a name="reset-details"></a>重設的詳細資料

按一下 註冊或重設圖表上，將帶您前往重設的詳細資料。

重設的詳細資料報表會顯示包括過去 30 天內的註冊和重設事件：

- 名稱
- 使用者名稱
- （所有註冊、 重設） 的功能
- 應用程式通知、 應用程式程式碼、 撥打電話 （辦公室通話、 簡訊、 電子郵件、 安全性問題） 的驗證方法
- （全部，成功、 失敗） 狀態

使用的控制項清單的頂端，您可以搜尋使用者，並篩選顯示的資料行的使用者清單。

## <a name="limitations"></a>限制

這些報告中所顯示的資料將延遲最多 60 分鐘的時間。 「 上次重新整理 」 有欄位來識別您的資料是如何最近在 Azure 入口網站。

使用方式和深入解析資料不是取代 Azure Multi-factor Authentication 活動報告或 Azure AD 登入報告中包含的資訊。

## <a name="next-steps"></a>後續步驟

- [使用 驗證方法的使用方式報告 API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [選擇您的組織的驗證方法](concept-authentication-methods.md)
- [結合註冊體驗](concept-registration-mfa-sspr-combined.md)
