---
title: 啟用對 Avere vFXT 的支援 - Azure
description: 如何啟用支援從「適用於 Azure 的 Avere vFXT」上傳的功能
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: b975f84106507da6adff11dc62441526773f5cab
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998140"
---
# <a name="enable-support-uploads"></a>啟用支援上傳的功能

「適用於 Azure 的 Avere vFXT」可以自動上傳有關您叢集的支援資料。 這些上傳項目可讓支援人員提供可能的最佳服務。

## <a name="steps-to-enable-uploads"></a>啟用上傳的步驟

請依照這些來自「Avere 控制台」的步驟來啟用支援。 (請參閱[存取 vFXT 叢集](avere-vfxt-cluster-gui.md)，以了解如何開啟「Avere 控制台」)。

1. 瀏覽至頂端的 [Settings] \(設定\) 索引標籤。
1. 按一下左側的 [Support] \(支援\) 連結，然後接受隱私權原則。

   ![顯示 Avere Control Panel 的螢幕擷取畫面，以及具有 [Confirm] \(確認\) 按鈕以接受隱私權原則的快顯視窗](media/avere-vfxt-privacy-policy.png)

1. 按一下 [Customer Info] \(客戶資訊\) 左側的三角形以展開該區段。
1. 按一下 [Revalidate upload information] \(重新驗證上傳資訊\) 按鈕。
1. 在 [Unique Cluster Name] \(唯一叢集名稱\) 中設定叢集的支援名稱 - 請確定它可讓支援人員唯一識別您的叢集。
1. 勾選 [Statistics Monitoring] \(統計資料監視\)、[General Information Upload] \(一般資訊上傳\) 及 [Crash Information Upload] \(當機資訊上傳\) 方塊。
1. 按一下 [提交] 。

   ![包含支援設定頁面上已完成的客戶資訊區段的螢幕擷取畫面](media/avere-vfxt-support-info.png)

1. 按一下 [Secure Proactive Support (SPS)] \(安全的主動式支援 (SPS)\) 左側的三角形以展開該區段。
1. 勾選 [Enable SPS Link] \(啟用 SPS 連結\) 方塊。
1. 按一下 [提交] 。

   ![包含支援設定頁面上已完成的安全主動式支援區段的螢幕擷取畫面](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>後續步驟

如果您需要將內部部署儲存體系統新增至叢集，或更新新建 Blob 容器中的預設加密金鑰，請依照[設定儲存體](avere-vfxt-add-storage.md)中的指示。 

如果您已做好開始將用戶端連結至叢集的準備，請參閱[掛接 Avere vFXT 叢集](avere-vfxt-mount-clients.md)。