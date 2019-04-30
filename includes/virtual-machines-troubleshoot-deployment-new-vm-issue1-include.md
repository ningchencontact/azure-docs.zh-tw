---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123821"
---
## <a name="issue-custom-image-provisioning-errors"></a>問題：自訂映像;佈建錯誤
如果您上傳或擷取一般化的 VM 映像做為特殊化 VM 的映像，會發生佈建錯誤，反之亦然。 前者會導致佈建逾時錯誤，後者將導致佈建失敗。 若要毫無錯誤地部署自訂映像，必須確定擷取過程中映像類型不會變更。

下表列出一般化和特殊化映像的可能組合、您會遇到的錯誤類型、以及要如何修正錯誤。

<!--Update_Description: wording update, update link-->