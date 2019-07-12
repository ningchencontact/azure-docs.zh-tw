---
title: Azure SQL Database 受控執行個體的時區 |Microsoft Docs 」
description: 深入了解 Azure SQL Database 受控執行個體的時區細節
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 07/05/2019
ms.openlocfilehash: 05ec49c98c5bcfe40346550f5570c03a8fb3f881
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657981"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Azure SQL Database 受控執行個體的時區

Coordinated Universal Time (UTC) 是雲端解決方案的資料層建議的時區。 Azure SQL Database 受控執行個體也會提供儲存日期和時間值，並且呼叫具有隱含的內容，針對特定時區的日期和時間函式的現有應用程式的需求所選擇的時區。

T-SQL 的功能，例如[getdate （)](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)或 CLR 程式碼觀察層級的執行個體上設定的時區。 SQL Server Agent 作業也會依照排程，根據執行個體的時區。

  >[!NOTE]
  > 受控執行個體是唯一的部署選項的 Azure SQL Database 支援的時區設定。 其他部署選項永遠會遵循 UTC。
使用[AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)單一和集區 SQL database 中如果您需要將非 UTC 時區的日期和時間資訊。

## <a name="supported-time-zones"></a>支援的時區

一組支援的時區被繼承自基礎作業系統的受管理的執行個體。 它會定期更新以取得新時區的定義，並反映至現有的變更。

[變更原則，日光節約時間/時區](https://aka.ms/time)保證從 2010 轉寄的歷程記錄的精確度。

支援的時區名稱的清單透過公開[sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)系統檢視表。

## <a name="set-a-time-zone"></a>設定時區

只能建立執行個體時，可以設定受管理的執行個體的時區。 預設的時區是 UTC。

  >[!NOTE]
  > 無法變更現有的受控執行個體的時區。

### <a name="set-the-time-zone-through-the-azure-portal"></a>設定在 Azure 入口網站的時區

當您輸入參數的新執行個體時，請從支援的時區清單中選取時區。
  
![在執行個體建立期間設定的時區](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

指定 timezoneId 屬性，在您[Resource Manager 範本](https://aka.ms/sql-mi-create-arm-posh)執行個體建立期間設定的時區。

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

這篇文章結尾處，是一份 timezoneId 屬性支援的值。

如果未指定，時區會設定為 UTC。

## <a name="check-the-time-zone-of-an-instance"></a>檢查執行個體的時區

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)函式會傳回執行個體的時區的顯示名稱。

## <a name="cross-feature-considerations"></a>跨功能的考量

### <a name="restore-and-import"></a>還原和匯入

您可以將備份檔案還原，或匯入資料到受管理的執行個體從執行個體或伺服器使用不同的時區設定。 請務必這樣做時請小心。 就像分析的應用程式行為和結果的查詢和報表，當您使用不同的時區設定的兩個 SQL Server 執行個體之間傳輸資料。

### <a name="point-in-time-restore"></a>還原時間點

<del>當您執行時間點還原時，若要還原的時間會解譯為 UTC 時間。 此設定可避免因日光節約時間和其潛在變更的任何模稜兩可。<del>

 >[!WARNING]
  > 目前的行為並不符合上述陳述式，並將還原至時間解譯根據自動資料庫備份會從擷取的來源受控執行個體的時區。 我們正努力修正此行為，以指定的點解譯為 UTC 時間的時間。 請參閱[已知問題](sql-database-managed-instance-timezone.md#known-issues)如需詳細資訊。

### <a name="auto-failover-groups"></a>自動容錯移轉群組

使用相同的時區，跨越多個容錯移轉群組中的主要和次要執行個體不會強制執行，但我們強烈建議。

  >[!WARNING]
  > 我們強烈建議您在容錯移轉群組中的主要和次要執行個體使用相同的時區。 因為某些罕見的情況下，保留相同的時區，在主要和次要執行個體之間不會強制執行。 請務必了解在使用手動或自動容錯移轉的情況下，為次要執行個體將會保留其原始的時區。

## <a name="limitations"></a>限制

- 無法變更現有的受控執行個體的時區。
- 從 SQL Server Agent 作業啟動的外部處理序沒有觀察執行個體的時區。

## <a name="known-issues"></a>已知問題

當時間點還原 (PITR) 執行作業時，若要還原的時間會根據設定，自動資料庫備份，從受管理的執行個體上，即使入口網站頁面的 PITR 建議時間會解譯為 UTC 的時區解譯。

範例：

假設其中自動備份會從該執行個體設有美加東部標準時間 (UTC-5) 的時區。
入口網站頁面的時間點還原，建議您選擇要還原的時間是 UTC 時間：

![使用入口網站的當地時間的 PITR](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

不過，若要還原的時間實際上解譯為美加東部標準時間，而且在此特定範例資料庫將會還原到 9 的上午美加東部標準時間，並不是 UTC 時間的狀態。

如果您想要以 UTC 時間的時間點還原至特定點時，首先計算的來源執行個體的時區中對等的時間，並使用該入口網站或 PowerShell/CLI 指令碼中的時間。

## <a name="list-of-supported-time-zones"></a>支援的時區清單

| **時區識別碼** | **時區顯示名稱** |
| --- | --- |
| 國際換日線標準時間 | (UTC-12:00) 國際換日線以西 |
| UTC-11 | (UTC-11:00) 國際標準時間-11 |
| 阿留申群島標準時間 | (UTC-10:00) 阿留申群島 |
| 夏威夷標準時間 | (UTC-10:00) 夏威夷 |
| 馬克沙斯標準時間 | (UTC-09:30) 馬克沙斯群島 |
| 阿拉斯加標準時間 | (UTC-09:00) 阿拉斯加 |
| UTC-09 | (UTC-09:00) 國際標準時間-09 |
| 太平洋標準時間 （墨西哥） | (UTC-08:00) 下加利福尼亞州 |
| UTC-08 | (UTC-08:00) 國際標準時間-08 |
| 太平洋標準時間 | (UTC-08:00) 太平洋時間 (美國和加拿大) |
| 美國山區標準時間 | (UTC-07:00) 亞利桑那 |
| 山區標準時間 （墨西哥） | (UTC-07:00) 赤瓦瓦，拉帕茲，馬薩特蘭 |
| 山區標準時間 | (UTC-07:00) 山區時間 (美國和加拿大) |
| 中美洲標準時間 | (UTC-06:00) 中美洲 |
| 中部標準時間 | (UTC-06:00) 中部時間 (美國和加拿大) |
| 伊斯特島標準時間 | (UTC-06:00) 伊斯特島 |
| 中部標準時間 （墨西哥） | (UTC-06:00) 瓜達拉加若，墨西哥市，蒙特利 |
| 加拿大中部標準時間 | (UTC-06:00) 薩克其萬 |
| 南美洲太平洋標準時間 | (UTC-05:00) 波哥大，利馬，基多，里約布蘭科 |
| 美加東部標準時間 （墨西哥） | (UTC-05:00) 切圖馬爾 |
| 美加東部標準時間 | (UTC-05:00) 東部時間 (美國和加拿大) |
| 海地標準時間 | (UTC-05:00) 海地 |
| 古巴標準時間 | (UTC-05:00) 哈瓦那 |
| 美國東部標準時間 | (UTC-05:00) 印地安那 (東部) |
| 土克斯及開科斯群島標準時間 | (UTC-05:00)土克斯及開科斯群島 |
| 巴拉圭標準時間 | (UTC-04:00) 亞松森 |
| 大西洋標準時間 | (UTC-04:00) 大西洋時間 (加拿大) |
| 委內瑞拉標準時間 | (UTC-04:00) 卡拉卡斯 |
| 巴西中部標準時間 | (UTC-04:00) 古雅巴 |
| 南美洲西部標準時間 | (UTC-04:00) 佐治敦，拉帕茲，瑪瑙斯，聖胡安 |
| 太平洋 SA 標準時間 | (UTC-04:00) 聖地牙哥 |
| 紐芬蘭標準時間 | (UTC-03:30) 紐芬蘭 |
| 托坎廷斯標準時間 | (UTC-03:00) 阿拉瓜伊納 |
| E. 南美洲標準時間 | (UTC-03:00) 巴西利亞 |
| 南美洲東部標準時間 | (UTC-03:00) 開雲，福塔力沙 |
| 阿根廷標準時間 | (UTC-03:00) 布宜諾斯艾利斯 |
| 格陵蘭標準時間 | (UTC-03:00) 格陵蘭 |
| 蒙特維多標準時間 | (UTC-03:00) 蒙特維多 |
| 麥哲倫標準時間 | (UTC-03:00)蓬塔阿雷納斯 |
| 聖匹島標準時間 | (UTC-03:00) 聖匹島 |
| 巴伊亞標準時間 | (UTC-03:00) 薩爾瓦多 |
| UTC-02 | (UTC-02:00) 國際標準時間-02 |
| 大西洋標準時間 | (UTC-02:00) 大西洋中部 - 舊 |
| 亞速爾標準時間 | (UTC-01:00) 亞述群島 |
| 維德角標準時間 | (UTC-01:00) 維德角 |
| UTC | (UTC) 國際標準時間 |
| GMT 標準時間 | (UTC+00:00) 都柏林，愛丁堡，里斯本，倫敦 |
| 格林威治標準時間 | (UTC+00:00) 蒙羅維亞，雷克雅維克 |
| W. 歐標準時間 | (UTC+01:00) 阿姆斯特丹，柏林，柏恩，羅馬，斯德哥爾摩，維也納 |
| 中歐標準時間 | (UTC+01:00) 貝爾格勒，布拉提斯拉瓦，布達佩斯，盧布亞納，布拉格 |
| 羅馬標準時間 | (UTC+01:00) 布魯塞爾，哥本哈根，馬德里，巴黎 |
| 摩洛哥標準時間 | （UTC + 01:00）卡薩布蘭卡 |
| 聖多美標準時間 | （UTC + 01:00）聖多美 |
| 歐洲中部標準時間 | (UTC+01:00) 塞拉耶佛，斯高彼亞，華沙，札格雷布 |
| W. 南非標準時間 | (UTC+01:00) 中西非 |
| 約旦標準時間 | (UTC+02:00) 安曼 |
| GTB 標準時間 | (UTC+02:00) 雅典，布加勒斯特 |
| 中東標準時間 | (UTC+02:00) 貝魯特 |
| 埃及標準時間 | (UTC+02:00) 開羅 |
| E. 歐標準時間 | (UTC+02:00) 奇西瑙 |
| 敘利亞標準時間 | (UTC+02:00) 大馬士革 |
| 約旦河西岸地區標準時間 | (UTC+02:00) 加薩，希伯侖 |
| 南非標準時間 | (UTC+02:00) 哈拉雷，普利托里亞 |
| FLE 標準時間 | (UTC+02:00) 赫爾辛基，基輔，里加，索菲亞，塔林，維爾紐斯 |
| 以色列標準時間 | (UTC+02:00) 耶路撒冷 |
| 卡里寧格勒標準時間 | (UTC+02:00) 卡里寧格勒 |
| 蘇丹標準時間 | （UTC + 02:00）喀土穆 |
| 利比亞標準時間 | (UTC+02:00) 的黎波里 |
| 納米比亞標準時間 | （UTC + 02:00）溫吐克 |
| 伊拉克標準時間 | (UTC+03:00) 巴格達 |
| 土耳其標準時間 | （UTC+03:00） 伊伊斯坦堡 |
| 沙烏地阿拉伯標準時間 | (UTC+03:00) 科威特，利雅德 |
| 白俄羅斯標準時間 | (UTC+03:00) 明斯克 |
| 俄羅斯標準時間 | （UTC+03:00） 伊莫斯科，聖彼得堡 |
| E. 非標準時間 | (UTC+03:00) 奈洛比 |
| 伊朗標準時間 | (UTC+03:30) 德黑蘭 |
| 沙烏地阿拉伯標準時間 | (UTC+04:00) 阿布達比，馬斯喀特 |
| 阿斯特拉罕標準時間 | (UTC+04:00) 阿斯特拉罕，烏里揚諾夫斯克 |
| 亞塞拜然標準時間 | (UTC+04:00) 巴庫 |
| 俄羅斯時區 3 | (UTC+04:00) 伊熱夫斯克，沙馬拉 |
| 模里西斯標準時間 | (UTC+04:00) 路易士港 |
| 薩拉托夫標準時間 | （UTC + 04:00）薩拉托夫 |
| 喬治亞標準時間 | (UTC+04:00) 第比利斯 |
| 伏爾加格勒標準時間 | （UTC + 04:00）伏爾加格勒 |
| 高加索標準時間 | (UTC+04:00) 葉里溫 |
| 阿富汗標準時間 | (UTC+04:30) 喀布爾 |
| 西亞標準時間 | (UTC+05:00) 阿什哈巴德，塔什干 |
| 伊卡特林堡標準時間 | (UTC+05:00) 伊卡特林堡 |
| 巴基斯坦標準時間 | (UTC+05:00) 伊斯蘭馬巴德，喀拉蚩 |
| 印度標準時間 | (UTC+05:30) 辰內，加爾各答，孟買，新德里 |
| 斯里蘭卡標準時間 | (UTC+05:30) 斯里加亞渥登普拉 |
| 尼泊爾標準時間 | (UTC+05:45) 加德滿都 |
| 中亞標準時間 | (UTC+06:00) 阿斯塔納 |
| 孟加拉標準時間 | (UTC+06:00) 達卡 |
| 鄂木斯克標準時間 | （UTC+06:00） 伊鄂木斯克 |
| 緬甸標準時間 | (UTC+06:30) 仰光 |
| 東南亞標準時間 | (UTC+07:00) 曼谷，河內，雅加達 |
| 阿爾泰標準時間 | (UTC+07:00) 巴爾瑙爾，戈爾諾-阿爾泰斯克 |
| W. 蒙古標準時間 | (UTC+07:00) 哈霍德 |
| 北亞標準時間 | (UTC+07:00) 克拉斯諾亞爾斯克 |
| N。 中亞標準時間 | （UTC + 07:00）諾曼斯比爾斯科 |
| 托木斯克標準時間 | (UTC+07:00) 托木斯克 |
| 中國標準時間 | (UTC+08:00) 北京，重慶，香港，烏魯木齊 |
| 東北亞標準時間 | (UTC+08:00) 伊爾庫次克 |
| 新加坡標準時間 | (UTC+08:00) 吉隆坡，新加坡 |
| W. 澳洲標準時間 | (UTC+08:00) 伯斯 |
| 台北標準時間 | (UTC+08:00) 台北 |
| 庫倫標準時間 | (UTC+08:00) 庫倫 |
| 澳洲中西部標準時間 | (UTC+08:45) 尤克拉 |
| 外貝加爾標準時間 | (UTC+09:00) 赤塔 |
| 東京標準時間 | (UTC+09:00) 大阪，札幌，東京 |
| 北韓標準時間 | （UTC + 09:00）平壤 |
| 韓國標準時間 | (UTC+09:00) 首爾 |
| 亞庫茲克標準時間 | (UTC+09:00) 雅庫茨克 |
| Cen。 澳洲標準時間 | (UTC+09:30) 阿得雷德 |
| 澳洲中部標準時間 | (UTC+09:30) 達爾文 |
| E. 澳洲標準時間 | (UTC+10:00) 布利斯班 |
| 澳洲東部標準時間 | (UTC+10:00) 坎培拉，墨爾本，雪梨 |
| 西太平洋標準時間 | (UTC+10:00) 關島，莫爾斯貝港 |
| 塔斯馬尼亞標準時間 | (UTC+10:00) 霍巴特 |
| 海參威標準時間 | (UTC+10:00) 海參崴 |
| 羅豪標準時間 | (UTC+10:30) 羅豪島 |
| 布干維爾標準時間 | (UTC+11:00) 布干維爾島 |
| 俄羅斯時區 10 | (UTC+11:00) 喬庫爾達赫 |
| 馬加丹標準時間 | (UTC+11:00) 馬加丹 |
| 諾福克標準時間 | (UTC+11:00) 諾福克島 |
| 庫頁島標準時間 | (UTC+11:00) 庫頁島 |
| 太平洋中部標準時間 | (UTC+11:00) 索羅門群島，新喀里多尼亞群島 |
| 俄羅斯時區 11 | (UTC+12:00) 阿納底，彼得保羅夫斯克-堪察加斯克 |
| 紐西蘭標準時間 | (UTC+12:00) 奧克蘭，威靈頓 |
| UTC+12 | (UTC+12:00) 國際標準時間+12 |
| 斐濟標準時間 | (UTC+12:00) 斐濟 |
| 勘察加標準時間 | (UTC+12:00) 彼得保羅夫斯克-堪察加斯克 - 舊 |
| 查塔姆群島標準時間 | (UTC+12:45) 查塔姆群島 |
| UTC+13 | （UTC + 13:00）國際標準時間 + 13 |
| 東加標準時間 | (UTC+13:00) 努瓜婁發 |
| 薩摩亞標準時間 | (UTC+13:00) 薩摩亞 |
| 來因群島標準時間 | (UTC+14:00) 刻里提瑪斯島 |

## <a name="see-also"></a>另請參閱 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
