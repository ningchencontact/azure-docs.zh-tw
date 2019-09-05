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
ms.openlocfilehash: b7fddce8f682bc341b361a47f8e083cc281e90aa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309580"
---
區域備援儲存體 (ZRS) 會以同步方式，將您的資料複寫到單一區域中的三個儲存體叢集。 每個儲存體叢集實際上都與其他叢集分隔且位在自己的可用性區域 (AZ) 中。 每個可用性區域&mdash;及其中的 ZRS 叢集&mdash;都是自發的，且包含個別的工具和網路功能。 只有在將資料寫入至三個叢集的所有複本之後，才會成功傳回 ZRS 儲存體帳戶的寫入要求。

當您使用 ZRS 複寫將資料儲存在儲存體帳戶中時，如果有可用性區域變得無法使用，您仍然可以繼續存取和管理您的資料。 ZRS 的效能優異且延遲性低。 ZRS 提供與[本地備援儲存體 (LRS)](../articles/storage/common/storage-redundancy-lrs.md) 相同的[延展性目標](../articles/storage/common/storage-scalability-targets.md)。

針對需要一致性、耐久性和高可用性的案例，請考慮使用 ZRS。 在給定的一年中，即使發生中斷或自然災害使可用性區域變得無法取得，ZRS 仍提供至少 99.9999999999% (12 個 9) 的儲存體物件耐久性。

異地區域冗余儲存體（切換）（預覽）會在主要區域中的三個 Azure 可用性區域之間同步複寫您的資料，然後以非同步方式將資料複寫至次要區域。 切換提供高可用性以及最大持久性。 切換的設計目的是要在指定的一年內提供至少 99.99999999999999% （16個9）的物件持久性。 如需次要區域中資料的讀取權限，請啟用讀取權限異地區域-多餘儲存體（RA-切換）。 如需切換的詳細資訊，請參閱[高可用性和最大持久性（預覽）的異地區域冗余儲存體](../articles/storage/common/storage-redundancy-gzrs.md)。

如需可用性區域的詳細資訊，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。
