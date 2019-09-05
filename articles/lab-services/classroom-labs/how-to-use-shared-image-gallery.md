---
title: 使用 Azure 實驗室服務中的共用映射資源庫 |Microsoft Docs
description: 瞭解如何設定實驗室帳戶以使用共用映射資源庫，讓使用者可以與其他人員共用映射，而其他使用者可以使用該映射在實驗室中建立範本 VM。
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 5ce0a81f6c9c886fcbe9186dd7363c38170ca580
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382989"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>使用 Azure 實驗室服務中的共用映射資源庫
本文說明老師/lab 管理員如何儲存範本虛擬機器映射，供其他人重複使用。 這些映射會儲存在 Azure[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)中。 第一個步驟是，實驗室系統管理員將現有的共用映射資源庫附加至實驗室帳戶。 連結共用映射資源庫之後，在實驗室帳戶中建立的實驗室可以將影像儲存到共用映射資源庫。 其他老師可以從共用映射庫中選取此映射，以建立其類別的範本。 

## <a name="prerequisites"></a>必要條件
- 使用[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)建立共用映射資源庫。
- 您已將共用映射資源庫連接至實驗室帳戶。 如需逐步指示，請參閱[如何附加或卸離共用映射資源庫](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>將影像儲存到共用映射資源庫
連結共用映射庫之後，實驗室帳戶管理員或老師可以將影像儲存到共用映射資源庫，讓其他老師可以重複使用該映射。 

1. 在實驗室的首頁上，選取 [**範本**] 區段中磚上的 [**儲存影像**]。

    ![[儲存影像] 按鈕](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  在 [**儲存虛擬機器映射**] 視窗中，輸入影像的名稱，然後選取 [**儲存**]。 

    ![儲存虛擬機器映射視窗](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. 檢查 [實驗室] 磚上的 [狀態]。 

    ![儲存映射作業的狀態](../media/how-to-use-shared-image-gallery/save-image-status.png)

 您也可以將影像上傳至實驗室內容外的共用映射資源庫。 如需詳細資訊，請參閱[共用映射資源庫總覽](../../virtual-machines/windows/shared-images.md)。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共用映射庫中的映射
老師/教授可以在新的實驗室建立期間，為範本選擇可在共用映射資源庫中使用的自訂映射。

![從資源庫使用虛擬機器映射](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>後續步驟
如需共用映射資源庫的詳細資訊，請參閱[共用映射資源庫](../../virtual-machines/windows/shared-image-galleries.md)。
