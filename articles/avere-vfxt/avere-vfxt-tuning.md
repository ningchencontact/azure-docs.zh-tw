---
title: Avere vFXT 叢集調整 - Azure
description: 在 Avere vFXT for Azure 中最佳化效能的自訂設定概觀
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 640c550e82c1b883970a3ea7a374a85989cd5e21
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669645"
---
# <a name="cluster-tuning"></a>叢集調整


大部分的 vFXT 叢集都可以受益於自訂的效能設定。 這些設定可協助叢集妥善搭配您特定的工作流程、資料集和工具使用。 

這項自訂工作應該與支援代表一起完成，因為它通常會牽涉到設定 Avere 控制項台中不提供的功能。

本節將說明一些可以完成的自訂調整。

<!-- 
[ xxx keep or not? \/ research this xxx ]

> [!TIP]
> The VDBench utility can be helfpul in generating I/O workloads to test a vFXT cluster. Read [Measuring vFXT Performance](vdbench.md) to learn more.

-->

## <a name="general-optimizations"></a>一般最佳化

根據資料集品質或工作流程樣式，可能會建議使用這些變更。

* 如果是大量寫入的工作負載，從其預設的 20% 增加寫入快取的大小。 
* 如果資料集涉及許多小檔案，請增加叢集快取的檔案計數限制。 
* 如果工作牽涉到複製或移動兩個存放庫之間的資料，調整用於移動資料之執行緒的數目： 
  * 若要增加速度，您可能要增加使用之平行執行緒的數目。
  * 如果後端儲存體磁碟區變成超載，您可能需要減少使用之平行執行緒的數目。
* 如果叢集快取使用 NFSv4 ACL 之核心篩選的資料，啟用存取模式快取可簡化特定用戶端的檔案授權。

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>雲端 NAS 或雲端閘道最佳化

若要在雲端 NAS 或閘道案例 (vFXT 叢集對雲端容器提供 NAS 樣式存取) 的 vFXT 叢集和雲端儲存體之間使用較高的資料速度，您的代表可能會建議變更諸如此類的這些設定，以便將資料從快取更積極地將推送到儲存體磁碟區：

* 增加叢集與儲存體容器之間 TCP 連線的數目
* 減少叢集與儲存體之間通訊的 REST 逾時值，以便在寫入未立即成功時，更快地重試寫入  
* 增加區段大小，讓每個後端寫入區段傳輸 8 MB 區塊的資料而不是 1 MB 的資料

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>雲端負載平衡或混合式 WAN 最佳化

在雲端負載平衡案例或混合式儲存體 WAN 最佳化案例 (其中 vFXT 叢集會在雲端和內部部署硬體儲存體之間提供整合) 中，這些變更可能很有幫助：

* 增加叢集與核心篩選之間允許之 TCP 連線的數目
* 針對遠端核心篩選啟用 WAN 最佳化設定 (此設定在不同的 Azure 區域中，可用於遠端內部部署篩選或雲端核心篩選)。
* 增加 TCP 通訊端緩衝區大小 (取決於工作負載和效能需求)
* 啟用「一律轉送」設定，減少重複快取的檔案 (取決於工作負載和效能需求)

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>協助最佳化 Avere vFXT for Azure

使用[取得有關系統的協助](avere-vfxt-open-ticket.md)中所述的程序，連絡支援人員了解這些最佳化相關問題。 