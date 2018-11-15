---
title: 關於 Avere vFXT for Azure 的其他連結
description: 關於 Avere vFXT for Azure 的其他資訊的連結
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 65e764d843f9e87adee4cf94c1d22b02db80eda0
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958827"
---
# <a name="additional-documentation"></a>其他文件

本文提供關於 Avere 控制台管理介面及相關主題之其他文件的連結。 

## <a name="avere-cluster-documentation"></a>Avere 叢集文件

其他 Avere 叢集文件可以在下列網站上找到：<http://library.averesystems.com/>。 這些文件可以協助您了解叢集的功能，以及如何進行其設定。 

* [FXT 叢集建立指南](<http://library.averesystems.com/#fxt_cluster>)是針對由實體硬體節點所組成的叢集而設計，但文件中的某些資訊也與 vFXT 叢集相關。 特別是，新的 vFXT 叢集系統管理員可以從閱讀以下幾節受益：
  * [自訂支援和監視設定](<http://library.averesystems.com/create_cluster/4_8/html/config_support.html#config-support>)說明如何自訂支援上傳設定，並啟用遠端監視。 
  * [設定 VServers 和全域命名空間](<http://library.averesystems.com/create_cluster/4_8/html/config_vserver.html#config-vserver>)提供建立用戶端面向命名空間的相關資訊。
  * [設定 Avere 叢集的 DNS](<http://library.averesystems.com/create_cluster/4_8/html/config_network.html#dns-overview>) 說明如何設定循環配置資源 DNS。
  * [新增後端儲存體](<http://library.averesystems.com/create_cluster/4_8/html/config_core_filer.html#add-core-filer>)記載如何新增核心篩選。

* [叢集設定指南](<http://library.averesystems.com/#operations>)是 Avere 叢集設定和選項的完整參考。 vFXT 叢集會使用這些選項的子集，但大部分相同的設定頁面都適用。

* [儀表板指南](<http://library.averesystems.com/#operations>)說明如何使用 Avere 控制台的叢集監視功能。

## <a name="vfxt-creation-and-management-documentation"></a>vFXT 建立和管理文件

使用 vfxt.py (雲端叢集建立與管理公用程式) 的完整指南可在 GitHub 上找到：[使用 vfxt.py 進行雲端叢集管理](https://github.com/Azure/AvereSDK/blob/master/docs/README.md) \(英文\)。  
