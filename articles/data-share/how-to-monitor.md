---
title: 如何監視 Azure 資料共用預覽
description: 如何監視 Azure 資料共用預覽
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789094"
---
# <a name="monitor-azure-data-share-preview"></a>監視 Azure 的資料共用預覽 

這篇文章說明如何監視您使用 Azure 資料共用預覽的資料共用。 做為資料提供者，就可以監視您的資料共用的關聯性的各種層面。 詳細資料，例如是否您資料的取用者已接受您的資料共用邀請，以及其是否已建立的共用訂用帳戶並開始使用您的資料都可供監視。 

為資料取用者，您可以監視已觸發的快照集到您的 Azure 訂用帳戶。 

## <a name="monitor-invitation-status"></a>監視邀請狀態

瀏覽至 寄件者共用您的資料共用邀請的狀態檢視-> 邀請。 

![邀請狀態](./media/invitation-status.png "邀請狀態") 

有三種您邀請可以處於的狀態：

* 暫止的資料共用收件者尚未接受邀請。
* 已接受-資料共用收件者已接受邀請。
* 已拒絕-資料共用收件者已拒絕邀請。

> [!IMPORTANT]
> 如果已接受之後，您就會刪除邀請，它就不相當於撤銷存取權。 如果您想要停止未來的快照集複製到您的資料取用者儲存體帳戶，您必須撤銷存取權，透過*共用訂用帳戶* 索引標籤。 

## <a name="monitor-share-subscriptions"></a>監視共用訂用帳戶

瀏覽至傳送共用共用訂用帳戶的狀態檢視]-> [共用的訂用帳戶。 這可讓您在接受邀請之後，由您的資料取用者的作用中訂用帳戶的詳細資料。 您可以向資料取用者停止未來的更新，選取 共用的訂用帳戶，然後選取*撤銷*。 

## <a name="snapshot-history"></a>快照集記錄 

在 歷程記錄 索引標籤中，就可以檢視已在 複製到資料取用者的租用戶的快照集。 您可以監視的頻率和每個快照集間隔的持續時間。 

![快照集記錄](./media/sent-shares.png "歷程記錄的快照集") 

您可以檢視上執行的開始日期 即可執行每個快照集的更多詳細。 

## <a name="next-steps"></a>後續步驟 

深入了解[Azure 的資料共用術語](terminology.md)