---
title: 使用 Azure 實驗室服務在共用的映像庫 |Microsoft Docs
description: 了解如何設定實驗室帳戶來使用共用的映像資源庫，以便使用者可以與其他共用映像，並在另一位使用者可以在實驗室中建立範本 VM 使用映像。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412860"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>使用 Azure 實驗室服務在共用的映像庫
本文說明如何老師/實驗室系統管理員可以在其中儲存範本的虛擬機器映像，才能由其他人重複使用。 這些映像會儲存在 Azure 中[共用的映像庫](../../virtual-machines/windows/shared-image-galleries.md)。 第一個步驟中，為實驗室系統管理員會將現有的共用的映像庫附加至實驗室帳戶。 一旦附加共用映像庫時，實驗室的實驗室帳戶中建立可以儲存共用的映像庫映像。 其他老師可以選取此映像來建立它們的類別範本的共用的映像庫。 

## <a name="prerequisites"></a>必要條件
- 所建立的共用映像資源庫[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或是[Azure CLI](../../virtual-machines/linux/shared-images.md)。
- 您在實驗室帳戶中附加的共用的映像庫。 如需逐步指示，請參閱 <<c0> [ 如何附加或卸離共用映像庫](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>若要共用的映像庫中儲存的映像
連結共用的映像資源庫之後，老師可以儲存，或使它可以由其他老師重複使用，將映像上傳至共用的映像庫。 將影像上傳至共用的映像資源庫的指示，請參閱[共用映像庫概觀](../../virtual-machines/windows/shared-images.md)。 

> [!NOTE]
> 目前，教室實驗室使用者介面 (UI) 不支援將實驗室映像儲存到共用的映像庫。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用 共用映像庫中的 映像
老師/教授可以挑選範本共用的映像庫中可用的自訂映像，在新的實驗室建立期間。

![從資源庫中使用虛擬機器映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
如需共用映像資源庫的詳細資訊，請參閱[共用的映像庫](../../virtual-machines/windows/shared-image-galleries.md)。
