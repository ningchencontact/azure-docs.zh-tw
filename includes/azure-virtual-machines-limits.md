---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279496"
---
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每一個雲端服務的[虛擬機器](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)數量 <sup>1</sup> |50 |50 |
| 每一雲端服務的輸入端點 <sup>2</sup> |150 |150 |

<sup>1</sup> 在服務管理 (而不是資源管理員) 中建立的虛擬機器會自動儲存在雲端服務中。 您可以將更多虛擬機器加入至該雲端服務以獲得負載平衡和可用性。 

<sup>2</sup> 輸入端點即可從虛擬機器的雲端服務外部與某個虛擬機器進行通訊。 在相同雲端服務或虛擬網路中的虛擬機器可自動彼此通訊。 請參閱[如何設定虛擬機器的端點](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
