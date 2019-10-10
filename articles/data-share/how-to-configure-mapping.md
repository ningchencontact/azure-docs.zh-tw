---
title: 在 Azure 資料共用預覽中設定資料集對應
description: 瞭解如何使用 Azure 資料共用預覽來設定已接收共用的資料集對應。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169142"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>如何在 Azure 資料共用預覽中為已接收的共用設定資料集對應

本文說明如何使用 Azure 資料共用預覽來設定已接收共用的資料集對應。 如果您接受資料共用邀請，但選擇「接受並設定之後」，您會想要這麼做。 否則，您可能只想要針對已接收的資料變更目的地儲存體帳戶。 

## <a name="navigate-to-a-received-data-share"></a>流覽至接收的資料共用

在 Azure 資料共用服務中，流覽至您收到的共用，然後選取 [**詳細資料**] 索引標籤。 

![資料集對應](./media/dataset-mapping.png "資料集對應") 

核取您想要指派目的地的資料集旁的方塊，然後按一下 [ **+ 對應至目標**]。 如果您已設定目的地儲存體帳戶，而且想要將對應變更為不同的儲存體帳戶，您可能需要先取消對應。 

![對應]至目標(./media/dataset-map-target.png "的")對應 

## <a name="select-a-new-storage-account"></a>選取新的儲存體帳戶 

選取您想要將資料放入其中的儲存體帳戶。 請注意，任何先前對應之儲存體帳戶中現有的資料，都不會自動移至新的儲存體帳戶。

![目標儲存體帳戶](./media/map-target.png "目標儲存體") 

## <a name="next-steps"></a>後續步驟

若要了解如何開始共用資料，請繼續進行[共用資料](share-your-data.md)教學課程。



