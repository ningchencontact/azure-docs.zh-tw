---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114873"
---
異地備援儲存體 (GRS) 設計為將您的資料複寫到與主要區域相隔數百英哩遠的次要區域，以在指定年份為物件提供至少 99.99999999999999% (16 9's) 的持久性。 如果您的儲存體帳戶已啟用 GRS，即使主要區域發生全區中斷或災害而無法復原的情況，您的資料仍會是永久性。

如果您選擇 GRS，您有兩個相關的選項可供選擇：

* GRS 會將您的資料複寫到次要區域中的另一個資料中心，但如果 Microsoft 起始從主要到次要區域的容錯移轉，該資料則為唯讀。 
* 讀取權限異地備援儲存體 (RA-GRS) 是以 GRS 為基礎。 RA-GRS 會將您的資料複寫到次要區域中的另一個資料中心，也會為您提供從次要區域讀取的選項。 使用 RA-GRS，不論 Microsoft 是否起始從主要到次要區域的容錯移轉，您都可以從次要區域讀取。 

對於已啟用 GRS 或 RA-GRS 的儲存體帳戶，會使用本地備援儲存體 (LRS) 先複寫所有資料。 更新會先認可到主要位置，並使用 LRS 進行複寫。 接著會使用 GRS，以非同步的方式將更新複寫到次要區域。 當資料寫入次要位置時，也會使用 LRS 在該位置中複寫。 

主要和次要區域會管理分散在儲存體縮放單位內不同容錯網域和升級網域之間的複本。 儲存體縮放單位是資料中心內的基本複寫單位。 在此層級的複寫是由 LRS; 提供如需詳細資訊，請參閱[本地備援儲存體 (LRS):適用於 Azure 儲存體的低成本資料備援](../articles/storage/common/storage-redundancy-lrs.md)。

當您決定要使用的複寫選項時，請記住下列幾點：

* 區域備援儲存體 (ZRS) 會利用同步複寫提供高可用性，而且在某些情況下可能是比 GRS 或 RA-GRS 更好的選擇。 如需有關 ZRS 的詳細資訊，請參閱 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)。
* 非同步複寫會涉及從將資料寫入主要區域，到將資料複寫至次要區域這段時間的延遲。 當發生區域性災害時，如果無法從主要區域復原尚未複寫到次要區域的變更，則這些變更可能會遺失。
* 使用 GRS 時，複本不提供讀取或寫入存取，除非 Microsoft 起始對次要區域的容錯移轉。 在容錯移轉的情況下，當容錯移轉完成時，您會有該資料的讀取和寫入存取權。 如需詳細資訊，請參閱[災害復原指導方針](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 如果應用程式需要從次要區域讀取，請啟用 RA-GRS。