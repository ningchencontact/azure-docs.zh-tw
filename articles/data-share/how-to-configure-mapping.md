---
title: 設定資料集對應
description: 設定資料集對應
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789237"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>如何在 Azure 資料共用的預覽版中設定的已接收的共用資料集對應

這篇文章說明如何設定接收到的共用，使用 Azure 資料共用預覽資料集的對應。 您會想要這樣做，如果您接受資料的共用邀請，但選擇了 「 接受和更新版本設定 」。 否則可能只是要變更為您的接收資料的目的地儲存體帳戶。 

## <a name="navigate-to-a-received-data-share"></a>瀏覽至已接收的資料共用

在共用 Azure 資料服務中，瀏覽至您接收到的共用，然後選取**詳細資料** 索引標籤。 

![資料集的對應](./media/dataset-mapping.png "資料集對應") 

核取您想要將指派目的地，然後按一下資料集旁邊的方塊 **+ 對應至目標**。 您可能需要取消對應第一，如果您已經有設定目的地儲存體帳戶，而且您想要變更對應至不同的儲存體帳戶。 

![對應至目標](./media/dataset-map-target.png "對應至目標") 

## <a name="select-a-new-storage-account"></a>選取新的儲存體帳戶 

選取您想要置入資料的儲存體帳戶。 請注意，在任何已存在的任何資料之前已將對應儲存體帳戶將不會自動移至新的儲存體帳戶。

![目標儲存體帳戶](./media/map-target.png "目標儲存體") 

## <a name="next-steps"></a>後續步驟

若要了解如何開始分享資料，繼續進行[共用您的資料](share-your-data.md)教學課程。



