---
title: 如何調查 Azure Active Directory identity protection 中的風險 (已重新整理) |Microsoft Docs
description: 瞭解如何在 Azure Active Directory identity protection (已重新整理) 中調查有風險的使用者、偵測和登入。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129714"
---
# <a name="how-to-investigate-risk"></a>如何：調查風險

使用有風險的登入、有風險的使用者和風險偵測報告, 您可以調查並深入瞭解環境中的風險。 有了篩選和排序有風險的登入、使用者和偵測的功能, 您可以更瞭解組織中可能的入侵。 

## <a name="risky-users-report"></a>具風險使用者報告

使用具風險使用者報告提供的資訊，您可以找到下列問題的解答：

- 哪些使用者有高風險？
- 哪些使用者具有補救的風險狀態？

此報告的第一個進入點是安全性頁面上的 [調查] 區段。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/01.png)

具風險的使用者報告有預設檢視，顯示：

- Name
- 風險狀態
- 風險層級
- 風險詳細資料
- 上次更新的風險
- Type
- 狀態

![具風險使用者報告](./media/howto-investigate-risky-users-signins/03.png)

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/04.png)

此資料行對話方塊可讓您顯示其他欄位，或移除已顯示的欄位。

按一下清單檢視中的項目，即可取得所有可用的詳細資料 (水平檢視)。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/05.png)

詳細資料檢視會顯示：

- 基本資訊
- 最近的具風險登入
- 未連結至登入的風險偵測
- 風險歷程記錄

此外，您可以：

![具風險使用者報告](./media/howto-investigate-risky-users-signins/08.png)

- 檢視所有的登入捷徑，以檢視該使用者的登入報告。
- 檢視所有具風險登入，以檢視已標記為具風險使用者的所有登入。
- 如果您認為使用者的身分識別已遭入侵，請重設使用者密碼。
- 如果您認為使用者的作用中風險偵測為誤報, 請關閉使用者風險。 如需詳細資訊, 請參閱在[Azure AD Identity Protection 中提供風險](howto-provide-risk-event-feedback.md)偵測的意見反應一文。

### <a name="filter-risky-users"></a>篩選具風險的使用者

若要將報告的資料縮小至您適用的層級，您可以使用下列預設欄位篩選具風險的使用者資料︰

- Name
- 使用者名稱
- 風險狀態
- 風險層級
- Type
- 狀態

![具風險使用者報告](./media/howto-investigate-risky-users-signins/06.png)

[名稱] 篩選條件可讓您指定您想知道的使用者名稱或使用者主體名稱 (UPN)。

[風險狀態] 篩選條件可讓您選取︰

- 有風險
- 已補救
- 已解除

[風險層級] 篩選條件可讓您選取︰

- 高
- 中等
- 低

[類型] 篩選條件可讓您選取︰

- 來賓
- 成員

[狀態] 篩選條件可讓您選取︰

- 已刪除
- 有效

### <a name="download-risky-users-data"></a>下載具風險使用者的資料

如果您想要在 Azure 入口網站之外使用, 您可以下載具風險使用者資料。 按一下 [下載], 會建立最新2500記錄的 CSV 檔案。 

![具風險使用者報告](./media/howto-investigate-risky-users-signins/07.png)

您可以按一下工具列中的 [資料行]，以自訂清單檢視。
 
這可讓您顯示其他欄位，或移除已顯示的欄位。
 
若要深入了解具風險的使用者，請按一下 [詳細資料] 隱藏式選單以展開資料

## <a name="risky-sign-ins-report"></a>有風險的登入報告

使用具風險登入報告提供的資訊，您可以找到下列問題的解答：

- 過去一周內有多少成功登入有匿名 IP 位址風險偵測？
- 上個月有哪些使用者確認遭受入侵？
- 哪些使用者對於 Office 365 入口網站屬於風險登入？

此報告的第一個進入點是安全性頁面上的 [調查] 區段。

![有風險的登入報告](./media/howto-investigate-risky-users-signins/02.png)

具風險的登入報告有預設檢視，顯示：

- Date
- 使用者
- 應用程式
- 登入狀態
- 風險狀態
- 風險層級 (彙總)
- 風險層級 (即時)
- 條件式存取
- 需要 MFA  

![有風險的登入報告](./media/howto-investigate-risky-users-signins/09.png)

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/11.png)

此資料行對話方塊可讓您顯示其他欄位，或移除已顯示的欄位。

按一下清單檢視中的項目，即可取得所有可用的詳細資料 (水平檢視)。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/12.png)

詳細資料檢視會顯示：

- 基本資訊
- 裝置資訊
- 風險資訊
- MFA 資訊
- 條件式存取

此外，您可以：

![具風險使用者報告](./media/howto-investigate-risky-users-signins/13.png)

- 確認遭入侵 
- 確認安全

如需詳細資訊, 請參閱在[Azure AD Identity Protection 中提供風險](howto-provide-risk-event-feedback.md)偵測的意見反應一文。

### <a name="filter-risky-sign-ins"></a>篩選具風險登入

若要將報告的資料縮小至您適用的層級，您可以使用下列預設欄位篩選具風險的使用者資料︰

- 使用者
- 應用程式
- 登入狀態
- 風險狀態
- 風險層級 (彙總)
- 風險層級 (即時)
- 條件式存取
- Date
- 風險層級類型

![有風險的登入報告](./media/howto-investigate-risky-users-signins/14.png)

[名稱] 篩選條件可讓您指定您想知道的使用者名稱或使用者主體名稱 (UPN)。

[應用程式] 篩選條件可讓您指定使用者嘗試存取的雲端應用程式。

[登入狀態] 篩選條件可讓您選取︰

- 全部
- 成功
- 失敗

[風險狀態] 篩選條件可讓您選取︰

- 有風險
- 已確認遭入侵
- 已確認安全
- 已解除
- 已補救

[風險層級 (彙總)] 篩選條件可讓您選取︰

- 高
- 中等
- 低

[風險層級 (即時)] 篩選條件可讓您選取︰

- 高
- 中等
- 低

**條件式存取**篩選器可讓您選取:

- 全部
- 不適用
- 成功
- 失敗

[日期] 篩選條件可讓您定義傳回資料的時間範圍。
可能的值包括：

- 前 1 個月
- 最近 7 天
- 最近 24 小時
- 自訂時間間隔

### <a name="download-risky-sign-ins-data"></a>下載具風險的登入資料

如果您想要在 Azure 入口網站外部使用有風險的登入資料, 您可以下載該資料。 按一下 [下載], 會建立最新2500記錄的 CSV 檔案。 

![具風險使用者報告](./media/howto-investigate-risky-users-signins/15.png)


## <a name="risk-detections-report"></a>風險偵測報告

「風險偵測」報告可讓您深入瞭解租使用者中的每個身分識別保護風險偵測。


風險偵測報告有一個預設視圖, 顯示:

- Date

- 使用者

- IP 位址

- Location

- 偵測類型

- 風險狀態

- 風險層級

- 要求識別碼
 

您可以按一下工具列中的 [資料行] 來自訂清單檢視。 此資料行對話方塊可讓您顯示其他欄位，或移除已顯示的欄位。

按一下清單檢視中的項目，即可取得所有可用的詳細資料 (水平檢視)。


詳細資料檢視會顯示風險偵測的其他資訊。

詳細資料檢視也包含連結至

- View user 的風險報告
- 查看使用者的登入
- 查看使用者的有風險登入
- View 已連結的具風險登入
- 查看使用者的風險偵測


### <a name="filter-risk-detections"></a>篩選風險偵測

若要將報告的資料縮小至適合您的層級, 您可以使用下列預設欄位來篩選風險偵測資料:

- 偵測時間
- 偵測類型
- 風險狀態
- 風險層級
- 使用者
- 使用者名稱
- IP 位址
- Location
- 偵測時間
- activities 
- Source
- 權杖簽發者類型
- 要求識別碼
- 相互關聯識別碼


[**偵測時間**] 篩選準則可讓您定義所傳回資料的時間範圍。 此篩選準則可讓您選取:
- 最近 90 天
- 過去 30 天
- 最近 7 天
- 最近 24 小時
- 自訂時間間隔

[**偵測類型**] 篩選準則可讓您指定風險偵測的類型 (例如不熟悉的登入屬性)。

[風險狀態] 篩選條件可讓您選取︰

- 有風險
- 已確認遭入侵
- 已確認安全
- 已解除
- 已補救

[風險層級] 篩選條件可讓您選取︰

- 高
- 中等
- 低

**使用者**篩選準則可讓您指定您關心的使用者名稱或物件識別碼。

[使用者名稱] 篩選準則可讓您指定您關心之使用者的使用者主要名稱 (UPN)。

[**偵測計時**] 篩選準則可讓您指定偵測為即時或離線。 注意：您可以使用登入風險原則, 透過即時偵測來挑戰登入。 

**活動**篩選準則可讓您指定偵測是否已連結至登入 (例如匿名 IP 位址) 或使用者 (例如洩漏的認證)。

[**來源**] 篩選準則可讓您指定風險偵測的來源 (例如 Azure AD 或 Microsoft Cloud App Security)。 

**權杖簽發者類型**篩選準則可讓您指定發出權杖的位置 (例如 AZURE AD 或 AD 同盟服務)


### <a name="download-risk-detections-data"></a>下載風險偵測資料

如果您想要在 Azure 入口網站以外的地方使用風險偵測資料, 您可以下載。 按一下 [下載], 會建立最新5000記錄的 CSV 檔案。 

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview-v2.md)。
