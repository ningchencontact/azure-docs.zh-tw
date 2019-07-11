---
title: 延展性和效能-Personalizer
titleSuffix: Azure Cognitive Services
description: 高效能且高流量的網站和應用程式有兩個考慮使用個人化工具以獲得延展性和效能的主要因素：延遲和訓練輸送量。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 06c2e65c723e18acc515dd7effc61aae0564f411
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722430"
---
# <a name="scalability-and-performance"></a>延展性和效能

高效能且高流量的網站和應用程式有兩個考慮使用個人化工具以獲得延展性和效能的主要因素：

* 進行排名 API 呼叫時保持低延遲
* 確定訓練輸送量能跟上事件輸入的速度

個人化將大多數的呼叫持續時間投注於透過 REST API 通訊，因此可非常快速地傳回排名。 Azure 會自動調整能力，以快速回應要求。

##  <a name="low-latency-scenarios"></a>低延遲案例

傳回排名時，有些應用程式需要低延遲。 一定要這麼做才能：

* 讓使用者在顯示已排名的內容之前，不需等候很久的時間。
* 協助遇到大量流量的伺服器避免受限於不足的計算時間和網路連線。

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>延展性和訓練輸送量

個人化工具的運作方式是在排名和獎勵 API 之後，根據個人化工具以非同步方式傳送的訊息，更新已重新定型的模型。 這些訊息會使用應用程式的 Azure 事件中樞進行傳送。

 不可能大部分的應用程式都會達到個人化工具的最大聯結和訓練輸送量。 達到此最大值並不會讓應用程式變慢，這意味著事件中樞佇列在內部填滿的速度比其清除的速度還要快。

## <a name="how-to-estimate-your-throughput-requirements"></a>如何估計您的輸送量需求

* 預估每個排名事件的平均位元組數，並將內容和動作 JSON 文件的長度相加。
* 20 MB/秒除以此估計平均位元組數。

例如，如果您的平均承載有 500 項功能，而每一項都估計有 20 個字元，則每個事件大約為 10 kb。 使用這些估計值，20,000,000 / 10,000 = 2,000 個事件/秒，即每天大約 1.73 億事件。 

如果您已達到這些限制，請連絡支援小組以取得架構建議。

## <a name="next-steps"></a>後續步驟

[建立和設定個人化工具](how-to-settings.md)。