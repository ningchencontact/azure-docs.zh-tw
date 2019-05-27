---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b75e24e5d59206ee8330c3ca9eaf86eacbad13d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114824"
---
### <a name="premium-performance-block-blob-storage"></a>Premium 效能區塊 blob 儲存體

使用較小，kb 的範圍，物件的應用程式最適合的 premium 效能區塊 blob 儲存體帳戶。 它很適合用於需要高交易率或一致的低延遲儲存體應用程式。 Premium 效能區塊 blob 儲存體被設計來調整您的應用程式。 如果您打算部署需要數十萬的每秒的要求或以 pb 計的儲存體容量的應用程式，請與我們連絡來提交支援要求中的[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="premium-performance-filestorage-preview"></a>進階效能 FileStorage （預覽）

進階檔案會使用名為唯一的儲存體帳戶**FileStorage （預覽）**。 此帳戶類型被設計用於具有高 IOPS 工作負載，以一致的低延遲高輸送量。 進階檔案儲存體的延展性與佈建的共用大小。

|領域  |目標  |
|---------|---------|
|最大可佈建大小     |100 TiB     |
|共用   |無限  |
|IOPS     |100,000    |
|輸入|4,136 MiB/s     |
|輸出|6,204 MiB/s |

 對於進階檔案共用中的擴展目標，請參閱 <<c0> [ 進階檔案調整目標](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets)一節。

### <a name="premium-performance-page-blob-storage"></a>Premium 效能分頁 blob 儲存體

進階效能、 一般用途 v1 或 v2 儲存體帳戶有下列延展性目標：

| 總帳戶容量                            | 本地備援儲存體帳戶總頻寬                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 磁碟容量：35 TB <br>快照容量：10 TB | 每秒最多 50 GB (輸入 <sup>1</sup> + 輸出 <sup>2</sup> |

<sup>1</sup> 傳送至儲存體帳戶的所有資料 (要求)

<sup>2</sup> 從儲存體帳戶接收的所有資料 (回應)

如果您針對非受控磁碟使用進階效能儲存體帳戶，而且您的應用程式超過單一儲存體帳戶的延展性目標，您可能想要移轉至受控磁碟。 如果您不想要移轉至受控磁碟，請將應用程式建置為使用多個儲存體帳戶。 然後將資料分散到那些儲存體帳戶。 例如，如果您想要將 51 TB 的磁碟連結至多個 VM，可將其分散到兩個儲存體帳戶。 單一進階儲存體帳戶的限制是 35 TB。 請確定單一的進階效能儲存體帳戶永遠不會有超過 35 TB 的佈建磁碟。