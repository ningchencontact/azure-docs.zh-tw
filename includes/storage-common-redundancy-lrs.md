---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219024"
---
本機多餘儲存體（LRS）會在單一資料中心內複寫您的資料三次。 LRS 在指定的一年內提供至少 99.999999999% （11個9）的物件持久性。 相較於其他選項，LRS 是成本最低的複寫選項，而且提供的持久性最弱。

如果發生資料中心層級的嚴重損壞（例如，火災或水災），則使用 LRS 的儲存體帳戶中的所有複本可能會遺失或無法復原。 為了降低此風險，Microsoft 建議使用區域冗余儲存體（ZRS）、異地多餘儲存體（GRS）或異地區域冗余儲存體（切換）。

只有在將資料寫入所有三個複本之後，才會成功傳回 LRS 儲存體帳戶的寫入要求。

在下列案例中，您可能會想要使用 LRS：

* 如果您的應用程式儲存的資料，可在發生資料遺失時輕鬆重新建構，則您可以選擇使用 LRS。
* 由於資料控管需求，某些應用程式僅限於在國家/地區內複寫資料。 在某些情況下，針對 GRS 帳戶複寫資料的配對區域可能位於另一個國家/地區。 如需配對區域的詳細資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。
