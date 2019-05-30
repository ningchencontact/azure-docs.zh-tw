---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238339"
---
| Resource | 預設限制 | 上限 |
| --- | --- | --- |
| 每一個雲端服務的[虛擬機器](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)數量 <sup>1</sup> |50 |50 |
| 每一雲端服務的輸入端點 <sup>2</sup> |150 |150 |

<sup>1</sup>使用傳統部署模型而不是 Azure Resource Manager 建立的虛擬機器會自動儲存在雲端服務。 您可以將更多虛擬機器加入至該雲端服務以獲得負載平衡和可用性。 

<sup>2</sup> 輸入端點即可從虛擬機器的雲端服務外部與某個虛擬機器進行通訊。 在相同雲端服務或虛擬網路中的虛擬機器可自動彼此通訊。 如需詳細資訊，請參閱[如何設定虛擬機器的端點](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
