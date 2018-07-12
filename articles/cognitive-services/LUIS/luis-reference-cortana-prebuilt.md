---
title: Cortana 預先建置應用程式參考 | Microsoft Docs
description: 適用於 Cortana 個人助理的參考，此為基於 Language Understanding Intelligent Service (LUIS) 的預先建置應用程式。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368203"
---
# <a name="cortana-prebuilt-app-reference"></a>Cortana 預先建置應用程式參考
此參考會列出 Cortana 預先建置應用程式可辨識的意圖和實體。

## <a name="cortana-prebuilt-intents"></a>Cortana 預先建置意圖
此預先建置個人助理應用程式可以識別下列意圖：

| 意圖 | 範例語句 |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|將我設為 7:30 的鬧鐘更新為八點 <br/>將我的鬧鐘從上午 8 點變更為上午 9 點 |
| builtin.intent.alarm.delete_alarm|刪除鬧鐘 <br/>刪除我的「喚醒」鬧鐘|
| builtin.intent.alarm.find_alarm|我的喚醒鬧鐘設定的時間為何？ <br/> 我的喚醒鬧鐘是否已開啟？ |
| builtin.intent.alarm.set_alarm|開啟我的喚醒鬧鐘<br/>你可以將鬧鐘設定為 12 點並將它命名為「吃抗生素」嗎？|
| builtin.intent.alarm.snooze|使鬧種延遲 5 分鐘<br/>使鬧鐘延遲|
| builtin.intent.alarm.time_remaining| 距離「喚醒」還有多少時間？ <br/> 距離下一個鬧鐘還有多少時間？|
| builtin.intent.alarm.turn_off_alarm | 關閉我的上午 7 點鬧鐘 <br/> 關閉我的喚醒鬧鐘 |
| builtin.intent.calendar.change_calendar_entry| 變更約會<br/>將我的會議從今天移到明天|
|builtin.intent.calendar.check_availability|Tim 星期五忙嗎？<br/>Brian 星期六有空嗎|
|builtin.intent.calendar.connect_to_meeting|連線到會議<br/>加入線上會議|
|builtin.intent.calendar.create_calendar_entry|我需要在星期五排程與 Tony 的約會<br/>在星期日的下午 2 點安排與 Lisa 的約會|
|builtin.intent.calendar.delete_calendar_entry|刪除我的約會<br/>從我的行事曆移除明天下午 3 點的會議|
|builtin.intent.calendar.find_calendar_entry|顯示我的行事曆<br/>顯示我的每週行事曆|
|builtin.intent.calendar.find_calendar_when|我與 Jon 的下一次會議是在什麼時候？<br/>我與 Larry 在星期一的約會是什麼時間？|
|builtin.intent.calendar.find_calendar_where|顯示我 6 點鐘會議的地點<br/>與 Jon 的會議是在哪裡舉行？|
|builtin.intent.calendar.find_calendar_who|這個會議的對象是誰？<br/>還有誰受到邀請？|
|builtin.intent.calendar.find_calendar_why|我 11 點會議的詳細資料為何？<br/>與 Jon 的會議內容是關於什麼？|
|builtin.intent.calendar.find_duration|我的下一個會議有多長<br/>我今天下午會議的長度是幾分鐘|
|builtin.intent.calendar.time_remaining|距離我下一個約會還有多少時間？<br/>在約會開始之前，我還有多少時間？|
|builtin.intent.communication.add_contact|儲存此號碼並將名字設為 Jose<br/>將 Jason 放入我的連絡人清單|
|builtin.intent.communication.answer_phone|接聽<br/>接聽來電|
|builtin.intent.communication.assign_nickname|編輯 Nickolas 的暱稱<br/>加入 John Doe 的暱稱|
|builtin.intent.communication.call_voice_mail|語音信箱<br/>撥打語音信箱|
|builtin.intent.communication.find_contact|顯示我的連絡人<br/>尋找連絡人|
|builtin.intent.communication.forwarding_off|停止轉接我的來電<br/>關閉來電轉接|
|builtin.intent.communication.forwarding_on|設定來電轉接以將來電轉接到住家電話<br/>將來電轉接到住家電話|
|builtin.intent.communication.ignore_incoming|不要接聽該通來電<br/>現在不要，我很忙|
|builtin.intent.communication.ignore_with_message|不接聽該通來電但改為傳送訊息<br/>忽略並回傳簡訊|
|builtin.intent.communication.make_call|撥電話給 Bob 和 John<br/>撥電話給媽媽和爸爸|
|builtin.intent.communication.press_key|撥打米字鍵<br/>按 1 2 3|
|builtin.intent.communication.read_aloud|朗讀簡訊<br/>她在訊息中說了什麼|
|builtin.intent.communication.redial|重撥我的上一通電話<br/>重撥|
|builtin.intent.communication.send_email|傳送電子郵件給 Mike Waters 表示上週的晚餐很棒<br/>傳送電子郵件給 Bob|
|builtin.intent.communication.send_text|傳送簡訊給 Bob 和 John<br/>Message|
|builtin.intent.communication.speakerphone_off|關閉擴音器<br/>關閉免持聽筒|
|builtin.intent.communication.speakerphone_on|免持聽筒模式<br/>開啟免持聽筒|
|builtin.intent.mystuff.find_attachment|尋找 John 昨天透過電子郵件寄給我的文件 <br/>尋找 John 所提供的文件|
|builtin.intent.mystuff.find_my_stuff|我想要編輯昨天的購物清單<br/>尋找我九月份所做的化學筆記
|builtin.intent.mystuff.search_messages|開啟訊息 <br/>上限|
|builtin.intent.mystuff.transform_my_stuff|與我的丈夫共用我的購物清單<br/>刪除我的購物清單|
|builtin.intent.ondevice.open_setting|開啟 Cortana 設定 <br/>跳至通知|
|builtin.intent.ondevice.pause|關閉音樂<br/>音樂關閉|
|builtin.intent.ondevice.play_music|我想要聆聽 Owner of a Lonely Heart<br/>播放一些福音音樂|
|builtin.intent.ondevice.recognize_song|告訴我這是哪一首歌<br/>分析並擷取歌曲名稱|
|builtin.intent.ondevice.repeat|重複這首曲目<br/>再播一次這首歌曲|
|builtin.intent.ondevice.resume|重新開始音樂<br/>再次開始音樂|
|builtin.intent.ondevice.skip_back|播上一首曲目<br/>上一首歌曲|
|builtin.intent.ondevice.skip_forward|下一首歌曲<br/>播下一首曲目|
|builtin.intent.ondevice.turn_off_setting|關閉 WiFi<br/>停用飛航模式|
|builtin.intent.ondevice.turn_on_setting|開啟 WiFi<br/>開啟藍芽|
|builtin.intent.places.add_favorite_place|將此地址新增至我的最愛<br/>將此位置儲存至我的最愛|
|builtin.intent.places.book_public_transportation|購買火車票<br/>訂購電車通行券|
|builtin.intent.places.book_taxi|你現在可以幫我找一輛車嗎？<br/>尋找計程車|
|builtin.intent.places.check_area_traffic|520 公路上的交通狀況如何<br/>I-5 公路上的交通狀況如何|
|builtin.intent.places.check_into_place|登記至 Joya<br/>在這裡登記|
|builtin.intent.places.check_route_traffic|顯示前往 Kirkland 途中的交通狀況<br/>到 Seattle 的交通狀況如何？|
|builtin.intent.places.find_place|我住在哪裡 <br/>為我提供前 3 家最熱門的日本餐廳|
|builtin.intent.places.get_address|顯示 Guu 在 Robson Street 上的地址<br/>最接近的 Starbucks 在哪裡？|
|builtin.intent.places.get_coupon|尋找我所在區域的電視優惠<br/>Mountain View 區域的折扣|
|builtin.intent.places.get_distance|Holiday Inn 有多遠？<br/>到 Tahoe 的距離有多遠|
|builtin.intent.places.get_hours|Bar Del Corso 星期一的營業時間為何？<br/>圖書館何時開放？|
|builtin.intent.places.get_menu|顯示 Applebee 的菜單<br/>Sizzler 的菜單內容為何|
|builtin.intent.places.get_phone_number|提供 Home Depot 的號碼<br/>最接近的 Starbucks 電話號碼為何？|
|builtin.intent.places.get_price_range|Red Lobster 的晚餐費用是多少<br/>Purple Cafe 有多貴|
|builtin.intent.places.get_reviews|顯示有關當地五金行的評論<br/>我想要查看餐廳評論|
|builtin.intent.places.get_route|給我路線指引|是否可以步行到 Pizza Hut|
|builtin.intent.places.get_star_rating|French Laundry 有多少顆星？<br/>Monterrey 的水族館評價好嗎？|
|builtin.intent.places.get_transportation_schedule|San Francisco 渡輪何時啟航？<br/>前往 Seattle 的最後一班火車是什麼時候？|
|builtin.intent.places.get_travel_time|從 SF 開車到 Denver 需要多久的時間<br/>從這裡到 San Francisco 需要多久的時間|
|builtin.intent.places.make_call|撥電話給位於 Bellevue 的 Dr. Smith<br/>撥電話給位於 1st Street 的 Home Depot|
|builtin.intent.places.rate_place|為此餐廳評等<br/>在 Yelp 上給予 Il Fornaio 5 顆星的評價|
|builtin.intent.places.show_map|我目前的位置在哪裡 <br/>我的位置在哪裡|
|builtin.intent.places.takes_reservations|是否可以預訂 Olive Garden 的座位<br/>這家美術館是否接受預約|
|builtin.intent.reminder.change_reminder|變更提醒<br/>將我的拍照日提醒提前 30 分鐘|
|builtin.intent.reminder.create_single_reminder|提醒我在上午 7 點起床<br/>提醒我在下午 4:40 要和 Luca 一起去參加萬聖節活動|
|builtin.intent.reminder.delete_reminder|刪除此提醒<br/>刪除我的圖片提醒|
|builtin.intent.reminder.find_reminder|我今天有設定任何提醒嗎<br/>我有為 Luca 的派對設定提醒嗎|
|builtin.intent.reminder.read_aloud|將提醒大聲讀出來<br/>將該提醒讀出來|
|builtin.intent.reminder.snooze|將該提醒延遲到明天<br/>延遲此提醒|
|builtin.intent.reminder.turn_off_reminder|關閉提醒<br/>取消機場接機提醒|
|builtin.intent.weather.change_temperature_unit|從華氏溫度變更為克耳文溫度<br/>從華氏溫度變更為攝氏溫度|
|builtin.intent.weather.check_weather|針對即將到來的西雅圖行程顯示氣象預報<br/>這個週末的天氣如何|
|builtin.intent.weather.check_weather_facts|夏威夷 12 月的天氣是怎麼樣？<br/>去年此時的溫度是幾度？|
|builtin.intent.weather.compare_weather|提供德州 Dallas 和 Houston 的最高溫與最低溫比較<br/>和 SLC 和 NYC 相比天氣如何|
|builtin.intent.weather.get_frequent_locations|提供我最常前往的地點<br/>顯示最常停駐的地點|
|builtin.intent.weather.get_weather_advisory|天氣警告<br/>顯示 Sacramento 的天氣預報|
|builtin.intent.weather.get_weather_maps|顯示天氣圖<br/>顯示非洲的天氣圖|
|builtin.intent.weather.question_weather|明天早上會有霧嗎？<br/>這個週末我需要鏟雪嗎？|
|builtin.intent.weather.show_weather_progression|顯示當地的天氣雷達圖<br/>顯示雷達圖|
|builtin.intent.none|你幾歲<br/>開啟相機|

## <a name="prebuilt-entities"></a>預先建置實體 

以下是預先建置個人助理應用程式可以識別的一些實體範例：

|實體 |語句中的實體範例 |
|-------|------------------|
|builtin.alarm.alarm_state | `off` 我的喚醒鬧鐘    <br/> 我的喚醒鬧鐘是否 `on`  | 
|builtin.alarm.duration |延遲 `10 minutes`    <br/> 使鬧鐘延遲 `5 minutes`  |
|builtin.alarm.start_date | 將鬧鐘設定為 `monday` 上午 7 點   <br/> 將鬧鐘設定為 `tomorrow` 中午   |
|builtin.alarm.start_time | 建立會在 `30 minutes` 後響起的鬧鐘    <br/> 將鬧鐘設定為在 `in 20 minutes` 後響起   |
|builtin.alarm.title | 我的 `wake up` 鬧鐘是否已開啟 <br/> 可以設定一個會於星期一到星期五的 11 點 45 分響起，並名為 `take antibiotics` 的鬧鐘嗎 |
|builtin.calendar.absolute_location | 建立明天在 `123 main street` 的約會   <br/> 此會議將在 6 月 5 日於 `cincinnati` 舉行    |
|builtin.calendar.contact_name|在我的行事曆上標示一個行銷會議，並確定 `joe` 會參加 <br/>我想要在 Il Fornaio 安排午餐並邀請 `paul` |   
|builtin.calendar.destination_calendar|將此項目加入我的 `work` 排程   <br/>將此項目放入我的 `personal` 行事曆 |
|builtin.calendar.duration| 在今天晚上 6 點安排為時 `an hour` 的約會 <br/>  預約與 Joe 進行為時 `2 hour` 的會議 |  
|builtin.calendar.end_date | 建立一個名為休假的行事曆項目，時間從明天到 `next monday` <br/>    以忙碌狀態封鎖我的時間直到 `monday, october 5th` | 
|builtin.calendar.end_time | 會議會在 `5:30 PM` 結束 <br/> 將它排程為從 11 點到 `noon`  |   
|builtin.calendar.implicit_location | 取消在 DMV 的約會 <br/> 將 Miles 的生日地點改為 `poppy restaurant` |    
|builtin.calendar.move_earlier_time | 將會議往前推 `an hour` <br/> 將我的牙醫預約往前移 `30 minutes`       |
|builtin.calendar.move_later_time | 將我的牙醫預約往後移 `30 minutes`    <br/> 將會議往後推 `an hour` |  
|builtin.calendar.original_start_date | 將我與美髮師的預約從 'Tuesday' 重新排程到星期三 <br/> 將我與 Ken 的會議從 `monday` 移到星期二 |
|builtin.calendar.original_start_time | 將我的會議從 `2:00` 重新排程到 3 點  <br/> 將我的牙醫預約從 `3:30` 變更為 4 點 |  
|builtin.calendar.start_date | 我的派對會在 `flag day` 的什麼時間開始 <br/> 將午餐排程在 `friday after next` 的中午 
|builtin.calendar.start_time | 我想要將它排程在 `this morning` <br/> 我想要將它排程在 `morning` |  
|builtin.calendar.title | `vet appointment`  <br/> 星期二 `dentist` |
|builtin.communication.audio_device_type | 使用 `bluetooth` 撥打電話  <br/> 使用我的 `headset` 撥號 | 
|builtin.communication.contact_name | 發簡訊給 `bob jones`  <br/> | 撥電話給 `sarah`|
|builtin.communication.destination_platform | 撥電話給位於 `london` 的 Dave <br/> 撥打他的`work line` |    
|builtin.communication.from_relationship_name | 顯示我 `daughter` 的來電 <br/> 讀取來自 `mom` 的電子郵件   |   
|builtin.communication.key | 撥打 `star` <br/>  按 `hash` 鍵    |
|builtin.communication.message | 寄送電子郵件給 Carly，表示 `i'm running late` <br/> 發送簡訊給 Angus Smith，表示 `good luck on your exam` |    
|builtin.communication.message_category | 標記為 `follow up` 的新電子郵件  <br/> 標記為 `high priority` 的新電子郵件 |    
|builtin.communication.message_type | 傳送 `email`   <br/> 將我的 `text` 訊息大聲讀出來 |
|builtin.communication.phone_number | 我想要撥打 `1-800-328-9459` <br/>     撥打 `555-555-5555` |   
|builtin.communication.relationship_name | 發送簡訊給我的 `husband` <br/>  傳送電子郵件給 `family`| 
|builtin.communication.slot_attribute | 變更 `recipient` <br/>    變更 `text` | 
|builtin.comminication.source_platform | 從 `skype` 撥電話給他 <br/> 從我的 `personal line` 撥電話給他 |
|builtin.mystuff.attachment| `attached` 文件 <br/> 尋找 Bob 傳送的電子郵件 `attachment` |
|builtin.mystuff.contact_name| 尋找 Lisa 傳送給 `me` 的試算表 <br/> 我昨晚傳送給 `susan` 的文件在哪裡 |
|builtin.mystuff.data_source | `c:\dev\` <br/> 我的 `desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | 找出我昨晚處理的 `document` <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> 顯示 Mike 的 `visio diagram`  |
|builtin.mystuff.end_date| 顯示我在 `between yesterday and today` 處理的文件   <br/> 找出我在 `before thursday the 31st` 處理的文件 |
|builtin.mystuff.end_time| 尋找我在 `before noon` 儲存的檔案 <br/> 尋找我在 `before 4pm` 處理的文件|      
|builtin.mystuff.file_action| 開啟我昨天 `saved` 的試算表 <br/> 尋找 Kevin `created` 的試算表| 
|builtin.mystuff.from_contact_name | 尋找 `jason` 傳送給我的提案 <br/> 開啟 `isaac` 的最後一封電子郵件 |
|builtin.mystuff.keyword | 顯示 `french conjugation` 檔案 <br/> 尋找我昨天草擬的 `marketing plan` |
|builtin.mystuff.location | 我在 `work` 編輯的文件 <br/> 我在 `paris` 拍攝的相片 |
|builtin.mystuff.message_category | 尋找我的 `new` 電子郵件 <br/> 搜尋我的 `high priority` 電子郵件 |
|builtin.mystuff.message_type | 檢查我的 `email` <br/>  顯示我的 `text` 訊息  |
|builtin.mystuff.source_platform | 在我的 `hotmail` 電子郵件中尋找 John 所傳送的電子郵件    <br/> 尋找我從 `work` 傳送的文件 |
|builtin.mystuff.start_date | 尋找 `january` 的筆記 <br/> 尋找我在 `after january 1st` 傳送給 Rob 的電子郵件 |
|builtin.mystuff.start_time | 尋找我在下午 2 點前及 `after noon` 傳送給 Rob 的那封電子郵件？ <br/> 尋找 Kistin 傳送給我且我已在 `last night` 編輯過的試算表 | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | `download` John 傳送給我的檔案 <br/> `open` 我的註釋指導方針文件 |
|builtin.note.note_text | 建立包括 `pork chops, applesauce and milk` 的雜貨清單 <br/> 記下要 `buy milk` |
|builtin.note.title | 記下名為 `grocery list` 的便條 <br/> 記下名為 `people to call` 的便條 |
|builtin.ondevice.music_artist_name | 播放 `rufus wainwright` 的全部內容 <br/> 播放 `garth brooks` 的音樂 |   
|builtin.ondevice.music_genre | 顯示 `classic rock` 歌曲 <br/> 播放我的巴洛克時期 `classical` 音樂 |
|builtin.ondevice.music_playlist | 從 `workout` 播放清單中隨機播放所有 Britney Spears 的歌 <br/> 播放 `breakup` 播放清單
|builtin.ondevice.music_song_name | 播放 `summertime` <br/> 播放 `me and bobby mcgee` | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | 帶我到 `5th and pike` 的交叉路口 <br/> 否，我需要前往 `1 microsoft way redmond wa 98052` 的路線 |
|builtin.places.atmosphere | 尋找可以去的 `interesting` 地方    <br/> 我可以在哪裡找到 `casual` 餐廳 |  
|builtin.places.audio_device_type |以 `hands free` 撥電話給郵局 <br/> 使用 `speakerphone` 撥電話給 Papa John's |    
|builtin.places.avoid_route | 避開 `toll road` <br/> 帶我前往 San Francisco 並避開 `construction on 101` |  
|builtin.places.cuisine | Mountain View 附近的 `halal` 熟食   <br/> 位於半島上的 `kosher` 美食 |
|builtin.places.date | 預訂 `next friday the 12th` 的座位 <br/>  Mashiko `mondays` 有營業嗎？ |
|builtin.places.discount_type | 尋找 Macy 的 `coupon` <br/> 幫我找一張 `coupon` |
|builtin.places.distance | `within 5 miles` 有沒有好吃的簡餐店 <br/> 尋找所有 `within 15 miles` 的地點 |   
|builtin.places.from_absolute_location | 從 `45 elm street` 到住家的路線 <br/> 幫我取得從 `san francisco` 到 Palo Alto 的路線  |
|builtin.places.from_place_name | 從 `post office` 開車到 56 Center Street <br/> 幫我取得從 `home depot` 到 Lowes 的路線 |
|builtin.places.from_place_type | 從 `work` 到市中心的路線 <br/>  幫我取得從 `drug store` 到住家的路線 |
|builtin.places.meal_type | 附近適合吃 `dinner` 的地方 <br/> 找個適合吃商務 `lunch` 的好地方 | 
|builtin.places.nearby | 顯示我 `near` 一些很酷的商店  <br/> `around` 是否有任何很棒的黎巴嫩餐廳？ |
|builtin.places.open_status | 此商場何時 `closed` <br/> 告訴我商店的 `opening` 時間 | 
|builtin.places.place_name | 帶我到 `central park` <br/> 查詢 `eiffel tower` |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.prefer_route | 用 `shortest` 的路徑顯示路線 <br/> 走 `fastest` 的路線 | 
|builtin.places.price_range| 提供一些 `moderately affordable` 的地方 <br/>  我想要找比較 `expensive` 的   |
|builtin.places.product | 這附近哪裡可以買到 `fresh fish`  <br/> 這附近哪裡有賣 `bare minerals` |
|builtin.places.public_transportation_route | 取得 `m2` 公車的公車時刻表 <br/> 公車路線 `3x` |  
|builtin.places.rating | 顯示 `3 star` 餐廳 <br/> 顯示 `3 stars or higher` 的結果  |
|builtin.places.reservation_number | 預約 `seven` 個人的桌子 <br/>  在 Il Fornaio 預訂 `two` 人座位 |
|builtin.places.results_number | 顯示離這裡最近的 `10` 家咖啡廳 <br/> 顯示最熱門的前 `3` 家水族館  
|builtin.places.service_provided | 我可以搭公車到哪裡去 `whale watch` <br/> 我需要一個技工來 `fix my brakes` |    
|builtin.places.time | 我需要會在星期六 `8 am` 開放的地方| Mashiko `now` 有營業嗎？ |
|builtin.places.transportation_company | `new jersey transit` 的火車時刻表 <br/> 我可以搭乘 `bart` 到那裡嗎？ | 
|builtin.places.transportation_type | 我可以 `on foot` 抵達的唱片行在哪裡？ <br/>  給我 `biking` 到 Mashiko 的路線|
|builtin.places.travel_time | 我希望開車的時間能 `less than 15 minutes` <br/>   我想要 `under 15 minutes` 可到達的地方 |   
|builtin.reminder.absolute_location | 當我抵達 `chicago` 時提醒我打電話給爸爸 <br/> 當我回到 `seattle` 時，提醒我要加油 |
|builtin.reminder.contact_name | 當 `bob` 來電時，提醒我告訴他這個玩笑 <br/> 當我和 `arthur` 交談時，提醒我提一下校車的事 |
|builtin.reminder.leaving_absolute_location | 離開 `1200 main` 時，提醒我要接 Craig |
|builtin.reminder.leaving_implicit_location | 當我離開 `work` 時，提醒我要加油  |
|builtin.reminder.original_start_date | 將有關草坪的提醒從 `saturday` 變更為星期日 <br/> 將有關學校的提醒從 `monday` 移到星期二   |
|builtin.reminder.relationship_name | 當我的 `husband` 來電時，提醒我他告訴有關 PTA 會議的事 <br/> 當 `mom` 來電時，再提醒我一次|
|builtin.reminder.reminder_text | 可以在 Dr. Smith 來電時提醒我 `bring up my small spot of patchy skin` 嗎  <br/> 在 4:40 提醒我 `pick up dry cleaning`   |
|builtin.reminder.start_date | 在 `thursday after next` 的下午 8 點提醒我  <br/> 在 `next thursday the 18th` 的下午 8 點提醒我    |
|builtin.reminder.start_time | `in 30 minutes`建立提醒 <br/> 建立提醒要在 `this evening at 7` 為植物澆水 |  
|builtin.weather.absolute_location | `boston` 會下雨嗎 <br/> `seattle` 的天氣預報為何？  |
|builtin.weather.date_range | NYC `this weekend` 的天氣 <br/>   查看佛羅里達州 Hollywood 的 `five day` 天氣預報 |
|builtin.weather.suitable_for | 我這個週末可以穿短褲去 `hiking` 嗎？   <br/> 今天適合 `walk` 去觀賞比賽嗎？ | 
|builtin.weather.temperature_unit | 今天以 `kelvin` 顯示的溫度為何？   <br/> 以 `celsius` 顯示溫度 |  
|builtin.weather.time_range | `tonight` 看起來會下雪嗎？ <br/> `now` 風很大嗎？  |
|builtin.weather.weather_condition | 顯示 `precipitation` <br/> Lake Tahoe 的 `snow` 現在有多厚？  |

