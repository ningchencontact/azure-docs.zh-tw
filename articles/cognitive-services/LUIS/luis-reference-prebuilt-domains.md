---
title: 預建網域參考-LUIS
titleSuffix: Azure Cognitive Services
description: 預先建立的定義域參考是從 Language Understanding Intelligent Service (LUIS) 預先建立的意圖和實體集合。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: b840f1ce42c9d7e4af8854a2c6bd7fd26f5b88e9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307419"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>您 LUIS 應用程式預先建置的網域參考
此參考提供[預先建立的定義域](luis-how-to-use-prebuilt-domains.md)相關資訊，這是 LUIS 提供之預先建立的意圖和實體集合。

相反地，[自訂網域](luis-how-to-start-new-app.md)一開始沒有任何意圖和模型。 您可以將任何預先建立的定義域意圖和實體新增至自訂模型。

# <a name="supported-domains-across-cultures"></a>跨文化特性支援的網域

下表摘要說明目前支援的網域。 英文的支援通常比其他語言更完整。 

| 實體類型       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [行事曆](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [交流](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [電子郵件](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [注意事項](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [把](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Restaurantreservation.reserve](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [多](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [多數](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [天氣](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

在中**不支援**預先建立的網域：

* 加拿大法文
* Hindi
* 西班牙文墨西哥

# <a name="description-for-luis-prebuilt-domains"></a>LUIS 預建網域的描述
## <a name="calendar"></a>**行事曆**
行事曆是關於個人會議和約會，而不是公開活動（例如，世界盃排程、西雅圖活動行事曆）或一般行事曆的任何專案（例如，今天的日期、開始的時間、何時為工作日）。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------------
 AcceptEventEntry | 在行事曆上接受（n）個約會/會議/活動。 | 接受約會。 <br> 接受事件 <br> 接受今天的會議。
 取消 | 取消虛擬助理進行中的動作，例如取消建立會議的程式。 <br> ***請注意**：此意圖主要包含行事曆案例中的「取消」動作。 如果您需要「取消」的一般運算式，請在**公用程式**網域中運用「取消」意圖。 * | 沒問題，只要取消事件就好了。 <br> 否，我只是取消約會。
 ChangeCalendarEntry | 變更或重新排定行事曆專案。 | 重新排定我的上午6點 預約到下午2點 <br> 重新排定醫生的約會 5 PM <br> 以 jenny olson 到星期五重新排定午餐。 <br> 變更事件時間。
 CheckAvailability | 在使用者的行事曆或其他人的行事曆上，找到可與會的約會或會議時段。 | 何時可以見到 Jim？ <br> 在明天推出 Carol 時顯示。 <br> Chris 星期六有空嗎？
 確認 | 確認是否要根據先前的意圖執行作業/動作。 <br> ***請注意**：此意圖主要包含行事曆案例的「確認」動作。 如果您需要「確認」的一般運算式，請在**公用程式**網域中利用「確認」意圖。 *| 沒錯，請建立會議 <br> 是，謝謝，請連接到會議。
 ConnectToMeeting | 連接到會議。 | 使用您的電話連接到11:00 電話會議 <br> 接受預算會議通話。
 ContactMeetingAttendees | 與會議出席者聯絡。 | 告訴我目前正在執行3:00 會議的會議。 <br> 通知同事上午8點會議，it 需要從8:30 開始。
 CreateCalendarEntry | 新增一次性項目至行事曆。 | 建立有關討論問題的會議。 <br> 使用建立會議abc@microsoft.com
 DeleteCalendarEntry | 要求刪除行事曆項目。| 取消我與 Carol 的約會。 <br> 刪除我的上午9點 出席者. <br> 刪除我的事件。
  FindCalendarEntry | 開啟行事曆應用程式，或搜尋行事曆專案。 | 尋找牙醫回診。 <br> 顯示我的行事曆。 <br> 我在星期四的行事曆有哪些？
 FindCalendarWhen | 檢查排程發生的時間。 | 何時符合琥珀色和 Susan 的需求？ <br> 我何時已排程 brunch？ 
 FindCalendarWhere | 檢查排程發生的位置。 | 我明天的預約在哪裡？ <br>我明天的 Stacy 和 Michael 會在何處與晚餐開會？
  FindCalendarWho | 請檢查將參與目標排程的出席者。 | 我想要在明天的會議上確認的助理為2。 <br> Jim 會在下一場護士的會議嗎？
 FindCalendarDetail | 檢查並顯示排程的詳細資料。 | 我需要為我提供我的同事 Paul 所排定的會議詳細資料。
 FindDuration | 檢查持續時間。 | 需要多少時間才能收取雜貨商店？ <br> 午餐需要多久的時間？
 FindMeetingRoom | 尋找可用的會議室。 | 我有什麼符合的聊天室？ <br> 新的會議位置，尋找一個。
 GoBack | 返回 [上一步] 或 [專案]。  <br> ***請注意**：如需更 GoBack 的一般語句，請參閱**公用程式**網域。 * | 上一個 <br> 回到最後一封電子郵件。
 拒絕 | 使用者會拒絕所提議的虛擬助理。 <br> ***請注意**：如需更多拒絕一般語句，請參閱**公用程式**網域。 * | 不需要設定事件。 <br> 當時我還有其他事項。
ShowNext | 檢查下一個事件。 <br> ***請注意**：如需更 ShowNext 的一般語句，請參閱**公用程式**網域。 * | 提供我的下一個事件。 <br> 行事曆中的下一步是什麼？
 ShowPrevious | 檢查上一個事件。 <br> ***請注意**：如需更 ShowPrevious 的一般語句，請參閱**公用程式**網域。 * | 這之前的排程是什麼？
 TimeRemaining | 檢查剩餘的時間，直到下一個事件為止。 | 顯示我的會議前有多少時間。 <br> 顯示我的下一個會議開始前的時間量。
 
### <a name="entities"></a>**條目**
實體名稱 | 實體類型 | 描述 | 範例 | 插槽
-------|-----------------------|-------------|---------|--------
ContactName | personName | 連絡人或會議出席者的名稱。 | 符合**Betsy**。 <br>  于7月3日下午7點與**Aubrey**會面 | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | 單個 | 目標行事曆名稱。 | 使用 mom 星期二 12**個人**的午餐 <br> 使用我的**Google**日曆作為預設行事曆。 <br> 將立功意義類別更新為下午3點的星期一週三 **個人**行事曆中的清單。 | Google <br> 人身 <br> 業務 <br> 主要
Duration | datetime | 會議、約會或剩餘時間的持續時間。 | 新增至工作行事曆與 Gary 會面，以在未來上午11點討論 scholarship 詳細資料**20 分鐘**。 <br> 在星期五的 subway 上將事件新增至行事曆，我將于下午9點吃 Thomas**一小時** | 一小時 <br> 2 天 <br> 20 分鐘 
EndDate | datetime | 會議或約會的結束日期。 | 行事曆在低音廳 Mary 3 到**Mary 5**新增音樂會 | Mary 5  
EndTime | datetime | 會議或約會的結束時間。 | 您可以將它設為 2 30 到**三個** | 三 
Location | 單個 | 行事曆項目、會議或約會的位置。  地址、城市和地區都是很好的位置範例。 | 將會議放在**fremont**中，將平板電腦放入 Burma <br> **Edina**中的 pro bono 會議 | 209 Nashville Gym <br> 897 Pancake house <br> 車庫 
MeetingRoom | 單個 | 會議室供會議或約會使用。 | 使用 jake 在下午2點新增至工作行事曆會議 在他的**辦公室**中，星期五 | 他的辦公室 <br> 會議室 <br> 房間2
MoveEarlierTimeSpan | datetime | 使用者想要稍早移動會議或約會的時間。 | 將我的午餐日期前移**30 分鐘**。 | 30 分鐘 <br> 兩小時 
MoveLaterTimeSpan |  datetime | 使用者稍後想要移動會議或約會的時間。 | 將我的會議與蘭花盒後推後**4 小時**。 | 4 小時 <br> 15 分鐘 
OrderReference | 單個 | 清單中的序數或相關位置，用於識別要擷取的項目。 | 明天的下一個約會是什麼？ | 下一步
OriginalEndDate | datetime | 會議或約會的原始結束日期。 | 將我的假期從**星期五**結束變更為星期一 | 星期五 
OriginalEndTime | datetime | 會議或約會的原始結束時間。 | 讓一個結束于**3** ，直到4 | 3
OriginalStartDate | datetime | 會議或約會的原始開始日期。 | 從上午10點變更**明天**的約會 到星期三上午9點  | 明天 
OriginalStartTime | datetime | 會議或約會的原始開始時間。 | 從**上午 10**點變更明天的約會 到星期三上午9點 | 上午10點
PositionReference | ordinal | 清單中的絕對位置，識別要取出的專案。 | **第二個** | 秒 <br> 資料分割 3 <br> 數位5
RelationshipName | 清單 | 連絡人的關聯性名稱。 | 在下午1:00 新增午餐 有我的**妻子** | 妻子 <br> 丈夫 <br> 備用 
SlotAttribute | 單個 | 使用者想要查詢或編輯的屬性。 | 變更事件**位置** <br> 將**時間**變更為七點鐘 | 位置 <br> time 
StartDate | datetime | 會議或約會的開始日期。 | 在**星期三**下午4點建立會議 | 星期三 
StartTime | datetime | 會議或約會的開始時間。 | 在星期三**下午 4**點建立會議 | 下午4點
Subject | 簡單的模式。任何 | 主旨，例如會議或約會的標題。 | **參與方準備**會議的時間為何？ | 牙醫 <br> 與 Julia 共進午餐 
Message | 簡單的模式。任何 | 要傳送給出席者的訊息。 | 提醒出席者開會，**我將會延遲**。 | 我會遲到

## <a name="communication"></a>**交流**
要求進行呼叫、傳送文字/IMs、尋找/新增連絡人，以及各種其他與通訊相關的要求（通常是傳出）。 _僅連絡人名稱_語句不屬於通訊網域。

### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
AddContact | 新增連絡人至使用者的連絡人清單。 | 加入新的連絡人。  <br>   儲存此數位，並將名稱設為 Jane。
AddFlag | 將旗標新增至電子郵件 | 將此電子郵件標記為 <br> 在此電子郵件中新增旗標。
AddMore | 新增更多內容至電子郵件或簡訊，作為逐步電子郵件或簡訊撰寫的一部分。 | 新增更多文字。  <br>   新增更多電子郵件內文。
回答 | 接聽來電。 | 接聽電話。  <br>   撿起來。
AssignContactNickname | 為連絡人指派暱稱。 | 將 Isaac 變更為 Dad。  <br>   編輯 Jim 的昵稱。 <br>   將昵稱新增至 Patti Owens。
回電 | 傳回通話。 | 回呼。
CallVoiceMail | 連線到使用者的語音信箱。 | 將我連線到我的語音信箱。 <br>語音信箱。 <br>   呼叫語音信箱。
取消 | 取消作業。 | 取消。 <br>   結束。
CheckIMStatus | 檢查 IM 中連絡人的狀態。 | Jim 的連線狀態是否設定為離開？ 
CheckMessages | 檢查是否有新的訊息或電子郵件。 | 檢查我的收件匣 <br>  我有任何新郵件嗎？
確認 | 確認動作。 | 是，傳送它。 <br> 沒錯，我確認要傳送這封電子郵件。
于回呼 endcall | 結束通話。 | 掛斷通話。 <br> 結束。
FindContact | 依名稱尋找連絡人資訊。 | 尋找母親的號碼。 <br>   顯示 Carol 的號碼。
FinishTask | 完成目前的工作。 | 我已完成 <br> 就是這樣。
TurnForwardingOff | 關閉來電轉接。 | 停止轉接我的來電。 <br> 關閉呼叫轉送。
TurnForwardingOn | 開啟呼叫轉送。 | 將我的來電轉接至 3333 <br>  將呼叫轉送切換為3333。
GetForwardingsStatus | 取得來電轉接的目前狀態。 | 是否啟動我的來電轉接？ <br>   如果我的通話狀態為開啟或關閉，請告訴我。
GetNotifications | 取得通知。 | 開啟我的通知 <br>   您可以檢查我的電話 facebook 通知
Goback | 返回上一個步驟。 | 返回 Twitter <br>   返回一個步驟 <br>   回復
忽略 | 略過來電。 | 不接聽 <br>   略過來電
IgnoreWithMessage | 略過來電並改以簡訊回覆。 | 不接聽該通來電但改為傳送訊息。 <br>   略過並傳回簡訊。
撥號 | 撥打電話。 | 撥打電話給 Jim <br>   請撥打311。
FindSpeedDial | 尋找電話號碼設成的快速撥號，或尋找設為快速撥號的電話號碼。 | 我的撥號 5 為何？ <br>   我是否已設定快速撥號？ <br>   941-5555-333 的快速撥號為何？
轉寄 | 轉寄電子郵件 | 將此電子郵件轉寄給 Greg。
ReadAloud | 對使用者朗讀訊息或電子郵件。 | 朗讀簡訊。 <br>   她在訊息中說了什麼？
PressKey | 按下按鈕或數字鍵台上的數字。 | 撥打米字鍵。 <br>   按 1 2 3。
QueryLastText | 查詢最後一則文字或訊息。 | 誰簡訊傳送我？ <br>   誰最近 messaged 我？
Redial | 重撥或再次撥打某個號碼。 | 重撥。 <br>   重撥我的上一通電話。
拒絕 | 拒絕來電。 | 拒絕來電 <br>   目前無法接聽 <br>   目前無法接聽，稍後將回電。
回覆 | 回復訊息。 | 回應傳說迷 <br>   藉由輸入我的方式來回複
SearchMessages | 依某些條件搜尋訊息。 | 您可以搜尋 Jane 傳送的電子郵件嗎？
SendEmail | 傳送電子郵件。 此意圖適用於電子郵件，但不適用於文字簡訊。 | 傳送電子郵件至 Mike Waters：Mike，上週的晚餐很棒。 <br>   傳送電子郵件給 Bob。
SendMessage | 傳送文字簡訊或立即訊息。 | 傳送簡訊給 Chris 和 Carol <br>   Facebook message greg <br>   
SetSpeedDial | 設定連絡人電話號碼的快速撥號快速鍵。 | 將 Carol 的快速撥號設定為 1。 <br>   設定媽媽的快速撥號。
ShowCurrentNotification | 顯示目前的通知。 | 是否有任何新的通知？ <br> 顯示通知。
ShowNext | 查看下一個項目，例如文字簡訊或電子郵件清單中的下一個項目。 | 顯示下一個。 <br>   移至下一頁。
ShowPrevious | 查看上一個項目，例如文字簡訊或電子郵件清單中的上一個項目。 | 顯示上一個。 <br>   處. <br>   移至上一個。
TurnSpeakerOff | 關閉免持聽筒。 | 結束擴音器。 <br>   關閉免持聽筒。
TurnSpeakerOn | 啟動免持聽筒。 | 免持聽筒模式。 <br>   將免持聽筒設定為開啟。

### <a name="entities"></a>**條目**
實體名稱 | 實體類型 | 描述 | 範例 | 插槽
------|-------|----------|--------------|---------------
Attachment | 單個 | 使用者想要以文字或電子郵件傳送的附件。 | **從 onenote**以電子郵件傳送檔案。 <br> **將我的內務處理**檔傳送至 Katie。 | 檔案 <br> 首
AudioDeviceType | 單個 | 音訊裝置的類型（說話者、耳機、麥克風等等）。 | 使用**免實習**的答案。 <br> 在**喇叭電話**上重新撥打電話。 | 演講 <br> 無人參與 <br> 藍牙
Category | 單個 | 訊息或電子郵件的分類，此類別目錄必須在電子郵件系統中具有清楚的定義，例如「未讀取」、「旗標」。 描述：如「新增」和「最近」，則不是分類。 | 將所有電子郵件標記為已**讀取**  <br> Paul 的新**高優先順序**電子郵件 | 重要 <br> 高優先順序 <br> 讀取
ContactAttribute | 單個 | 使用者所查詢之連絡人的屬性。| 下個月的任何**生日**都應該知道嗎？ | 以外 <br> 位址 <br> 電話號碼
ContactName | personName  | 連絡人或訊息收件者的名稱。 | 將郵件傳送至**Stevens** | Stevens
Date/Time | datetime | 收到的電子郵件日期時間。 | 閱讀**今天**的郵件 <br> **今天**我的電子郵件是誰？ <br> 誰在**下午 7**點 phoned？ | 今天 <br> 明天
DestinationPhone | 單個 | 目標使用者想要呼叫或傳送文字給。 | 撥打**房屋**電話 <br> 將文字訊息傳送至**首頁** | 房 <br> 首頁
EmailAddress | email | 使用者想要傳送或查詢的電子郵件地址。 | 傳送電子郵件給Megan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | 簡單的模式。任何 | 作為電子郵件主旨列的文字。 | **使用主旨**撰寫電子郵件給 David  | RE: 有趣的故事
Key | 單個 | 使用者想要按的按鍵。 | 按**空格鍵**鍵。 <br> 按**9** | 跟 <br> star <br> 8
行 | 單個 | 使用者想要用來傳送電子郵件或中的文字的那一行。 | 閱讀我的最後一封**hotmail**電子郵件。 <br> 透過**mobile**呼叫 Peter。 <br> 使用我的**工作**行打電話給爸爸。| hotmail <br> Skype <br> 英國行動電話
SenderName | personName | 寄件者的名稱。 | 閱讀**David**的電子郵件 <br> 來自 Chanda 的電子郵件 | David <br> Chanda
FromRelationshipName | 單個 | 寄件者的關聯性名稱。 | 讀取來自**爸爸**的訊息。 <br> 您可以從**mom**閱讀所有文字訊息嗎？ | Dad <br> 母親 
Message | 簡單的模式。任何 |  要作為電子郵件或簡訊傳送的訊息。  | 傳送電子郵件，指出「**我忙碌**中」。 | 我忙碌中
OrderReference | 單個 | 清單中的序數或相關位置，用於識別要擷取的項目。 | 我傳送的**最後一個**訊息是什麼？ <br> 閱讀**最新**的 nokia 電子郵件。 <br> 讀取**新**的文字訊息。 | last <br> 最新 <br> ' <br> 降冪
PositionReference | 簡單、序數 | 清單中的序數或相關位置，用於識別要擷取的項目。| 我傳送的**第一個**訊息是什麼？ <br> **第三**個。| 第一個 <br> 方
phoneNumber | phoneNumber | 使用者想要呼叫或傳送文字的電話號碼。 | 將文字傳送至**4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName | 單個 | 連絡人或訊息收件者的關聯性名稱。 | 傳送電子郵件給我的**妻子** | 妻子
SearchTexts | 簡單、模式. any | 用於篩選電子郵件或訊息的文字 | 搜尋包含「**Surface Pro**」的所有電子郵件 | Surface Pro
撥號 | 單個 | 速度撥號。 | 呼叫**3 4 5**。 <br> 設定速度撥接**一次**。 | 345 <br> 5

## <a name="email"></a>**電子郵件**
電子郵件是*通訊*網域的子域。 它主要包含透過電子郵件傳送和接收訊息的要求。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
AddMore | 新增更多內容至電子郵件或簡訊，作為逐步電子郵件或簡訊撰寫的一部分。 | 新增更多電子郵件內文。
取消 | 取消作業。 <br> ***請注意**：如需更多取消一般語句，請參閱**公用程式**網域。 * | 取消。 不要傳送它。 <br> 結束。
CheckMessages | 檢查是否有新的郵件/電子郵件，但沒有條件式查詢。 如果有任何條件，則語句會屬於*SearchMessage*意圖。 | 檢查我的收件匣。 <br> 我有任何新郵件嗎？ 
確認 | 確認與處理電子郵件相關的持續性動作。 <br> ***請注意**：如需更多確認一般語句，請參閱**公用程式**網域。 * | 是，傳送它。 <br> 我確認要傳送此電子郵件。
DELETE | 刪除電子郵件或依某些條件篩選的郵件。 | 將電子郵件放入回收站 <br> 清空我的收件匣。 <br> 移除 Mary 的電子郵件。
ReadAloud | 對使用者朗讀訊息或電子郵件。 <br> ***請注意**：如需更 Note.readaloud 的一般語句，請參閱**公用程式**網域。 *  | 讀取 Mary 傳送的電子郵件。
回覆 | 將訊息回復到目前的電子郵件。 | 建立電子郵件的回應。 <br> 輸入「謝謝您的很多人，您好，John」的回復。
SearchMessages | 依某些條件搜尋訊息，包括寄件者名稱、時間和主旨。 | 顯示來自 Nisheen 的訊息。 <br> 您可以搜尋標題為 "ABC" 的電子郵件嗎？
SendEmail | 傳送電子郵件。 | 寄送電子郵件給 Mike：Mike，上週的晚餐很棒。 <br> 傳送電子郵件給 Bob。
ShowNext | 查看文字訊息或電子郵件清單中的下一個專案。 <br> ***請注意**：如需更 ShowNext 的一般語句，請參閱**公用程式**網域。 * | 顯示下一個。 <br> 移至下一頁。
ShowPrevious | 在文字訊息或電子郵件清單中顯示上一個專案。 <br> ***請注意**：如需更 ShowPrevious 的一般語句，請參閱**公用程式**網域。 * | 顯示上一個。 <br> 處. <br> 移至上一個。
轉寄 | 轉寄電子郵件。 | 將此電子郵件轉寄給 Greg。
AddFlag | 將旗標新增至電子郵件。 | 將此電子郵件標記為 <br> 在此電子郵件中新增旗標。
QueryLastText | 查詢最後一封電子郵件。 | 誰傳送電子郵件給我？ <br> 我最近何時傳送電子郵件給我？


### <a name="entities"></a>**條目**
實體名稱 | 實體類型 | 描述 | 範例 | 插槽
------|-------|----------|--------------|---------------
Attachment | 單個 | 使用者想要以文字或電子郵件傳送的附件。 | **從 onenote**以電子郵件傳送檔案。 <br> **將我的內務處理**檔傳送至 Katie。 | 檔案 <br> 首
ContactName | personName  | 連絡人或訊息收件者的名稱。 | 將郵件傳送至**Stevens** | Stevens
Date | datetime | 收到的電子郵件日期。 | 閱讀**今天**的郵件 <br> **今天**我的電子郵件是誰？ | 今天
EmailAddress | email | 使用者想要傳送或查詢的電子郵件地址。 | 傳送電子郵件給Megan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | 簡單的模式。任何 | 作為電子郵件主旨列的文字。 | **使用主旨**撰寫電子郵件給 David  | RE: 有趣的故事
SenderName | personName | 寄件者的名稱。 | 閱讀**David**的電子郵件 <br> 來自 Chanda 的電子郵件 | David <br> Chanda
FromRelationshipName | 單個 | 寄件者的關聯性名稱。 | 讀取來自**爸爸**的訊息。 <br> 您可以從**mom**閱讀所有文字訊息嗎？ | Dad <br> 母親 
Message | 簡單的模式。任何 |  要作為電子郵件或簡訊傳送的訊息。  | 傳送電子郵件，指出「**我忙碌**中」。 | 我忙碌中
Category | 單個 | 訊息或電子郵件的分類，此類別目錄必須在電子郵件系統中具有清楚的定義，例如「未讀取」、「旗標」。 描述：如「新增」和「最近」，則不是分類。 | 將所有電子郵件標記為已**讀取**  <br> Paul 的新**高優先順序**電子郵件 | 重要 <br> 高優先順序 <br> 讀取
OrderReference | 單個 | 清單中的序數或相關位置，用於識別要擷取的項目。 | 我傳送的**最後一個**訊息是什麼？ <br> 閱讀**最新**的 nokia 電子郵件。 <br> 讀取**新**的文字訊息。 | last <br> 最新 <br> ' <br> 降冪
PositionReference | 簡單、序數 | 清單中的序數或相關位置，用於識別要擷取的項目。| 我傳送的**第一個**訊息是什麼？ <br> **第三**個。| 第一個 <br> 方
RelationshipName | 單個 | 連絡人或訊息收件者的關聯性名稱。 | 傳送電子郵件給我的**妻子** | 妻子
Time | datetime | Time | 在此**傳送電子郵件**。 | 今晚
SearchTexts | 簡單、模式. any | 用於篩選電子郵件或訊息的文字 | 搜尋包含「**Surface Pro**」的所有電子郵件 | Surface Pro 
行 | 單個 | 使用者想要用來傳送電子郵件的程式程式碼。 | 閱讀我的最後一封**hotmail**電子郵件。 <br> 從我的**公司帳戶**傳送電子郵件。| hotmail <br> 公司帳戶 

## <a name="homeautomation"></a>**HomeAutomation**
HomeAutomation 網域提供與控制智慧型家庭裝置相關的意圖和實體。 我們主要支援與光源和空氣空調相關的控制命令。 但是它在其他電動設備上有一些一般化的功能。
### <a name="supported-devices-and-properties"></a>**支援的裝置和屬性**
裝置 | 屬性
-------|---------
溫度感應器 | 溫度
淺色、燈泡 | 關閉、亮度、色彩
電視、收音機 | 已關閉，磁片區
空中空調（A/C）、控溫器 | 開，溫度，電源
迷 | 開時，電源
[輸出]、[dvd 播放機]、[ice maker] 等等。 | 開-關

### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
 TurnOff | 使用者想要關閉裝置或設定。  | 關閉燈。 <br> 關閉某個專案。 <br> 一些東西。
 TurnOn | 開啟裝置或開啟裝置，並將裝置設定為特定設定或模式。 | 將光源開啟至 50%。 <br> 開啟我的咖啡機 <br> 可以開啟我的咖啡機嗎？
 SetDevice | 使用者想要將裝置設定為特定的設定或類型。  | 將 [空氣空調] 設定為26度。 <br> 將光源變成藍色。 <br> 以 nightlight 的身分呼叫此燈。
 QueryState | 使用者要求提供裝置或設定的狀態。  | 控溫器的設定為何？ <br> A/C 是 on？ <br> 臥室溫度為何？
 TurnUp | 開啟裝置的設定或亮度。 | 以75百分比來變亮燈。 <br> 10% 以上的亮度。  <br> 使其在生活空間中更暖。
 TurnDown | 關閉但不關閉裝置，不論是透過變暗、光線的溫度或亮度。 | 以 44% 關閉媒體櫃。 <br> 將光線變暗。 <br> 讓房間更酷。
### <a name="entities"></a>**條目**
實體名稱 | 實體類型 | 描述 | 範例
-------|----------|--------------|---------------
DeviceName | List | 使用者為其裝置定義的文字。 | 藍色<br> 願 <br> 打電話
DeviceType | List | 支援的裝置。 | 無 <br> 空中空調 <br> nightlight
Location | 單個 | 裝置所在的位置或房間。 | 廚房<br> 樓下 <br> 臥室
NumericalChange | 單個 | 設定要增加或減少的數量。 <br> <br> _此位置只會顯示 turn_up 和 turn_down 意圖。_ | 3<br> 50%<br>
OrderReference | ordinal | 此位置的目的是要捕捉專案的選擇。 它會指出專案在清單上的位置。 | 第一個<br> 第二個
限定詞 | List | 數量詞指出會參考特定實體的多少個實例。 例如，「全部」、「每個」等等。 | 全部<br> 每一個<br> 所有項目
設定 | 簡單 | 使用者想要設定其裝置的目標設定，包括場景、層級、濃度、色彩、模式、溫度、裝置的狀態。 | 藍色<br> 72 <br> 熱度 
SettingType | List | 使用者感興趣的裝置設定。 | 溫度<br> 
時間/日期 |  datetime | 操作裝置的時間和持續時間| 5 分鐘 <br> 下午3點
單位 | List | 若要標記「度數」、「百分比」、「華氏」、「攝氏」等單字，應分別標記每個單位詞彙。 | °<br> Percent


## <a name="notes"></a>**注意事項**
注意 domain 有助於建立便箋並為使用者寫下專案。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
AddToNote | 將資訊新增至已開啟的便箋。 | 新增至我的規劃附注，以聯絡我的老闆關於專案。
清除 | 從現有筆記中清除所有項目。 | 從我的假期便箋中移除所有專案。 <br>從我的閱讀便箋全部清除。
確認 | 確認與筆記相關的動作。 <br> ***請注意**：此意圖主要包含記事案例的「確認」動作。 如果您需要「確認」的一般運算式，請在**公用程式**網域中利用「確認」意圖。 * | 這是我的注意事項。 <br>我確認要將所有專案保留在清單上。
建立 | 建立新的筆記。 | 建立便箋。 <br>請注意，提醒我 Jason 是5月的第一周。 
DELETE | 刪除整個筆記。 | 刪除我的休假便箋。 <br>刪除我的雜貨商店便箋。
ReadAloud | 大聲閱讀便箋。 | 請閱讀第一張便箋。 <br>閱讀詳細資料。
關閉 | 關閉目前的便箋。 | 關閉便箋。 <br>關閉目前的便箋。
開啟 | 開啟預先存在的便箋。 | 開啟我的 vocation 便箋。 <br>開啟附注「規劃」。
RemoveSentence | 移除便箋的句子。 | 移除最後一個句子。 <br>從我的雜貨便箋中刪除晶片。 <br>從我的學校購物便箋中移除畫筆
ChangeTitle | 變更便箋的標題。 | 將此附注命名為「規劃」。

### <a name="entities"></a>**條目**
實體名稱 | 實體類型 | 描述 | 範例 
------- | ------- | ------- | -------
文字 | 簡單的模式。任何 | 筆記或提醒的文字。 | 步行前先做伸展操 <br> 明天的長跑
標題 | 簡單的模式。任何 | 筆記的標題。 | 雜貨 <br> 要致電的人員 <br> 待辦事項
CreationDate | datetimeV2 | 當使用者要求在特定日期/時間範圍內建立的記事時，此位置適用于。 | 
限定詞 | List | 當使用者要求對 [全部]、[每個] 或 [任何] 專案或便箋中的所有文字執行動作時。 | 全部 <br> 任何 <br> 每個
OrderReference | ordinal | 使用者想要使用「first」、「last」、「next」等專案來進行動作。 | first <br> last


## <a name="places"></a>**把**
地點包括企業、機構、餐廳、公共空間和位址。 網域支援就地尋找並將資訊詢問為公共場所，例如位置、上班時間和距離。 
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
MakeCall | 撥打電話到某個地點。 | applebees on 1000/200 Rojas St 並呼叫。
FindPlace | 搜尋地點 (公司、機構、餐廳、公共空間、地址)。 但不是： <li> 僅限公司名稱：星巴克 <li> 僅限位置名稱：西雅圖/挪威 <li> 僅限料理、食物或產品：比薩/guacamole/義大利文 | 最近的圖書館在哪裡？ <br> 西雅圖的星巴克。 <br> 最近的圖書館在哪裡？ <br> 
GetAddress | 要求某個地點的地址。 | 在 Robson 的街道上顯示 Street 的位址。 <br> 最近的 Starbucks 在哪裡？
GetDistance | 詢問從目前位置到特定位置的距離。 | Holiday Inn 有多遠？<br> 從這裡 Bellevue 正方形有多遠？ <br> Tahoe 的距離為何？
GetPhoneNumber | 要求某個地點的電話號碼。 | 最近的 Starbucks 電話號碼為何？<br> 提供家庭維修的號碼。 <br> 用於 Disneyland 的電話號碼。
GetPriceRange | 詢問某個地點的每個人數耗用量範圍。 | 西雅圖壽司的平均價格。 <br> Cineplex 每週三是否半價？ <br> 在 Sizzler，全隻龍蝦晚餐價格為何?
GetStarRating | 要求某個地點的星級評等。 | 如何分級 Zucca？<br> French Laundry 有多少顆星？<br> 蒙特利的水族館是否不錯？
GetHours | 詢問某個地點的營業時間。 | Safeway 何時打烊？<br> Home Depot 的營業時間為何？<br> Starbucks 還開著嗎？
GetReviews | 要求某個地點的評論。 | 顯示 Cheesecake Factory 的評論。 <br> 閱讀 Cineplex 評論。 <br> 是否有任何近期的 Humperdinks 評論？
GetMenu | 要求餐廳的菜單項目。 | Zucca 是否有供應素食？ <br> Sizzler 的功能表有哪些功能？ <br> 顯示 Applebee 的功能表。
RatePlace | 對某個位置進行評分。 | Kimberly 中 Maxi 之比薩的4顆星分級。 <br> 在 Tripadvisor 上提供4顆星給 Bonefish。 <br> 為 La Hacienda 建立4顆星的評論。
AddFavoritePlace | 使用者想要將位置新增至我的最愛或 MVP 清單。 | 將此位置儲存至我的最愛。 <br> 新增 [我的最愛] 的 [購買]。

### <a name="entities"></a>**條目**
LUIS 實體 | 實體類型 | 描述 | 範例 | 語句範例
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | 單個 | 某個地點的位置或地址。 | Palo Alto <br> 300 112th Ave SE <br> Seattle | **1020 Middlefield Rd。** 在**Palo Alto**中 <br> **西雅圖**的鳥種子存放區 <br> 取得從這裡到**300 112th**的距離。
Amenities | 單個 | 公共場所的目標特性和優點。 | 水邊 <br> 免費停車 | 柯克蘭**濱水區**的海鮮餐廳。 <br> 是否有任何**免費的停車**近端？
Cuisine | 單個 | 食物類型、料理或料理國籍。 | 中文 <br> 義大利文 <br> 壽司 <br> Noodle <br> | 協助我尋找**中文**餐廳。 <br> **壽司**餐廳的上班時間為何？ <br> 最接近的**牛排館**房屋在哪裡？
Date | datetime | 目標位置的日期時間或時間。 | 明天 <br> 今天 <br> 下午6點 | 水族箱**明天**的結束時間為何？ <br> **下午 6**點後開啟的最接近自行車商店
距離 | 維度 | 從使用者的 currenct 位置到公用位置的距離。 | 15 英哩 <br> 10英里 | **15 英里**內的服裝商店 <br> 一個小孩的餐廳，只有**10 英里**的距離
MealType | List | 早餐或午餐等餐飲類型。 | 早餐 <br> 正餐 | 搜尋**早餐**Greenwood 西雅圖 <br> 尋找我的**午餐**位置。
旁邊 | List | 描述附近位置，但不含絕對位置或位址。 | 與其 <br> 在此區域中 <br> 關於我 | 尋找**最接近**的印度餐廳。 <br> 我的**本機**Wetherspoon 在哪裡？ <br> 有任何不錯**的餐廳嗎**？
OpenStatus | List | 指出某個地點為營業或關店。 | 開啟 <br> 已關閉 | Yogurt Land 今天**關閉**了什麼時間？ <br> Costco 的**開啟**時數為何？
PlaceName | 單個 | 公司、餐廳、公共遊樂設施或機構目的地的名稱。 位置名稱可以包含 placetype （如果它是常用的）。 | Central Park <br> Safeway <br> Walmart| **Safeway**藥房的開啟時間為何？ <br> **Walmart**是否開放？
PlaceType | 單個 | 當地公司、餐廳、公共遊樂設施或機構目的地的類型。 | 餐廳 <br> 歌劇 <br> 電影院 | 康橋中的**cinemas** <br> 是否有**餐廳**近端？
PriceRange | 單個 | 地點產品或服務的價格範圍。 | 經濟 <br> 符合成本效益 <br> 便宜 | 尋找**價格實惠**的設備修復 <br> 目前開啟的**便宜**的比薩餅位置是什麼？
產品 | 單個 | 某個地點提供的產品。 | 服裝 <br> 電視 | 取得**食物**的最佳位置為何？ <br> 尋找**電視**的東部 kilbride。
Rating | 單個 | 某個地點的評等。 | 5 顆星 <br> top <br> 充分 | 是否有任何**不錯**的地方可以讓我開始進行，並在明天吃 <br> **最佳**阿姆斯特丹餐廳 <br> 列出**前**三個比薩商店。


## <a name="restaurantreservation"></a>**Restaurantreservation.reserve**
餐廳保留網域支援處理餐廳保留的意圖。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
Reserve | 要求預約餐廳。 | 在 Zucca 為兩個的保留時間。 <br> 為明天預訂一張資料表。 <br> Palo Alto 中的3個數據表為7。 <br> 在紅色 Lobster 中，將保留專案設為3。
DeleteReservation | 取消或刪除餐廳的保留。 | 在 Zucca 明天的晚上取消保留。 <br> 忘了我在下午7:00 的 red lobster 保留 下一個星期五。 <br> 我不需要保留 Zucca，請將它取消。
ChangeReservation | 變更現有餐廳保留的時間、地點或人數。 | 將我的保留變更為下午9點 <br> 將我的保留從 Zucca 變更為 Red Lobster。 <br> 7人，而不是6，用於 Zucca 的保留。
確認 | 確認與保留相關的動作。 <br> ***請注意**：此意圖主要包含餐廳保留案例的「確認」動作。 如果您需要「確認」的一般運算式，請在**公用程式**網域中利用「確認」意圖。 * | 沒錯，我已在 pasta maker 的8點鐘進行保留。 <br> 是的，只要將它預訂就可以了。 <br> 確認保留在壽司列。
FindReservationDetail | 顯示現有保留的詳細資訊。 | 在 renaissance 聖地牙哥尋找我的保留 <br> 明天顯示保留。 <br> 顯示 Zucca 的保留詳細資料。
FindReservationWhere | 檢查保留專案的絕對位置。 | Zucca 在我的保留中的位置為何？ <br> 在明天顯示保留的地區設定。
FindReservationWhen | 檢查保留的確切時間 | 未來的 Zucca 保留時間為何？ <br> 我在 Red Lobster 的保留時間。
拒絕 | 使用者會拒絕在管理保留時所提議的虛擬助理。 <br> ***請注意**:P 租用參考**公用程式**網域以取得更多拒絕一般語句。 * | 不需要設定事件。 | 否，我不想要變更保留。 <br> 否，不要預訂，我犯了一個錯誤。

### <a name="entities"></a>**條目**
LUIS 實體 | 實體類型 | 描述 | 範例
-------|------|---------|-------------------
地址 | 單個 | 預約的活動位置或地址。 | Palo Alto<br>300 112th Ave SE<br>Seattle
Atmosphere | List | 餐廳的大氣說明。 | 浪漫<br>休閒<br>適合團體<br>！
Cuisine | 單個 | 食物類型、料理或料理國籍。 | 中文<br>義大利文<br>墨西哥<br>壽司<br>Noodle<br>牛排館
MealType | List | 與預約建立關聯的飲食類型。 | 早餐<br>正餐<br>午餐<br>晚餐
PlaceName | 單個 | 餐廳的名稱 | Zucca<br>Cheesecake Factory<br>red lobster
Rating | 單個 | 地點或餐廳的評等。 | 5 顆星<br>3 顆星<br>4顆星
NumberPeople | 單個 | 保留的人數 | 3<br>六
Time | datetime| 餐廳保留的時間 | 明天<br>今晚<br>下午7:00<br>聖誕前夕


## <a name="todo"></a>**多**
_Todo_定義域提供工作清單類型，讓使用者加入、標記和刪除其待辦事項專案。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
AddToDo | 使用者想要加入任何清單類型的工作專案。 |  提醒我購買牛奶。 <br> 將名為「購買牛奶」的專案新增至我的待辦事項清單
確認 | 使用者想要確認進行中的動作。 語句包含如「是」的明確信號，以確認作業。 <br> <br> ***請注意**：此意圖主要包含 ToDo 案例的「確認」動作。 如果您需要「確認」的一般運算式，請在**公用程式**網域中利用「確認」意圖。 * | 請刪除工作。 <br> 我確定要新增這項工作。 <br> 是，我要這麼做。
取消 | 使用者想要取消進行中的動作。  <br> <br> ***請注意**：此意圖主要包含餐廳保留案例的「確認」動作。 如果您需要「確認」的一般運算式，請在**公用程式**網域中利用「確認」意圖。 * | 不行，別忘了。 <br> 只要取消工作就能了。 <br> 否，不要新增它。
DeleteToDo | 藉由參考工作清單中的專案，或刪除所有待辦事項專案，以刪除它。 | 移除所有工作。 <br> 協助我刪除第二個。
MarkToDo | 藉由參考其順序或工作內容，將工作標示為已完成或已完成。 | 將工作「購買牛奶」標示為已完成。 <br> 完成工作讀取。 <br> 全部完成。
ShowNextPage | 清單會分割成數個頁面，以顯示。 在下一個頁面上顯示使用者的清單專案。 | 您可以在 [購物清單] 中顯示下一頁嗎？ <br> 下一步是什麼？ <br> 後續步驟
ShowPreviousPage | 在前一頁顯示使用者的清單專案。 | 顯示 [上一步]。 <br> 我需要檢查先前的工作。
ShowToDo | 顯示待辦事項清單上的所有專案。 | 顯示我的購物清單。 <br> 顯示我的雜貨店清單。

### <a name="entities"></a>**條目**
LUIS 實體 | 實體類型 | 描述 | 範例
-------|------|---------|-------------------
ContainsAll | 單個 | 代表工作清單中的所有或任何專案 | 您可以協助移除**所有**工作。 <br> 完成**所有**作業。
ordinal | ordinal | 專案的序數或數值參考。 | 將**第三**個標示為已完成。 <br> 刪除**第一個**工作。
ListType | 單個 | 工作清單類型。  | 將鞋新增至我的**購物**清單。
FoodOfGrocery | List | 偵測食物專案清單 | 提醒我購買**牛奶**。 <br> 將**牛絞肉**新增至我的雜貨清單。
TaskContent | 簡單、模式. any | 偵測工作的內容。 | 請提醒我**打電話給我**。 <br> 將**慶祝 John 的生日**新增至待辦事項清單


## <a name="utilities"></a>**多數**
公用_程式_網域是所有_LUIS_預建模型中的一般網域，其中包含不同案例中的常見意圖和語句。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
 取消 | 取消動作/要求/工作/服務。 | 將它取消。 <br> 千萬別忘了。
 確認 | 確認動作。 | 當然 <br> 是，請 <br> 確認。
 拒絕 | 使用者會拒絕所提議的虛擬助理。 | 否
 FinishTask | 完成使用者開始的工作。 | 我已經完成了。 <br> 就這樣。 <br> 結束.
 GoBack | 返回一個步驟或返回上一個步驟。 | 返回步驟。 <br> 回去。
 Help | 要求說明。 | 請提供協助。 <br> 開啟 [說明]。
 重複 | 重複動作。 | 再說一次。
 ShowNext | 顯示或告知下一個專案。 | 顯示下一個。
 ShowPrevious | 顯示序列中的上一個項目。 | 顯示上一個。
 StartOver | 重新啟動應用程式或啟動新工作階段。 | 後.
 停止 | 告訴虛擬助理停止交談。  | 請停止說這種情況。
 SelectAny | 使用者要求選取螢幕上顯示的任何專案或結果。 | 其中任何一項。 <br> 選取任何一個。
 SelectNone | 使用者不會選取任何現有的專案，也不會要求更多選項。 | 提供其他建議。 <br> 全都不會。
  SelectItem | 使用者要求選取顯示在螢幕上的專案或結果。 | 選取第三個。 <br> 選取右上方的一個。
 呈報 | 要求客戶服務來處理問題。 | 我可以與個人溝通嗎？
 ReadAloud | 大聲讀出使用者的內容。 | 閱讀此頁面。 <br> 請大聲閱讀。

### <a name="entities"></a>**條目**
LUIS 實體 | 實體類型 | 描述 | 範例
------------|-------------|-------------|---------
ordinal | ordinal | 專案的序數或數值參考。 | **第二個**。 <br> **下一步**。
number | number | 使用者想要的專案數量 | 接下來的**3**個專案
DirectionalReference | 單個 | 專案所在畫面上的參考點。 | 右邊的一個 <br> upper

## <a name="weather"></a>**天氣**
氣象領域著重于檢查天氣條件和諮詢，並提供位置和時間，或依天氣條件檢查時間。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
 CheckWeatherValue | 在預測或過去的資訊上，要求氣象或天氣相關的因素。 <br> 天氣相關的因素包括： <li> 溫度 </li> <li> rain/雪/降雨機率 </li> <li> 幹/濕/濕度 </li> <li> 雲 </li> <li> UV 索引 </li> <li> 雨/雪的釐米 </li> | 北京的空氣品質指數是什麼？ <br> 在3月的西雅圖中，預期有多少 rain？ <br> 記錄夏威夷的最高溫度。
 CheckWeatherTime | 詢問某個地點的預測或歷史天氣相關因素的時間。 | 何時應該是 rain？ <br> 前往加拿大的好時機 <br> 明尼蘇達州中最熱門的月份何時？
 QueryWeather | 詢問特定位置的天氣條件或天氣相關因素，其中有「是，不能或可能」的回應，或詢問取決於氣象條件的活動通知。 | 這是明天的 rain 嗎？ <br> 今天是否 sunny？ <br> 可能太早無法前往阿拉斯加嗎？
 ChangeTemperatureUnit | 變更天氣單位，包括攝氏、開氏和華氏。 | 以攝氏轉換。 <br> 我可以同意嗎？
 GetWeatherAdvisory | 從特定位置取得氣象諮詢或警示。 | 是否有 Memphis 的天氣諮詢？ <br> 我的區域是否有任何氣象警示？

### <a name="entities"></a>**條目**
LUIS 實體 | 實體類型 | 描述 | 範例
------------|-------------|-------------|---------
Location | geography | 氣象要求的絕對或隱含位置。 | Palo Alto<br>上海<br>Seattle<br>Delvina<br>
Date/Time   | datetime | 查詢天氣的日期時間或持續時間。 | 11 月<br>每小時<br>淩晨<br>這個週末<br>10天<br>
AdditionalWeatherCondition | 清單 | 天氣的其他描述文字，例如風的速度或方向。 | 方向<br>快<br>速度
歷史 | 單個 | 描述歷史天氣條件的單字，包括過去一段時間內的平均、邊框大小寫案例。 | 後<br>歷程記錄/歷程<br>季節性<br>最佳時間<br>已記錄
PrecipitationUnit | 維度 | 雪或 rain 的降雨機率。 | 5英寸<br>6 cm
SuitableFor | 單個 | 在氣象條件下的人類活動描述，當使用者根據天氣條件來查詢活動建議時，這是常見的情況。 | jacket<br>umbrella<br>swimming
TemperatureUnit |溫度 | 溫度 | 18攝氏<br>7個開氏度
WeatherRange | 單個 | 一段時間內的溫度、風和其他天氣狀況的特定條件 | 最大值<br>高<br>低<br>平均高<br>最高
WeatherCondition | 單個 | 天氣條件描述 | sunny<br>rain<br>定型<br>溫度<br>snow<br>hot
WindDirectionUnit | 清單 | 風的方向單字 | north<br>中南部<br>遠東<br>west<br>右上


## <a name="web"></a>**Web**
Web 網域提供搜尋網站的意圖和實體。
### <a name="intents"></a>**意圖**
意圖名稱 | 描述 | 範例
---------|----------|---------
 WebSearch | 在搜尋引擎中流覽至指定的網站或搜尋的要求。 | Google.com 中的搜尋介面。 <br> 在網路上尋找快樂的生日歌曲 <br> 移至 www.twitter.com。

### <a name="entities"></a>**條目**
LUIS 實體 | 實體類型 | 描述 | 範例
------------|-------------|-------------|---------
SearchEngine | List | 搜尋引擎使用者想要使用。 | Bing <br> Google
SearchText | 簡單的模式。任何 | 使用者想要搜尋的文字。 <br> _如果 "in" 後面的網站不是搜尋引擎，則將「facebook 中的朋友」標記為 SearchText。Url 也應標記為 SearchText。_ | 電影 <br> 深入學習 <br> Tom 巡航
連結 | url | 網站連結。 | www.twitter.com

