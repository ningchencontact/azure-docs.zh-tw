---
title: 啟用對 Avere vFXT 的支援 - Azure
description: 如何啟用支援從「適用於 Azure 的 Avere vFXT」上傳的功能
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669285"
---
# <a name="enable-support-uploads"></a>啟用支援上傳的功能

「適用於 Azure 的 Avere vFXT」可以自動上傳有關您叢集的支援資料。 這些上傳項目可讓支援人員提供可能的最佳服務。

## <a name="steps-to-enable-uploads"></a>啟用上傳的步驟

請依照這些來自「Avere 控制台」的步驟來啟用支援。 (請參閱[存取 vFXT 叢集](avere-vfxt-cluster-gui.md)，以了解如何開啟「Avere 控制台」)。

1. 瀏覽至頂端的 [Settings] \(設定\) 索引標籤。
1. 按一下左側的 [Support] \(支援\) 連結，然後接受隱私權原則。

   ![確認接受隱私權原則的螢幕擷取畫面](media/avere-vfxt-privacy-policy.png)
1. 按一下 [Customer Info] \(客戶資訊\) 左側的三角形以展開該區段。
1. 在 [Unique Cluster Name] \(唯一叢集名稱\) 中設定叢集的支援名稱 - 請確定它可讓支援人員唯一識別您的叢集。
1. 勾選 [Statistics Monitoring] \(統計資料監視\)、[General Information Upload] \(一般資訊上傳\) 及 [Crash Information Upload] \(當機資訊上傳\) 方塊。
1. 按一下 [Validate upload information] \(驗證上傳資訊\) 按鈕。
1. 按一下 [提交] 。
1. 按一下 [Secure Proactive Support (SPS)] \(安全的主動式支援 (SPS)\) 左側的三角形以展開該區段。
1. 勾選 [Enable SPS Link] \(啟用 SPS 連結\) 方塊。
1. 按一下 [提交] 。

   ![包含啟用支援之所有步驟的螢幕擷取畫面](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>後續步驟

如果您需要將內部部署的儲存體系統新增到叢集，請依照[設定儲存體](avere-vfxt-add-storage.md)中的指示進行操作。 

如果您已做好開始將用戶端連結至叢集的準備，請參閱[掛接 Avere vFXT 叢集](avere-vfxt-mount-clients.md)。