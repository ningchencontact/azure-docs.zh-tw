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
ms.openlocfilehash: 28b53864abc37a880a9573cc9657231aff862336
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029757"
---
異地備援儲存體 (GRS) 設計為將您的資料複寫到與主要區域相隔數百英哩遠的次要區域，以在指定年份為物件提供至少 99.99999999999999% (16 9's) 的持久性。 如果您的儲存體帳戶已啟用 GRS，即使主要區域發生全區中斷或災害而無法復原的情況，您的資料仍會是永久性。

如果您選擇 GRS，您有兩個相關的選項可供選擇：

* GRS 會將您的資料複寫到次要區域中的另一個資料中心，但如果 Microsoft 起始從主要到次要區域的容錯移轉，該資料則為唯讀。
* 讀取權限異地備援儲存體 (RA-GRS) 是以 GRS 為基礎。 RA-GRS 會將您的資料複寫到次要區域中的另一個資料中心，也會為您提供從次要區域讀取的選項。 使用 RA-GRS，不論 Microsoft 是否起始從主要到次要區域的容錯移轉，您都可以從次要區域讀取。

對於已啟用 GRS 或 RA-GRS 的儲存體帳戶，會使用本地備援儲存體 (LRS) 先複寫所有資料。 更新會先認可到主要位置，並使用 LRS 進行複寫。 接著會使用 GRS，以非同步的方式將更新複寫到次要區域。 當資料寫入次要位置時，也會使用 LRS 在該位置中複寫。

主要和次要區域會管理分散在儲存體縮放單位內不同容錯網域和升級網域之間的複本。 儲存體縮放單位是資料中心內的基本複寫單位。 此層級的複寫是由 LRS 所提供;如需詳細資訊, [請參閱本機多餘儲存體 (LRS):適用於 Azure 儲存體的低成本資料備援](../articles/storage/common/storage-redundancy-lrs.md)。

當您決定要使用的複寫選項時，請記住下列幾點：

* 異地區域冗余儲存體 (切換) (預覽) 會以同步方式將資料複寫到三個 Azure 可用性區域, 並以非同步方式將資料複寫到次要區域, 藉此提供高可用性和最大耐久性。 您也可以啟用次要區域的讀取權限。 切換的設計目的是要在指定的一年內提供至少 99.99999999999999% (16 個 9) 的物件持久性。 如需切換的詳細資訊, 請參閱[高可用性和最大持久性 (預覽) 的異地區域冗余儲存體](../articles/storage/common/storage-redundancy-gzrs.md)。
* 區域冗余儲存體 (ZRS) 可跨三個 Azure 可用性區域的同步複寫提供高可用性, 而且在某些情況下可能是比 GRS 或 RA-GRS 更好的選擇。 如需有關 ZRS 的詳細資訊，請參閱 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)。
* 非同步複寫會涉及從將資料寫入主要區域，到將資料複寫至次要區域這段時間的延遲。 當發生區域性災害時，如果無法從主要區域復原尚未複寫到次要區域的變更，則這些變更可能會遺失。
* 使用 GRS 時，複本不提供讀取或寫入存取，除非 Microsoft 起始對次要區域的容錯移轉。 在容錯移轉的情況下，當容錯移轉完成時，您會有該資料的讀取和寫入存取權。 如需詳細資訊，請參閱[災害復原指導方針](../articles/storage/common/storage-disaster-recovery-guidance.md)。
* 如果您的應用程式需要從次要區域讀取, 請啟用 [GRS] 或 [RA-切換 (預覽)]。
