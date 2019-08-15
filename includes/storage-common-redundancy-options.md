---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029754"
---
儲存體帳戶的複寫選項包含：

* [本機-多餘儲存體 (LRS)](../articles/storage/common/storage-redundancy-lrs.md):簡單、低成本的複寫策略。 資料會在主要區域內以同步方式複寫三次。
* [區域備援儲存體 (ZRS)](../articles/storage/common/storage-redundancy-zrs.md)：需要高可用性的案例複寫。 資料會在主要區域中的三個 Azure 可用性區域之間同步複寫。
* [異地備援儲存體 (GRS)](../articles/storage/common/storage-redundancy-grs.md)：跨區域複寫, 以防止區域性中斷。 資料會在主要區域中同步複寫三次, 然後以非同步方式複寫到次要區域。 如需次要區域中資料的讀取權限, 請啟用讀取權限異地多餘儲存體 (RA-GRS)。
* [異地區域-多餘儲存體 (切換) (預覽)](../articles/storage/common/storage-redundancy-gzrs.md):針對需要高可用性和最大持久性的案例進行複寫。 資料會在主要區域中的三個 Azure 可用性區域之間同步複寫, 然後以非同步方式複寫到次要區域。 如需次要區域中資料的讀取權限, 請啟用讀取權限異地區域-多餘儲存體 (RA-切換)。
