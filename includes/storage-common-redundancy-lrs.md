---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 32032f729283cb3f2a786412b563fdee88ba4c8a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238562"
---
本地備援儲存體 (LRS) 可提供在指定一年中至少 99.999999999% (11 個九) 的物件耐久性。 LRS 是透過將資料複寫至儲存體縮放單位來提供此物件耐久性。 位於您建立儲存體帳戶所在區域的資料中心會裝載該儲存體縮放單位。 只有在將資料寫入所有複本之後，才會成功傳回 LRS 儲存體帳戶的寫入要求。 每個複本均位於儲存體縮放單位內不同的容錯網域和升級網域中。

儲存體縮放單位是儲存體節點機架的集合。 容錯網域 (FD) 是由代表實體失敗單位的節點所組成的群組。 您可將容錯網域想像成屬於同一個實體機架的節點。 升級網域 (UD) 是一組在服務升級 (首度發行) 過程中一起升級的節點。 複本會分散於某一儲存體縮放單位內的 UD 和 FD 之間。 此架構可確保即使硬體故障而影響單一機架，或是在服務升級期間升級節點時，資料仍然可以使用。

相較於其他選項，LRS 是成本最低的複寫選項，而且提供的持久性最弱。 如果發生資料中心層級災害 (例如，火災或水災)，可能會遺失或無法復原所有複本。 若要降低此風險，Microsoft 建議使用區域備援儲存體 (ZRS) 或異地備援儲存體 (GRS)。

* 如果您的應用程式儲存的資料，可在發生資料遺失時輕鬆重新建構，則您可以選擇使用 LRS。
* 由於資料控管需求，某些應用程式限制只能在國家/地區中複寫資料。 在某些情況下，針對 GRS 帳戶所複寫資料的配對區域，可能會位在另一個國家/地區。 如需配對區域的詳細資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。
