---
title: 設定安全性以存取和管理 Azure 時間序列深入解析預覽 | Microsoft Docs
description: 本文說明如何設定安全性和權限作為管理存取原則和資料存取原則，以保護 Azure 時間序列深入解析預覽。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 53489ef9c1264fa31b06f4660e545e6efbd1f616
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237041"
---
# <a name="grant-data-access-to-an-environment"></a>授與環境的資料存取權

本文討論兩種 Azure 時間序列深入解析預覽存取原則。

## <a name="sign-in-to-time-series-insights"></a>登入時間序列深入解析

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 找到您的時間序列深入解析環境。 在 [搜尋]  方塊中，輸入 `Time Series`。 選取搜尋結果中的 [時間序列環境]  。
1. 從清單中選取 Time Series Insights 環境。

## <a name="grant-data-access"></a>授與資料存取

依照下列步驟來授與使用者主體的資料存取權。

1. 選取 [資料存取原則]  ，然後選取 [+新增]  。

    [![資料存取一](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. 選擇 [選取使用者]  。 搜尋使用者名稱或電子郵件地址，以找出您想要新增的使用者。 按一下 [選取]  以確認選取項目。

    [![資料存取兩](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. 選擇 [選取角色]  。 為使用者選擇適當的存取角色：

    * 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]  。

    * 否則，選取 [讀取者]  ，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定]  以確認角色選擇。

    [![資料存取三](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. 在 [選取使用者角色]  頁面中選取 [確定]  。

    [![資料存取四](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. 確認 [資料存取原則]  頁面列出的使用者和每個使用者的角色。

    [![資料存取五](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>提供從另一個 AAD 租用戶的來賓存取

`Guest` 不是管理角色。 這個詞彙指的是從一個租用戶邀請到另一個租用戶的帳戶。 在邀請來賓帳戶至租用戶目錄後，就可以套用與任何其他帳戶一樣的存取控制。 您可以使用存取控制 (IAM) 刀鋒視窗，授與時間序列深入解析環境管理存取權。 或者，您可以透過 [資料存取原則] 刀鋒視窗授與環境中資料的存取權。 如需有關 Azure Active Directory (Azure AD) 租用戶來賓存取權的詳細資訊，請參閱[在 Azure 入口網站中新增 Azure Active Directory B2B 共同作業使用者](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

依照這些步驟將對「時間序列深入解析」環境的存取權授與來自另一個租用戶的 Azure AD 使用者。

1. 選取 [資料存取原則]  ，然後選取 [+ 邀請]  。

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. 輸入您要邀請之使用者的電子郵件地址。 此電子郵件地址必須與 Azure AD 相關聯。 (選擇性) 您可以在邀請中包括個人訊息。

    [![資料存取-七個](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. 尋找顯示在畫面上的確認泡泡訊息。

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. 選擇 [選取使用者]  。 搜尋您邀請之來賓使用者的電子郵件地址，以尋找您要新增的使用者。 按一下 [選取]  以確認選取項目。

    [![資料存取-九個](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. 選擇 [選取角色]  。 為來賓使用者選擇適當的存取角色：

    * 如果您要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [參與者]  。

    * 否則，選取 [讀取者]  ，允許使用者查詢環境中的資料並且在環境中儲存個人 (非共用) 查詢。

   選取 [確定]  以確認角色選擇。

    [![資料存取-十個](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. 在 [選取使用者角色]  頁面中選取 [確定]  。

1. 確認 [資料存取原則]  頁面列出的來賓使用者與每個來賓使用者的角色。

    [![資料集的存取-11](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. 現在，來賓使用者必須遵循步驟，以存取位於您將其邀請到其中之 Azure 租用戶的環境。 首先，他們接受您傳送給他們的邀請。 此邀請是透過電子郵件傳送到您在步驟 5 使用的電子郵件地址。 他們選取 [開始使用]  以接受。

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. 接著，來賓使用者接受與系統管理員組織關聯的權限。

    [![資料集的存取-13 個](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. 在來賓使用者登入您用來邀請他們的電子郵件地址，並接受邀請之後，他們將會移至 insights.azure.com。 到達該處之後，他們要選取畫面右上角其電子郵件地址旁的頭像。

    [![資料層存取-14](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. 接著，來賓使用者必須從目錄下拉式清單功能表選取您的 Azure 租用戶。 此租用戶即為您邀請他們的租用戶。

    [![15-存取資料-](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

當來賓使用者選取您的租用戶之後，他們會看到您為其提供存取權的時間序列深入解析環境。 他們現在有您提供它們與角色相關聯的所有功能**步驟 5**。

## <a name="next-steps"></a>後續步驟

* 了解[如何將 Azure 事件中樞的事件來源新增](./time-series-insights-how-to-add-an-event-source-eventhub.md)至時間序列深入解析環境。

* [將事件傳送到事件來源](./time-series-insights-send-events.md)。

* [在時間序列深入解析預覽總管中檢視您的環境](./time-series-insights-update-explorer.md)。
