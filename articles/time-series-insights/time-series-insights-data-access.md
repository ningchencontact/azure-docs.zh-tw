---
title: 設定安全性以存取和管理 Azure 時間序列深入解析預覽 | Microsoft Docs
description: 本文說明如何設定安全性和權限作為管理存取原則和資料存取原則，以保護 Azure 時間序列深入解析預覽。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: dd4c5e1652eb4dbff66591aa4bbe74e51be3e6c0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64716707"
---
# <a name="grant-data-access-to-an-environment"></a>授與環境的資料存取權

本文討論兩種 Azure 時間序列深入解析預覽存取原則。

## <a name="sign-in-to-tsi"></a>登入至 TSI

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 找到您的時間序列深入解析環境。 在 [搜尋] 方塊中，輸入 `Time Series`。 選取搜尋結果中的 [時間序列環境]。
1. 從清單中選取 Time Series Insights 環境。

## <a name="grant-data-access"></a>授與資料存取

依照下列步驟來授與使用者主體的資料存取權。

1. 選取 [資料存取原則]，然後選取 [+新增]。

    ![資料存取一][1]

1. 選擇 [選取使用者]。 搜尋使用者名稱或電子郵件地址，以找出您想要新增的使用者。 按一下 [選取] 以確認選取項目。

    ![資料存取二][2]

1. 選擇 [選取角色]。 為使用者選擇適當的存取角色：

    * 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]。

    * 否則，選取 [讀取者]，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定] 以確認角色選擇。

    ![資料存取三][3]

1. 在 [選取使用者角色] 頁面中選取 [確定]。

    ![資料存取四][4]

1. 確認 [資料存取原則] 頁面列出的使用者和每個使用者的角色。

    ![資料存取四][5]

## <a name="provide-guest-access-from-another-aad-tenant"></a>提供從另一個 AAD 租用戶的來賓存取

`Guest` 不是管理角色。 這個詞彙指的是從一個租用戶邀請到另一個租用戶的帳戶。 在邀請來賓帳戶至租用戶目錄後，就可以套用與任何其他帳戶一樣的存取控制。 您可以使用存取控制 (IAM) 刀鋒視窗，授與時間序列深入解析環境管理存取權。 或者，您可以透過 [資料存取原則] 刀鋒視窗授與環境中資料的存取權。 如需有關 Azure Active Directory (Azure AD) 租用戶來賓存取權的詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

依照這些步驟將對「時間序列深入解析」環境的存取權授與來自另一個租用戶的 Azure AD 使用者。

1. 選取 [資料存取原則]，然後選取 [+ 邀請]。

    ![資料存取六][6]

1. 輸入您要邀請之使用者的電子郵件地址。 此電子郵件地址必須與 Azure AD 相關聯。 (選擇性) 您可以在邀請中包括個人訊息。

    ![資料存取七][7]

1. 尋找顯示在畫面上的確認泡泡訊息。

    ![資料存取八][8]

1. 選擇 [選取使用者]。 搜尋您邀請之來賓使用者的電子郵件地址，以尋找您要新增的使用者。 按一下 [選取] 以確認選取項目。

    ![資料存取九][9]

1. 選擇 [選取角色]。 為來賓使用者選擇適當的存取角色：

    * 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]。

    * 否則，選取 [讀取者]，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定] 以確認角色選擇。

    ![資料存取十][10]

1. 在 [選取使用者角色] 頁面中選取 [確定]。

1. 確認 [資料存取原則] 頁面列出的來賓使用者與每個來賓使用者的角色。

    ![資料存取十一][11]

1. 現在，來賓使用者必須遵循步驟，以存取位於您將其邀請到其中之 Azure 租用戶的環境。 首先，他們接受您傳送給他們的邀請。 此邀請是透過電子郵件傳送到您在步驟 5 使用的電子郵件地址。 他們選取 [開始使用] 以接受。

    ![資料存取十二][12]

1. 接著，來賓使用者接受與系統管理員組織關聯的權限。

    ![資料存取十三][13]

1. 在來賓使用者登入您用來邀請他們的電子郵件地址，並接受邀請之後，他們將會移至 insights.azure.com。 到達該處之後，他們要選取畫面右上角其電子郵件地址旁的頭像。

    ![資料存取十四][14]

1. 接著，來賓使用者必須從目錄下拉式清單功能表選取您的 Azure 租用戶。 此租用戶即為您邀請他們的租用戶。

    ![資料存取十五][15]

當來賓使用者選取您的租用戶之後，他們會看到您為其提供存取權的時間序列深入解析環境。 他們現在有您提供它們與角色相關聯的所有功能**步驟 5**。

## <a name="next-steps"></a>後續步驟

* 了解[如何將 Azure 事件中樞的事件來源新增](./time-series-insights-how-to-add-an-event-source-eventhub.md)至時間序列深入解析環境。

* [將事件傳送到事件來源](./time-series-insights-send-events.md)。

* [在時間序列深入解析預覽總管中檢視您的環境](./time-series-insights-update-explorer.md)。

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png
