---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331063"
---
進階儲存體帳戶有下列延展性目標：

| 總帳戶容量 | 本地備援儲存體帳戶總頻寬 |
| --- | --- | 
| 磁碟容量：35 TB <br>快照容量：10 TB | 每秒最多 50 GB (輸入 <sup>1</sup> + 輸出 <sup>2</sup> |

<sup>1</sup> 傳送至儲存體帳戶的所有資料 (要求)

<sup>2</sup> 從儲存體帳戶接收的所有資料 (回應)

如果您針對非受控磁碟使用進階儲存體帳戶，而您的應用程式超過單一儲存體帳戶的延展性目標，您可能會想要移轉至受控磁碟。 如果您不想要移轉至受控磁碟，請將應用程式建置為使用多個儲存體帳戶。 然後將資料分散到那些儲存體帳戶。 例如，如果您想要將 51 TB 的磁碟連結至多個 VM，可將其分散到兩個儲存體帳戶。 單一進階儲存體帳戶的限制是 35 TB。 請務必確認單一進階儲存體帳戶的佈建磁碟決不超過 35 TB。