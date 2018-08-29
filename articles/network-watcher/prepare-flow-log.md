---
title: Azure 網路安全性群組流量記錄變更 | Microsoft Docs
description: 了解 Azure 網路安全性群組流量記錄變更。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180470"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>網路安全性群組流量記錄 - 第 2 版 – 即將進行的變更

網路安全性群組流量記錄格式將從 2018 年 9 月 15 日起變更。 新增的欄位為您提供流量狀態的相關資訊，以及在每個方向傳輸的遞增位元組與封包計數。 剖析流量記錄的應用程式將會受此變更影響，而且必須據以更新。 此文章概略說明變更詳細資料。

## <a name="what-is-changing"></a>變更內容為何？

網路安全性群組流量記錄的版本將從 "版本": 1 變更為 "版本": 2，而且記錄的 *flowTuples* 屬性中也加入了額外欄位。 [第 2 版中的流量格式為何](#how-is-a-flow-modeled-in-version-2)提供有關格式的詳細資料。

### <a name="version-1-flow-tuple-format"></a>第 1 版流量 Tuple 格式

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>第 2 版流量 Tuple 格式

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>此變更何時會發生？

此變更將在美國中西部時間**2018 年 9 月 15 日**起生效。 在公用雲端區域中的推出將在 2018 年 11 月 31 日前完成，屆時將只有第 2 版記錄可用。

## <a name="am-i-affected-by-the-change"></a>我是否會受此變更影響？

若您建置或使用會處理網路安全性群組流量記錄資料的應用程式，則可能會受此變更影響。

### <a name="if-i-use-traffic-analytics"></a>若我使用「流量分析」？

否。 在從第 1 版移轉到第 2 版流量記錄期間，「流量分析」將不會受影響。 即將推出的增強功能將充分發揮第 2 版記錄中提供之額外工作階段的優勢與頻寬資訊。

### <a name="if-im-using-third-party-tooling"></a>若我是使用第三方工具呢？

我們已事先與所有[網路監看員合作夥伴](https://azure.microsoft.com/services/network-watcher)溝通記錄格式中的變更。 連絡您的廠商以取得詳細資料。

### <a name="if-i-have-built-a-custom-application-or-integration"></a>若我已建置自訂用程式或整合？

**是**，您將必須修改您的應用程式以處理新格式的 NSG 流量記錄。

## <a name="what-is-the-new-flow-logging-format"></a>新的流量記錄格式為何？

流量記錄第 2 版包含下列格式的流量 Tuple：

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

下表提供網路安全性群組第 2 版流量 Tuple 的屬性名稱與說明。 您可以在[第 2 版範例事件](#version2sampleevent)中範例記錄。

| 屬性名稱                        | 值           | 說明                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 時間戳記                           | 1493763938      | 對應到流量項目的時間戳記。 使用 UNIX EPOCH 格式。                                                                                       |
| 來源 IP 位址                    | 185.170.185.105 |                                                                                                                                                             |
| 目的地 IP 位址               | 10.2.0.4        |                                                                                                                                                             |
| 來源連接埠                          | 35370           |                                                                                                                                                             |
| 目的地連接埠                     | 23              |                                                                                                                                                             |
| 流量通訊協定                     | T               | **T**: TCP 與 **U**: UDP。                                                                                                                                  |
| 資料流量方向               | I               | **I**：連入流量與 **O**：連出流量。                                                                                                         |
| 流量決策                     | A               | **A**：流量已被允許與 **D**：流量已被拒絕。                                                                                                         |
| 流量態                           | C               | **B**：開始，當流量建立時。 不提供統計資料。 **C**：繼續進行中的流量。 提供 5 分鐘間隔的統計資料。 **E**：結束，當流量終止時。 提供統計資料。                                                                                                                                                        |
| 封包 - 來源到目的地      | 1               | 上次更新之後從來源傳送到目的地的 TCP 與 UDP 封包總數。                                                                  |
| 已傳送位元組數 - 來源到目的地   | 103             | 上次更新之後從來源傳送到目的地的 TCP 與 UDP 封包位元組總數。 封包位元組包括封包標頭與承載。         |
| 已傳送的封包數 - 目的地到來源 | 1               | 上次更新之後從目的地傳送到來源的 TCP 與 UDP 封包總數。                                                                  |
| 已傳送位元組數 - 目的地到來源   | 186             | 上次更新之後從目的地傳送到來源的 TCP 與 UDP 封包位元組總數。 封包位元組包括封包標頭與承載。             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>第 2 版中的流量格式為何？

第 2 版的記錄引進流量狀態。 當起始流量時，會記錄流量狀態 *B*。 流量狀態 *C* 與流量狀態 *E* 是分別標記繼續傳送流量與流量終止的狀態。 *C* 與 *E* 狀態包含流量頻寬資訊。

**範例**：來自 185.170.185.105:35370 與 10.2.0.4:23 間之 TCP 對話的流量 Tuple。

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

### <a name="how-does-the-format-differ-from-version-1"></a>格式與第 1 版有何不同？

在第 1 版中，會在每次建立流量時或嘗試建立 (拒絕案例) 時建立流量 Tuple ["1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"]。

### <a name="how-are-bytes-and-packets-accounted-for"></a>位元組數與封包數佔有多少比例？

針對繼續 *C* 與結束 *E* 流量狀態，位元組與封包計數是從上一個流量 Tuple 記錄開始時的彙總計數。 參考上一個範例對話，已傳輸的封包總數是 1021+52+8005+47 = 9125。 已傳輸的位元組總數是 588096+29952+4610880+27072 = 5256000。

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>若我的應用程式不了解流量頻寬欄位。第 2 版如何影響應用程式？

使用第 1 版網路安全性群組流量記錄的應用程式通常會計算唯一流量數目。 若剖析中沒有任何變更會對流量狀態造成影響，您會看到回報的流量數目不正確地增加。 忽略 *C* 與 *E* 流量狀態中的流量 Tuple 即可計算唯一流量。

## <a name="can-i-control-the-version-format-i-receive"></a>我是否可以控制我收到的版本格式？

否。 啟用及停用流量記錄將不會影響記錄的輸出格式。 從第 1 版到第 2 版記錄的變更將會逐區域發生。 當某個區域從第 1 版升級到第 2 版時，您可能會看到兩種格式的 NSG 流量記錄。 推出完成之後，將只有第 2 版記錄可用。

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>當變更發生時，我是否會同時看到相同網路安全性群組的兩種格式？

是。 在區域內，轉換將會逐 MAC 位址發生。 因為網路安全性群組可以套用到許多機器，您可能會同時看到兩種格式的記錄寫入到儲存體。 記錄將會是第 1 版或第 2 版。

## <a name="will-i-see-duplicate-data"></a>我是否會看到重複的資料？

不會有跨格式的重複流量記錄資料。 當變更發生時，流量將會以第 1 版或第 2 版格式記錄。

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>我如何事先測試新格式？

是。 您可以[下載](https://aka.ms/nsgflowlogsv2blobsample)範例第 2 版流量記錄檔案，以用於測試您的解決方案。

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>網路安全性群組流量記錄的設定與管理方式是否有任何變更？

否。 對跨共用相同 Azure Active Directory 租用戶之訂用帳戶的 Azure 儲存體帳戶支援已在今年稍早[釋出](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/)。 此變更可協助您減少管理網路安全性群組流量記錄時所需的儲存體帳戶數目。

## <a name="questions-or-feedback"></a>有任何問題或意見反應嗎？

我們很期待聽到您對網路安全性群組流量記錄與「網路監看員」體驗的意見反應。 您可以在線上提供意見反應或建議，或透過電子郵件將它寄到 AzureNetworkWatcher@microsoft.com。

## <a name="version-2-sample"></a>第 2 版範例

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>第 1 版範例

```json
{ 

    "records": [ 

        { 

            "time": "2017-02-16T22:00:32.8950000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A", 

                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A", 

                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A", 

                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A" 

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        }, 

        { 

            "time": "2017-02-16T22:01:32.8960000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",

                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",

                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        } 

    ] 
}
```