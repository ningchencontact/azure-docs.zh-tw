---
title: 如何監視 Azure 資料共用預覽
description: 如何監視 Azure 資料共用預覽
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fbc6847dd86b7687d477b2bae0deab1389dc8491
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827071"
---
# <a name="monitor-azure-data-share-preview"></a>監視 Azure 資料共用預覽 

本文說明如何使用 Azure 資料共用預覽來監視您的資料共用。 身為數據提供者，您可以監視資料共用關聯性的各個層面。 詳細資訊，例如您的資料取用者是否已接受您對資料共用的邀請，以及他們是否已建立共用訂閱並開始使用您的資料，都可供監視。 

身為數據取用者，您可以監視已觸發至您的 Azure 訂用帳戶的快照集。 

## <a name="monitor-invitation-status"></a>監視邀請狀態

流覽至 [已傳送的共用-> 邀請]，以查看資料共用邀請的狀態。 

![邀請狀態](./media/invitation-status.png "邀請狀態") 

您的邀請可以有三種狀態：

* 待決-資料共用收件者尚未接受邀請。
* 已接受-資料共用收件者已接受邀請。
* 已拒絕-資料共用收件者已拒絕邀請。

> [!IMPORTANT]
> 如果您在已接受邀請之後將其刪除，則不等於撤銷存取權。 如果您想要停止將未來的快照集複製到資料取用者儲存體帳戶，您必須透過 [*共用訂閱*] 索引標籤撤銷存取權。 

## <a name="monitor-share-subscriptions"></a>監視共用訂閱

流覽至 已傳送的共用-> 共用訂閱，以查看共用訂閱的狀態。 這會提供您在接受邀請之後，您的資料取用者所建立之作用中訂閱的詳細資料。 您可以選取共用訂用帳戶，然後選取 *[撤銷]* ，以停止資料取用者的未來更新。 

## <a name="snapshot-history"></a>快照集歷程記錄 

在 [歷程記錄] 索引標籤中，您可以查看已複製到資料取用者的租使用者中的快照集。 您可以監視每個快照間隔的頻率和持續時間。 

![快照集記錄](./media/sent-shares.png "快照集記錄") 

按一下 [執行] 開始日期，即可查看每個快照集執行的更多詳細資料。 

根據預設，最多會顯示30天的快照集歷程記錄。 如果您需要查看超過30天的歷程記錄，請流覽至 [監視]-> [診斷設定]，然後選取 [**新增診斷設定**]。 您必須選取要用來儲存這些記錄的儲存體帳戶。 

![快照集記錄](./media/diagnostic-settings.png "診斷設定") 

## <a name="next-steps"></a>後續步驟 

深入瞭解[Azure 資料共用術語](terminology.md)